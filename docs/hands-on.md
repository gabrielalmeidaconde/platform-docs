# Hands-on

Os hands-on da disciplina foram aplicados **direto no projeto do grupo**, em vez de num repositório de
estudos separado: cada um virou uma parte da plataforma. A tabela mostra onde cada hands-on está e quem
fez essa parte.

| Hands-on | Tema | Onde está no projeto | Quem |
|---|---|---|---|
| 0 | Ambiente, repositório raiz e MkDocs | [`pna26/platform`](https://github.com/pna26/platform){target="_blank"} com os serviços como submódulos e a [documentação](https://pna26.github.io/platform/){target="_blank"} | Leonardo (eu: as páginas do Exchange, testes de carga, observabilidade e custos) |
| 1 | Account (controller, repository, container) | [`account`](https://github.com/pna26/account){target="_blank"} + [`account-service`](https://github.com/pna26/account-service){target="_blank"} | Nicolas |
| 2 | Gateway | [`gateway-service`](https://github.com/pna26/gateway-service){target="_blank"}; a rota `/exchange/**` leva ao meu serviço | Leonardo |
| 3 | Segurança (JWT, rotas abertas, autorização) | filtro de autorização do gateway e emissão do JWT no `auth-service`; o Exchange recusa (`401`) quem não passou pelo gateway | Leonardo e Nicolas |
| 4 | CI/CD (Jenkins) | feito com **GitHub Actions**, que o curso aceita no lugar do Jenkins; o `exchange-service` usa o pipeline reutilizável do grupo (pytest, Trivy, imagem) | Leonardo (eu: o CI do Exchange) |
| 5 | Kubernetes | manifestos de cada serviço e do `platform`; cluster EKS | Leonardo e Nicolas (eu: os manifestos do Exchange, Prometheus e Grafana) |
| 6 | Observabilidade (Prometheus + Grafana) | [Observabilidade](bottlenecks.md#observabilidade) | **eu** |
| 7 | Balanceamento de carga (Nginx) | [Balanceamento](https://pna26.github.io/platform/bottlenecks/load-balancing/){target="_blank"}; o teste com HPA mostrou o ajuste de `keepalive_time` | Leonardo |
| 8 | Cache distribuído (Redis) | [Cache do token](https://pna26.github.io/platform/bottlenecks/cache/){target="_blank"}; o cache da cotação é o mesmo problema, resolvido em memória ([Cache da cotação](bottlenecks.md#cache-da-cotacao)) | Leonardo (eu: o cache da cotação) |
| 9 | Replicação e failover do banco | não aplicado no projeto (no EKS o banco é o RDS, gerenciado pela AWS; o impacto do single-AZ no SLA está em [PaaS, custos e SLA](projeto.md#paas-custos-e-sla)) | — |
| 10 | Mensageria (Kafka) | [Mensageria](https://pna26.github.io/platform/bottlenecks/messaging/){target="_blank"} | Nicolas |
| 11 | Rastreamento distribuído (traces) | não aplicado no projeto | — |
| 12 | IA no CI/CD | não aplicado no projeto (a IA foi usada no desenvolvimento, ver [Uso de IA](uso-de-ia.md)) | — |

Além dos hands-on, fiz os **testes de carga** com k6 e a análise de **PaaS, custos e SLA**, que são
entregas do projeto: ver [Projeto](projeto.md).
