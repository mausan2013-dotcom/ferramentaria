# Ferramentaria — conferência de ferramentas por turno (PWA para Android)

App para a equipe de manutenção de vagões conferir as ferramentas no fim de cada turno,
comparar com o turno anterior e gerar alerta quando o número não bate.

## Arquivos
- `index.html` — o app inteiro (funciona sozinho, sem internet)
- `manifest.webmanifest`, `sw.js`, `icon-*.png` — instalação na tela inicial e uso offline (só valem hospedado em HTTPS)

## Como funciona
- **Esperado** de cada ferramenta = contagem da última conferência + entradas/baixas registradas depois dela.
- **Conferência de turno**: escolhe turno e responsável, conta item a item (−/+, digitar ou “= anterior”).
  Só fecha com todos os itens contados. Diferenças exigem motivo e viram **alertas pendentes** até alguém “Tratar”.
- O relatório sai pelo compartilhamento do Android (WhatsApp, e-mail…).
- **Entrada / Baixa** registra compras, quebras, envios para reparo etc. sem gerar alerta falso.
- Opção de **contagem às cegas** (esconde o número anterior durante a contagem).
- Backup JSON, restauração e exportação CSV (abre no Excel).
- **Alertas por WhatsApp em 1 toque**: contatos em Ajustes; ao fechar com divergência o WhatsApp abre na conversa
  do 1º contato com a mensagem pronta (os outros têm botão na tela de resultado). Envio sem toque = etapa da nuvem
  (API oficial do WhatsApp/Meta + servidor).
- **Níveis de acesso** (v1.5, substituem o PIN): login **técnico** (padrão) confere turnos, registra motivos, vê histórico
  e envia relatórios; login **supervisor** também mexe em Ajustes, ferramentas, entradas/baixas e trata divergências.
  A regra vale na nuvem (RLS). Definir nível no SQL Editor: `select public.definir_papel('email', 'supervisor');`
  Sem nuvem, o aparelho tem acesso total.

## Nuvem (Supabase) — desde a v1.3
- Projeto Supabase `blizyewnlcyaxizwlzdk` (região São Paulo), conta no e-mail pessoal. Cadastro público desligado;
  logins criados em Authentication → Users (um login por aparelho, pode ser o mesmo nos vários aparelhos).
- Scripts em `supabase/`: `01-tabelas.sql` (tabelas, regras de acesso, visões), `02-permissoes.sql`,
  `03-limpar-dados.sql` (apaga tudo da nuvem — só para limpar testes),
  `04-niveis-acesso.sql` (tabela `perfis`, funções `eh_supervisor` e `definir_papel`, regras por nível),
  `05-apagar-historico.sql` (só supervisor grava/altera conferência marcada como excluída).
- Local primeiro: grava no aparelho (`ferramentaria_v1`) e sincroniza quando há internet (ao abrir, ao voltar o sinal,
  1,5 s após cada alteração e a cada 60 s). Envia só o que mudou (impressão digital por registro); recebe pelo
  `sinc_em` do servidor. Conflito no mesmo registro: vale o último envio. Na 1ª sincronização de um aparelho,
  os Ajustes da nuvem prevalecem.
- A conferência em andamento (rascunho) fica só no aparelho até ser encerrada.
- Para Excel/Power BI: visões `v_ferramentas`, `v_movimentacoes`, `v_conferencia_linhas` (Table Editor → exportar CSV).
- Ninguém apaga registros pelo app; exclusões só pelo painel do Supabase.

## Versão 1.0 (02/10/2026)
- Cadastro (individual e importação em lista), locais, categorias, desativação com baixa automática
- Conferência de turno com comparação, alertas, motivos rápidos, tratamento das divergências
- Histórico por conferência e por ferramenta, ajustes de equipe/turnos, backup/CSV, PWA offline

## Versão 1.1 (02/10/2026)
- Trava dos Ajustes com PIN do supervisor (criar, trocar, remover, travar agora; trava de novo ao sair da tela)
- Avisos na tela não se sobrepõem mais

## Versão 1.2 (02/10/2026)
- Contatos para alerta de divergência (nome; DDD e número) e abertura automática do WhatsApp com a mensagem pronta
- Botões Enviar/Reenviar por contato, com registro de quando foi aberto, no resultado e no histórico

## Versão 1.3 (03/10/2026)
- Login da equipe e sincronização com a nuvem (Supabase), funcionando sem sinal e enviando ao voltar
- Indicador ☁ no topo (✓ salvo, ↻ enviando, sem sinal · N pendentes, ⚠ erro); toque para detalhes
- Ajustes → Nuvem: conta, última sincronização, sincronizar agora, sair da conta (limpa o aparelho)
- Opção "Usar sem nuvem"; ao entrar depois, os dados do aparelho sobem para a nuvem
- Tratamentos de divergência viraram registros próprios (migração automática da v1.2)

## Versão 1.4 (03/10/2026)
- Visual no padrão da empresa (referência: rumolog.com): azul-marinho #043865, azul-claro #32a6e6,
  fundo #ebf0f2, fonte Open Sans (guardada para uso offline), cantos retos, bloco de status com canto cortado,
  abas em maiúsculas com marcador azul; tema escuro em azul-marinho; ícone novo
- Logo da empresa NÃO incluído: o site é público e o app ainda é teste sem aval formal (aguarda autorização)

## Versão 1.5 (03/10/2026)
- Níveis de acesso por login (técnico x supervisor), validados também na nuvem; PIN removido
- Técnico vê a aba "Conta" no lugar de "Ajustes"; botões de cadastro, entrada/baixa e "Tratar" só para supervisor
- O técnico envia à nuvem só conferências; o resto que mudar no aparelho dele é substituído pela versão da nuvem

## Versão 1.6 (03/10/2026)
- Botão "Trocar de login" (Conta/Ajustes), para os dois níveis: entra com outro login no mesmo aparelho sem apagar
  nada (os dados da equipe são os mesmos para todos os logins); sincroniza antes de trocar; senha errada mantém o login atual

## Versão 1.7 (03/10/2026)
- Supervisor apaga histórico: em Ajustes → Histórico (mais antigas que 30/90 dias ou todas menos a última, com
  confirmação digitando APAGAR) e em cada conferência ("Apagar esta conferência", avisa se for a mais recente)
- A última conferência é mantida no apagar em massa (é a referência do próximo turno)
- Exclusão sincroniza por "marca" {id, excluida, excluidaEm, por} sem o conteúdo; a marca sempre prevalece;
  tratamentos da conferência vão junto (lista local `excluidos`)
- Correção: eventos de uma janela não passam mais para a próxima
