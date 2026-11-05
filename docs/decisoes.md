# Decisões e regras da casa

| Decisão | Por quê |
|---|---|
| Nenhuma porta aberta no roteador | Superfície de ataque zero; o Tailscale resolve o acesso de fora |
| LXC + Docker Compose, sem Kubernetes | Um host só, 16 GB; complexidade não paga o benefício |
| Faixa 10.0.77.0/24 | Não colidir com as redes comuns (192.168.0.x) de onde acesso pela VPN |
| DNS do Pi-hole configurado à mão nos aparelhos escolhidos | O DHCP do roteador não anuncia o Pi-hole: se ele cair, a casa continua com internet |
| HTTPS interno (`tls internal` do Caddy) | Nomes `.home` com cadeado, sem expor nada para emitir certificado público |
| Backup fora do servidor, em PC que dorme | Sem custo de nuvem; o PC só liga para receber a cópia |
| Toda mudança registrada por data | Saber o que mudou quando algo quebra |
| Testes visuais pelo host, nunca dentro do CT do painel | O CT tem 512 MB; um Chrome lá dentro já travou o servidor |

<!-- TODO: acrescentar decisões que você acha importantes e o "por quê" com suas palavras -->
