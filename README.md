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
- **PIN do supervisor** (opcional) trava a tela de Ajustes. Só o hash com sal fica gravado; 5 erros = espera de 30s.
  Se o PIN for esquecido, não há recuperação pelo app.

## Preparado para sincronizar (etapa futura)
Dados em `localStorage` (chave `ferramentaria_v1`), todos os registros com `id` único, datas ISO e
`dispositivo`. Para vários aparelhos, trocar o objeto `DB` por um banco na nuvem (ex.: Supabase/Firebase).

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
