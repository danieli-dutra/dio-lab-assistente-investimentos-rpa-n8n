# Workflow público — preparação da entrega

Importe `workflow.json` pelo menu **Import from File** do editor do n8n. O
workflow permanece inativo (`active: false`). Use uma instalação que ofereça os
tipos e versões dos nós registrados no JSON, incluindo o nó Google Gemini
`@n8n/n8n-nodes-langchain.googleGemini`, versão 1.2.

No nó **Message a model**, crie ou selecione uma credencial Google Gemini
(tipo interno `googlePalmApi`, exigido pelo próprio tipo do nó). O JSON público
não contém a propriedade `credentials` nesse nó. Após importar, abra **Message
a model**, localize o seletor de credencial Google Gemini e selecione uma
credencial existente ou crie uma nova. Cadastre a chave exclusivamente no
gerenciador de credenciais do n8n e salve o nó/workflow antes de executar ou
ativar o fluxo. O modelo preservado é `models/gemini-2.5-flash`.

O nó **Limit** permanece temporariamente com `maxItems: 2`: processa no máximo
dois clientes por execução como restrição de teste relacionada à cota gratuita
do Gemini. Esse limite não garante disponibilidade de cota. O nó **Wait** e
todos os demais parâmetros foram preservados. Qualquer ampliação do limite
deve ser uma alteração posterior, explícita e revisada.

O caminho do Webhook continua `dio-investimentos` (POST). Após importar,
obtenha a URL na instalação de destino e configure o emissor RPA para essa URL.
Os `webhookId` locais foram removidos; a instalação de destino deve gerar os
identificadores necessários ao salvar/importar. Confira os nós Webhook e Wait
na revisão da importação antes de ativar o fluxo.

## Relatório de auditoria — 24/10/2026

O arquivo exportado já existia, mas ainda não era rastreado pelo Git. A revisão
foi realizada na branch `chore/preparar-workflow-publicacao`, sem commit.

| Alteração | Justificativa |
| --- | --- |
| Removido `id` na raiz | Identidade do workflow na instalação de origem. |
| Removido `versionId` | Identidade da revisão local. |
| Removido `meta.instanceId` | Identificador da instalação de origem. |
| Removido `meta.templateCredsSetupCompleted` | Estado local de configuração; a credencial precisa ser configurada no destino. |
| Removidos os dois `webhookId` | Identificadores locais de Webhook e Wait; preservados caminho, métodos e parâmetros. |
| Removida a propriedade `credentials` de Message a model | Evita referências locais e o vínculo inválido com `id: null`; a credencial será selecionada no destino. |

Os diffs de diagnóstico da preparação anterior não estão presentes no estado
atual do repositório. A tabela acima registra as alterações da sanitização;
este README é documentação e não faz parte da execução do workflow.

### Validação estática

- JSON decodificado em UTF-8 e analisado sem erros.
- O JSON atual contém 11 nós e 11 conexões, incluindo as duas saídas do loop.
  A cópia original não está disponível no repositório para nova comparação.
- Nomes e IDs dos nós únicos; todas as origens, destinos e referências
  `$('...')` apontam para nós existentes; índices de entrada válidos (0).
- `Limit.maxItems` continua 2 e o modelo é `models/gemini-2.5-flash`.
  A identidade com parâmetros do export original é um registro da revisão
  anterior, não uma comparação repetida nesta auditoria final.
- Nome do workflow, configurações de execução, estado inativo e demais campos
  fora da lista de sanitização preservados.
- Inspeção dos parâmetros e varredura textual sem indícios de chaves de API,
  tokens, senhas, chaves privadas ou e-mails literais no JSON. `email` aparece
  apenas como campo dos dados processados. `pinData` está vazio; não há dados
  de clientes fixados ou histórico de execução no arquivo.
- A única URL literal é a fonte pública do CSV da DIO, sem autenticação.

A estrutura é compatível com um export para importação pelo editor, sujeita
à disponibilidade dos tipos/versões dos nós e à configuração de credenciais
na instalação de destino. Não foi realizada importação real nem execução:
essa validação estática não comprova comportamento em runtime ou disponibilidade
do modelo. A revisão de segurança se limita ao workflow e aos artefatos desta
preparação; não certifica o notebook ou todo o histórico Git.

Nenhuma chamada ao Gemini, interação com Docker, alteração no workflow em
execução, commit, push ou abertura de PR foi realizada.

Referência: [documentação oficial de exportação/importação do n8n](https://docs.n8n.io/workflows/export-import/),
que orienta remover ou anonimizar nomes/IDs de credenciais e revisar headers
de autenticação antes de compartilhar exports.

## Correção de portabilidade da credencial

A versão sanitizada anterior declarava `googlePalmApi` com `id: null` e nome
genérico. Essa referência é a causa provável do erro `Node "Message a model"
uses invalid credential` em `CredentialsPermissionChecker.mapCredIdsToNodes`,
relatado após importar no n8n 2.44.6 Self Hosted. A referência declarada não
identifica uma credencial válida. A correção remove integralmente a propriedade
`credentials`; o tipo do nó continua definindo a credencial necessária.

Na revisão anterior, a comparação integral dos objetos JSON confirmou que a
única alteração foi remover essa propriedade de **Message a model**: os 11 nós,
as 11 conexões, códigos, prompts, parâmetros e `Limit.maxItems: 2` permaneceram
iguais. JSON válido; origens, destinos e referências por nome válidos.
O diff de diagnóstico dessa correção não está presente no estado atual.

Não houve reprodução em runtime nem nova importação no n8n. A validação desta
correção é estática; não confirma a resolução na instalação de destino. Nenhuma
chamada à API, interação com Docker, commit ou push foi realizada.

## Execução original e evidências

Conforme validação informada pelo autor, a execução original #17 terminou com
sucesso em 24/10/2026: dez clientes recebidos, nove opções de investimento,
dez mensagens estáticas preparadas e dois clientes processados pelo Gemini.
A integração usa diretamente **Message a model**, sem nó **AI Agent**.
Dois prints reais (canvas e JSON consolidado) estão incluídos e descritos em
[docs/evidencias](../docs/evidencias/README.md).
A importação da versão sanitizada ainda não foi integralmente validada.
