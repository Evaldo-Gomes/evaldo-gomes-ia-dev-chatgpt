# Detalhamento das correções do Service Desk

## Escopo

As correções foram feitas na branch `Preprod`, sem integração com `main`.
Cada bug funcional recebeu um commit próprio; este relatório foi registrado
em um commit separado.

## Bugs identificados e corrigidos

1. **Busca não encontrava o solicitante** — embora a interface indique busca
   por solicitante, ID ou título, a filtragem só consultava ID e título.
   A busca agora também considera o nome do solicitante.
2. **Alterações do analista não eram persistidas** — status, prioridade,
   correção e histórico eram atualizados apenas em memória. A rotina de
   atualização agora salva o estado no `localStorage`.
3. **A ação “Limpar base local” restaurava os exemplos** — o botão removia e
   recriava os chamados de demonstração. Agora a lista é realmente esvaziada
   e o estado vazio permanece após recarregar.
4. **Novo chamado sem registros recebia ID inválido** — `Math.max` sobre uma
   lista vazia resultava em `-Infinity`. A geração agora usa `1000` como base,
   iniciando em `1001` quando não há chamados.
5. **Campos com apenas espaços eram aceitos** — a validação HTML `required`
   não rejeita espaços. Solicitante, título e descrição são verificados após
   remover espaços das extremidades antes de criar o chamado.

## Construção dos prompts para análise

Os prompts foram estruturados com objetivo, contexto observado, restrição de
alteração cirúrgica e uma condição verificável de aceite. Exemplos usados
como guia para cada investigação:

- **Busca:** “Verifique se o filtro do painel cobre todos os campos anunciados
  pela interface (ID, título e solicitante). Corrija apenas os campos usados
  na busca e valide que uma busca pelo nome retorna o chamado correto.”
- **Persistência:** “Analise o fluxo de salvar alterações do analista.
  Garanta que status, prioridade, correção e eventos sobrevivam a um
  recarregamento, reutilizando a persistência existente.”
- **Limpeza:** “Compare o resultado do botão ‘Limpar base local’ com o rótulo.
  Corrija a ação para deixar a coleção vazia e manter esse estado após
  recarregar, sem alterar a inicialização padrão de uma instalação nova.”
- **Identificadores:** “Verifique a geração de IDs quando a coleção está
  vazia. Preserve a sequência atual para listas preenchidas e gere um ID
  numérico válido no caso vazio.”
- **Validação:** “Revise os campos obrigatórios do formulário. Rejeite
  solicitante, título e descrição que contenham apenas espaços, sem remover
  as validações nativas restantes.”

## Versionamento e validação

Commits funcionais, na ordem em que foram criados:

- `bea3e1c` — `fix: incluir solicitante na busca de chamados`
- `77884b7` — `fix: persistir atualizações dos chamados`
- `7261ae5` — `fix: limpar a base local de chamados`
- `edbcd1c` — `fix: gerar id quando não há chamados`
- `ceb8439` — `fix: rejeitar campos de texto em branco`

Validações realizadas:

- `node --check service-desk/app.js`
- Teste interativo no navegador: busca por solicitante retornou um chamado;
  status “Resolvido” persistiu após recarregar; limpeza deixou zero registros
  inclusive após recarregar; novo chamado sem registros recebeu ID `1001`;
  envio com campos compostos apenas por espaços não criou registros e exibiu
  mensagem de validação.

Este relatório é um commit documental separado. Nenhum merge para `main` foi
realizado.
