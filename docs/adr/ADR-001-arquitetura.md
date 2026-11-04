# ADR-001: Stack e arquitetura do Connexa

- **Status:** Aceito
- **Data:** 03/11/2026
- **Task:** AB#26
- **Equipe:** Bruno Barros Andrade, Fernando Fernandes Moraes da Silva,
  Guilherme Pompeo Verrone, Murilo Francisco Alvim, Renzo Heiki Vandini Fernandes

## Contexto

O Connexa ajuda estudantes a formar e manter grupos de estudo por disciplina
e horário. A Sprint 1 tem duas semanas, e o código será escrito por um agente
de IA (Copilot/Codex) a partir de Tasks especificadas pela equipe no Azure Boards.
A equipe precisa de uma stack que:

- todos os integrantes consigam ler e revisar com segurança;
- rode no Codespaces sem configuração extra (Node.js 24 e PostgreSQL já vêm prontos);
- tenha testes automatizados rápidos, já que o fluxo exige CI vermelho → verde;
- seja bem conhecida pelos agentes de IA, reduzindo código incorreto.

O professor ofereceu três modelos equivalentes: Java, .NET e Node.

## Decisão

Adotamos o modelo **connexa-modelo-node**, mantendo a arquitetura que ele já traz:

- **Backend:** Node.js 24 com TypeScript e Prisma (ORM), expondo uma API HTTP na porta 8080, com contrato
  documentado em OpenAPI (Swagger UI em `/swagger-ui`);
- **Frontend:** aplicação TypeScript com Vite na porta 5173, que repassa `/api` para o backend;
- **Banco de dados:** PostgreSQL, com o banco `connexa` para a aplicação e
  `connexa_teste` para os testes automatizados;
- **Verificação:** o script `./verificar` roda as verificações e os testes,
  e o mesmo processo é executado pelo CI do GitHub Actions;
- **Ambiente:** GitHub Codespaces, que já traz Node.js 24 e PostgreSQL, sem
  nada a instalar na máquina dos integrantes;
- **Autenticação:** simulada pelo template (usuário fixo). Identidade fica fora da Sprint 1.

Não alteramos a estrutura de pastas nem as dependências do modelo nesta sprint.

## Alternativas consideradas

- **connexa-modelo-java:** mais verboso e com build mais lento; poucos
  integrantes têm prática recente com Java.
- **connexa-modelo-dotnet:** robusto, mas exige mais configuração e tem
  menos familiaridade na equipe.

## Consequências

- **Positivas:** curva de aprendizado menor, revisões de PR mais seguras,
  testes rápidos e ampla base de exemplos para o agente de IA.
- **Negativas:** o ecossistema npm muda rápido e tem muitas dependências;
  por isso mantemos as versões fixadas pelo modelo e não atualizamos
  pacotes durante a sprint. Erros de lógica não são pegos pelo TypeScript,
  então cada cenário Gherkin deve ter um teste automatizado correspondente.
- **Revisão:** esta decisão pode ser revista em um novo ADR se a equipe
  encontrar limitações do modelo nas próximas sprints.
