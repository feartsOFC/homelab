# Placar da FURIA no painel: escolhendo fontes de dados

**Resumo:** eu queria no painel os próximos jogos de CS2 da FURIA, a chave do campeonato e, durante o
jogo, o placar de rounds com o cronômetro. O trabalho maior não foi o código, foi escolher de onde
tirar os dados de forma legítima.

## Opções avaliadas
| Fonte | Resultado |
|---|---|
| Liquipedia (API MediaWiki) | ✅ calendário e chave; sem chave de API; regras claras (User-Agent próprio, gzip, ≤ 1 pedido / 30 s) |
| HLTV | ❌ sem API pública; a listagem filtrada é proibida no `robots.txt`; contornar bloqueio não é opção |
| APIs internas de sites (scorebot, bo3.gg) | ❌ não são feitas para uso externo |
| GRID Open Access | ❌ só dados históricos; ao vivo é comercial |
| PandaScore | ⚠️ grátis só calendário/resultados/chave; ao vivo é pago (caro) |
| Cito API | ✅ ao vivo com round, cronômetro e bomba; grátis com 60 s de atraso e 500 pedidos/mês |
| Game State Integration do CS2 | ⚠️ oficial e exato, mas exige o jogo aberto num PC assistindo |

## Como ficou
- A **Liquipedia** dá os próximos jogos e a página do campeonato; um parser de HTML monta a chave
  (fases por profundidade da árvore do bracket). Dois pedidos a cada 5 min, separados por 31 s.
- A **Cito** só é consultada quando a Liquipedia mostra jogo começado: 1 pedido por minuto, com teto
  diário, para caber no plano grátis.
- O cronômetro do round corre no navegador a partir do último dado, usando o relógio do servidor.
- Se uma fonte falha, o quadro cai para o nível anterior (só placar de mapas, ou só calendário).

## O que aprendi
<!-- TODO -->
