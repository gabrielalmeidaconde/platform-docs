# Uso de IA

A disciplina permite usar IA na implementação desde que o uso seja documentado e que cada integrante
saiba explicar e defender todas as partes. O registro completo do grupo, por integrante, está em
[Uso de IA](https://pna26.github.io/platform/uso-de-ia/){target="_blank"}; aqui está o resumo da minha parte.

**Ferramenta:** Claude Code (Anthropic), no VS Code, com acesso aos repositórios locais.

**Como usei:** pedi para a IA olhar o projeto, achar as tarefas que eram minhas no
[quadro](https://github.com/orgs/pna26/projects/1){target="_blank"} e implementá-las com um commit por
tarefa, seguindo os padrões que o grupo já usava (pipeline reutilizável, manifestos k8s, estilo da
documentação). A IA gerou o código, rodou os testes e a plataforma na minha máquina, e eu aprovei cada
ação externa (repositório, PRs, publicação).

| Parte | Com IA? | Como foi verificado |
|---|---|---|
| Exchange (`pna26/exchange-service`) | Sim: código, testes, Dockerfile, k8s e CI | 21 testes; testado contra a AwesomeAPI real e de ponta a ponta pelo gateway (smoke test) |
| Cache da cotação | Sim | medido no teste de carga (p95 49 → 4 ms) |
| Observabilidade (Prometheus + Grafana) | Sim | compose local, ensaio em kind e o Grafana do EKS durante o teste com HPA (19/11) |
| Testes de carga (k6) | Scripts sim; **números não** | os números são os que saíram das rodadas na minha máquina; a IA executou e escreveu a análise a partir do k6, do Prometheus e dos logs |
| Teste com HPA (ensaio em kind e EKS, vídeo e prints) | Sim: scripts, execução e análise | ensaio em kind em 12/11; no EKS (19/11), o gravador foi rodado por quem tinha `kubectl` no cluster e a IA escreveu a análise |
| PaaS, custos e SLA | Sim: texto e contas | preços conferidos nas páginas da AWS em 05/11/2026 |
| Esta documentação individual | Sim | `mkdocs build --strict`; mesmo formato das dos colegas |

**O que eu fiz sem IA:**

- **Direção do trabalho:** escolhi as tarefas, exigi um commit por tarefa e aprovei cada ação externa:
  criação do repositório `pna26/exchange-service`, os PRs por fork, a atualização do quadro e as
  instalações com permissão de administrador.
- **Ambiente:** login no GitHub CLI e na AWS (SSO), ativação da virtualização/WSL e reinicialização para
  o Docker funcionar.

**Onde a IA errou e foi corrigido:**

- O perfil `smoke` do k6 não rodava nenhuma iteração (subia de 0 a 1 usuário nos próprios 20 s): virou
  1 usuário fixo.
- O cadastro e o login das contas de teste entravam nas estatísticas: separados com a tag `phase`.
- O experimento de réplicas não achou ponto de quebra (a pausa entre ações limitava a carga): foi criado
  o experimento de saturação, que achou o gargalo real no Auth.
- A IA editou o `load-matrix.sh` enquanto ele rodava, o que corrompeu uma rodada: o experimento foi
  rodado de novo.
- No ensaio do HPA, os primeiros prints do Grafana mostravam réplicas que sobraram de uma rodada
  anterior; o gravador passou a esperar o Prometheus ver só as 2 réplicas antes de começar.
