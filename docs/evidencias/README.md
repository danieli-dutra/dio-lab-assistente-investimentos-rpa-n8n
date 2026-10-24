# Evidências da execução original

As duas capturas reais registram a execução original #17 de 24/10/2026.

- [Workflow executado com sucesso](workflow-execucao-sucesso.png): mostra o
  canvas com 11 nós, o status `Succeeded` da execução #17 e as contagens de
  nove investimentos, dez itens preparados antes do Limit e dois itens
  encaminhados à etapa Gemini.
- [Resultado consolidado do Gemini](resultado-gemini-json.png): mostra a saída
  do nó **Resultado Final - Mensagens IA**, com `total_clientes: 2`, mensagem
  original, mensagem reescrita, modelo `gemini-2.5-flash` e status `gerado`
  visíveis no primeiro registro. O início do segundo cliente também aparece;
  a captura não exibe todo o JSON sem rolagem.

A inspeção visual não identificou chaves de API, tokens ou credenciais.
As capturas exibem URL `localhost`, identificadores locais do workflow/execução
e nomes/e-mails da base fictícia do desafio.

O fluxo preparou dez mensagens estáticas; o limite intencional de dois clientes
se aplica à etapa de IA. As evidências se referem ao workflow original e não
validam integralmente a importação da versão sanitizada. A credencial Gemini
própria deve ser configurada pelo usuário na instalação de destino.
