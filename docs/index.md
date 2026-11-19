# Gabriel · Platform 2026.2

???+ info inline end "Disciplina"

    Plataformas, Microsserviços, DevOps e APIs
    Insper · 2026.2

**Aluno:** Gabriel Almeida Conde ([@gabrielalmeidaconde](https://github.com/gabrielalmeidaconde){target="_blank"})

**Grupo:** pna26

1. Leonardo de Souza Lima e Silva ([@souzxxx](https://github.com/souzxxx){target="_blank"})
1. Nicolas Volf Martins da Silva ([@NicolasVolf](https://github.com/NicolasVolf){target="_blank"})
1. Gabriel Almeida Conde (eu)

Esta é a minha documentação individual. O projeto foi feito em grupo; aqui estão as partes que eu
implementei, os meus dois bottlenecks, os hands-on e os links de tudo o que foi usado.

**Documentação completa do grupo:** [pna26.github.io/platform](https://pna26.github.io/platform/){target="_blank"}

## Vídeo de apresentação (1–2 min)

!!! warning "Em gravação"

    O vídeo do grupo é gravado com a plataforma no EKS (roteiro em
    [Apresentação](https://pna26.github.io/platform/apresentacao/){target="_blank"}) e entra aqui
    assim que estiver pronto.

<!-- vídeo: <iframe width="100%" height="470" src="https://www.youtube.com/embed/<id>" allowfullscreen></iframe> -->

O vídeo do **teste de carga com o HPA escalando** o gateway, ensaiado num cluster igual ao do EKS, está
em [Projeto → Teste com HPA](projeto.md#teste-com-hpa).

## Entregas

- [x] Exchange (5%): [Projeto → Exchange](projeto.md#exchange)
- [x] Testes de carga (15%): bateria no compose e ensaio com HPA, em [Projeto → Testes de carga](projeto.md#testes-de-carga)
- [x] PaaS, custos e SLA (10%): [Projeto → PaaS, custos e SLA](projeto.md#paas-custos-e-sla)
- [x] Bottleneck 1: cache da cotação, em [Meus bottlenecks](bottlenecks.md#cache-da-cotacao)
- [x] Bottleneck 2: observabilidade (Prometheus + Grafana), em [Meus bottlenecks](bottlenecks.md#observabilidade)
- [x] Hands-on: [Hands-on](hands-on.md)
- [x] Uso de IA documentado: [Uso de IA](uso-de-ia.md)
- [ ] Teste com HPA no EKS (na próxima subida do cluster)
- [ ] Vídeo do grupo (1–2 min)

## O que eu fiz

| Entrega | O quê | Detalhes |
|---|---|---|
| Microsserviço **Exchange** | Câmbio em Python (FastAPI): cotação de um par de moedas para o usuário logado, com cache | [Projeto](projeto.md#exchange) |
| **Testes de carga** (k6) | Cenários e perfis de carga, bateria de experimentos no compose e o teste com HPA | [Projeto](projeto.md#testes-de-carga) |
| **PaaS, custos e SLA** | Custo do EKS por hora e por mês, comparação com PaaS, SLA composto e SLO | [Projeto](projeto.md#paas-custos-e-sla) |
| Bottleneck: **cache da cotação** | Cotação guardada por 30 s, com lock contra *stampede* e *stale-if-error* | [Meus bottlenecks](bottlenecks.md#cache-da-cotacao) |
| Bottleneck: **observabilidade** | Prometheus descobrindo as réplicas sozinho, Grafana com o dashboard versionado e o k6 mandando métricas | [Meus bottlenecks](bottlenecks.md#observabilidade) |

## Repositórios usados

| Repositório | O que é | Responsável |
|---|---|---|
| [pna26/platform](https://github.com/pna26/platform){target="_blank"} | Repositório raiz: documentação, `compose.yaml`, Nginx, manifestos k8s, pipeline reutilizável e os serviços como submódulos | Leonardo (meus: teste de carga, observabilidade, custos e as páginas deles) |
| [pna26/exchange-service](https://github.com/pna26/exchange-service){target="_blank"} | Câmbio (Python/FastAPI) | **eu** |
| [pna26/gateway-service](https://github.com/pna26/gateway-service){target="_blank"} | API gateway (Spring Cloud Gateway) | Leonardo |
| [pna26/auth](https://github.com/pna26/auth){target="_blank"} | Interface (Feign) e DTOs do Auth | Nicolas |
| [pna26/auth-service](https://github.com/pna26/auth-service){target="_blank"} | Implementação do Auth (JWT) | Nicolas |
| [pna26/account](https://github.com/pna26/account){target="_blank"} | Interface (Feign) e DTOs do Account | Nicolas |
| [pna26/account-service](https://github.com/pna26/account-service){target="_blank"} | Implementação do Account (PostgreSQL + Flyway) | Nicolas |
| [pna26/notification-service](https://github.com/pna26/notification-service){target="_blank"} | Consumidor Kafka: e-mail de boas-vindas | Nicolas |
| [gabrielalmeidaconde/platform-docs](https://github.com/gabrielalmeidaconde/platform-docs){target="_blank"} | Esta documentação individual | **eu** |

Quadro de tarefas do grupo: [Projeto Platform 2026.2](https://github.com/orgs/pna26/projects/1){target="_blank"}.
