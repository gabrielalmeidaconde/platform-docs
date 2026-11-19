# Meus bottlenecks

Implementei dois dos bottlenecks do projeto. Cada um tem a página completa (problema, solução, código,
evidência) na documentação do grupo; aqui está o resumo com o número que mostra o efeito.

| Bottleneck | Número que mostra o efeito | Página completa |
|---|---|---|
| Cache da cotação | p95 da cotação de **49 → 4 ms** (−92 %); a API externa passa de ≈ 7 chamadas/s para no máximo 2 por minuto por par | [Cache da cotação](https://pna26.github.io/platform/bottlenecks/exchange-cache/){target="_blank"} |
| Observabilidade | explicou o teste de carga: 99,7 % de acerto do cache do token, os 0,57 % de erro vindos do Auth e as réplicas do HPA demorando ≈ 1,5 min para receber carga | [Observabilidade](https://pna26.github.io/platform/bottlenecks/observability/){target="_blank"} |

## Cache da cotação

**Problema:** toda consulta de câmbio dependia de uma API de terceiros, gratuita, **sem SLA** e com limite
de requisições. A latência do Exchange era a dela, e com mais usuários o limite seria atingido (o que
de fato aconteceu no EKS: a AwesomeAPI devolve `429` para o IP da AWS).

**Solução:** o Exchange guarda cada cotação em memória por **30 s**:

- **lock por par de moedas** contra *cache stampede*: se 100 requisições de `USD/BRL` chegam com o cache
  vazio, só a primeira chama a API, e as outras esperam e usam o resultado;
- **stale-if-error**: se a API externa cair, o Exchange devolve a última cotação conhecida (até 10 min)
  em vez de erro; só responde `503` sem nenhuma cotação recente;
- métrica `exchange_quote_cache_total{result}` (`hit`, `miss`, `stale`, `error`) para ver o cache
  funcionando no Grafana.

**Resultado** (teste de carga em 05/11/2026: 10 usuários, 30 s, pelo `lb` → gateway → Exchange, com a
API real):

| Cache | Requisições | p50 | p95 | p99 | Erros |
|---|---:|---:|---:|---:|---:|
| desligado (`TTL=0`) | 445 | 23 ms | 49 ms | 69 ms | 0 % |
| ligado (`TTL=30`) | 454 | **3 ms** | **4 ms** | 17 ms | 0 % |

O ganho maior não aparece na tabela: sem cache, a API externa recebia ≈ 7 chamadas por segundo de só 10
usuários; com cache, recebe no máximo 2 por minuto por par e por réplica, **qualquer que seja a carga**.

## Observabilidade

**Problema:** com várias réplicas de cada serviço, sem métricas não dá para saber qual réplica está
sobrecarregada, se o balanceamento divide a carga, se um cache acerta, nem por que o teste de carga deu
erro: o k6 só vê a plataforma de fora.

**Solução:** **Prometheus** + **Grafana**, iguais no compose e no EKS:

- o Prometheus **descobre as réplicas sozinho**: pelo DNS do Docker no compose e pelo
  `kubernetes_sd_configs` no EKS, onde basta o pod ter as anotações `prometheus.io/*`; o acesso à API do
  Kubernetes é um `Role` só de leitura no namespace, não um `ClusterRole`;
- o Grafana sobe com o data source e o dashboard já provisionados; o dashboard é **um JSON no Git**,
  o mesmo arquivo no compose e no EKS (por `ConfigMap`);
- o **k6 manda as métricas dele para o Prometheus** (*remote write*), então a carga aplicada e a
  latência vista pelo cliente aparecem no mesmo dashboard que o servidor;
- nada fica público: `127.0.0.1` no compose, `ClusterIP` + `port-forward` no EKS, e as rotas de
  métricas bloqueadas no Nginx.

**O que a observabilidade mostrou** (perguntas que o k6 sozinho não respondia):

| Pergunta | Resposta no Prometheus/Grafana |
|---|---|
| O cache do token poupa mesmo o Auth? | **7.571 hits × 21 misses** (99,7 %) numa rodada com cache |
| Por que 4 réplicas do gateway renderam menos que 1 na saturação? | CPU da máquina em ≈ 83–85 %: as 4 JVMs disputavam os mesmos núcleos |
| De onde vieram os 0,57 % de erro? | `500` no gateway → log do Auth: `BindException` (Feign sem pool de conexões) |
| As réplicas que o HPA cria recebem carga logo? | **Não:** o painel por réplica mostrou ≈ 1,5 min até a carga se dividir (o Service balanceia conexões) |

Para o teste com HPA, o dashboard ganhou o painel **Réplicas no ar ao longo do tempo** e o painel de
erros passou a mostrar 0 % em vez de *No data* quando não há erro:

![Dashboard Store — plataforma durante o teste com HPA](https://pna26.github.io/platform/devops/hpa/kind-hpa-grafana.png)
