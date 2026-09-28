# Indicador Timbro — contexto para o Claude

Dashboard de performance logística da PortoEx para o cliente **Timbro**. É uma
cópia do Indicador Forte (repo `maurocmarques-creator/indicador-forte`, site
`forte.portoexapps.com.br`, que por sua vez é cópia do Indicador Ansell), com
os mesmos scripts genéricos; tudo o que é específico do cliente fica em
`cliente_config.json`.

**Esta cópia foi criada em 28/09/2026 e ainda não está em produção.** Vários
itens estão marcados como `TODO` e precisam ser preenchidos antes de publicar
— ver seção "Pendências desta cópia" abaixo.

## Publicação (a configurar)
- Repo: ainda não existe no GitHub. Sugestão de nome: `indicador-timbro`,
  seguindo o padrão dos outros (`indicador-ansell`, `indicador-forte`).
- Site estático via GitHub Pages. `CNAME` já está com o domínio sugerido
  `timbro.portoexapps.com.br` (placeholder — confirmar e criar o CNAME no
  Cloudflare, igual ao da Forte, antes de valer).
- Qualquer push no `main` publica, quando o repo existir. **Pedir confirmação
  ao usuário antes de dar push**, igual nos outros indicadores.

## Arquivos
Mesma estrutura do Indicador Forte — ver o `CLAUDE.md` de lá para o
funcionamento detalhado de cada arquivo (`extrair_portal.py`,
`atualizar_dashboard.py`, `pipeline_atualizar.py`, `config.py`, `index.html`,
os módulos de Cadastro/Simulador/Auditoria). Aqui só o que muda:

- `cliente_config.json` — cheio de `TODO` para preencher: nome do cliente no
  portal Brudam (`clientes_portal`), relatório personalizado 106
  (`template_relatorio` — confirmar se a Timbro tem um relatório próprio ou
  se reaproveita o `AUDITORIA TELA 106_ANSELL`), pasta do OneDrive
  consolidado, e-mail de destino do aviso. `reentrega_conta_performance` foi
  deixado `false` (padrão da Ansell) — perguntar ao cliente se a Timbro quer
  o mesmo comportamento da Forte (`true`).
- `index.html` — copiado do Indicador Forte e com os identificadores trocados:
  `GH_REPO = 'indicador-timbro'`, `GH_TOKEN_KEY = 'indicador_timbro_gh_token'`,
  `CAD_SERVICOS_KEY = 'timbro_servicos'` (chave própria no Supabase, não
  conflita com a `forte_servicos`). O `RAW` foi **esvaziado** (não é dado real
  da Timbro — era da Forte, removido para não vazar dado de um cliente para
  o outro). `MURAL_URL` ficou vazio — não existe Mural da Timbro ainda.
  **Logo:** ainda está o logo da Forte Logística — tem um comentário
  `<!-- TODO Timbro -->` no `<header>` marcando onde trocar.
- **Cadastros compartilhados no Supabase:** `cadastro_tabelas` (tabelas de
  frete), `cadastro_icms` e `cadastro_cidades` usam as **mesmas chaves
  genéricas** do Indicador Forte, no mesmo projeto Supabase da PortoEx —
  isso é proposital (dado de referência reaproveitado entre projetos, ver
  `CLAUDE.md` da Forte). Ou seja: se alguém mexer nas telas de Cadastro >
  Tabela/ICMS/Cidade nesta cópia, está mexendo nos **mesmos dados de
  produção** que o Indicador Forte usa. Só o Cadastro > Serviço é isolado
  por cliente (`timbro_servicos`).
- `.github/workflows/atualizar-manual.yml` — apontando para um runner
  self-hosted que ainda não existe; `working-directory` com `TODO`.

## Pendências desta cópia
1. ~~Confirmar o nome exato da Timbro no filtro do portal Brudam.~~ Feito: cliente
   aparece como `AC COMERCIAL IMPORTADORA`, no portal `pexlogistica.brudam.com.br`
   (diferente do `azportoex.brudam.com.br` usado pela Forte/Ansell). Como o login
   desse portal é separado, `extrair_portal.py` e `pipeline_atualizar.py` foram
   atualizados para ler `PORTAL_USER_TIMBRO`/`PORTAL_PASS_TIMBRO` em vez de
   `PORTAL_USER`/`PORTAL_PASS` (evita colisão com o login azportoex no mesmo PC).
   **Falta obter e configurar essas credenciais de fato.**
2. ~~Confirmar/gerar o relatório personalizado 106 no portal.~~ Feito: relatório
   gerado no portal como "Auditoria Timbro" (`template_relatorio` já atualizado).
3. ~~Preencher `cliente_config.json` (pasta OneDrive, e-mail de aviso).~~ Feito:
   `email_destino` = brenda.elicia@portoex.com.br; `onedrive_consolidado` aponta
   pra `Analise Timbro` dentro do OneDrive da Brenda (`C:\Users\Brenda\OneDrive - PORTOEXPRESS LOGISTICA LTDA\Analise Timbro`,
   pasta já criada). **Atenção:** esse caminho é da máquina da Brenda — se o
   pipeline rodar em outro PC (ver item do runner), ajustar aqui.
4. ~~Trocar o logo no `index.html`.~~ Feito.
5. ~~Criar o repositório `indicador-timbro` no GitHub e dar o primeiro push.~~ Feito,
   em `brendaelicia-sketch/indicador-timbro` (conta da Brenda, não a
   `maurocmarques-creator` usada pela Forte/Ansell — decisão explícita da Brenda;
   ver nota abaixo sobre o que isso muda na configuração do runner/Pages).
6. Configurar o domínio `timbro.portoexapps.com.br` no Cloudflare (CNAME +
   Cloudflare Access) e o GitHub Pages.
7. Registrar um runner self-hosted próprio da Timbro (`C:\actions-runner-timbro`
   ou nome equivalente) e criar a tarefa agendada do pipeline.
8. Decidir se cria um Mural próprio (Artifact) para a Timbro.
9. Decidir `reentrega_conta_performance`.

**Nota sobre o repo estar em `brendaelicia-sketch`:** diferente da Forte/Ansell,
o dono do repo não é quem hoje administra os runners self-hosted (PC do Mauro).
Quem for configurar o runner/GitHub Pages/Actions precisa de acesso de admin
nesse repo — se for o Mauro, `brendaelicia-sketch` precisa adicioná-lo como
colaborador antes.

## Rodar local
- Visualizar: `python -m http.server 8935` nesta pasta (`.claude/launch.json`
  usa `python` do PATH — igual ao Forte).
- Credenciais **nunca** no código: variáveis de ambiente `PORTAL_USER`/
  `PORTAL_PASS` (portal Brudam) e `EMAIL_USER`/`EMAIL_PASS` (Gmail).
