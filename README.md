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

## Preparado para sincronizar (etapa futura)
Dados em `localStorage` (chave `ferramentaria_v1`), todos os registros com `id` único, datas ISO e
`dispositivo`. Para vários aparelhos, trocar o objeto `DB` por um banco na nuvem (ex.: Supabase/Firebase).

## Versão 1.0 (02/10/2026)
- Cadastro (individual e importação em lista), locais, categorias, desativação com baixa automática
- Conferência de turno com comparação, alertas, motivos rápidos, tratamento das divergências
- Histórico por conferência e por ferramenta, ajustes de equipe/turnos, backup/CSV, PWA offline
