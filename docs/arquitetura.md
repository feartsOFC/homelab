# Arquitetura

<!-- TODO: trocar por um diagrama (pode ser um print do mapa interno, sem nomes de aparelhos) -->

```
Internet ── modem (bridge) ── AirPort Extreme 10.0.77.1 (roteador, DHCP .100+)
                                   │
                         ┌─────────┴──────────┐
                   iMac / Proxmox 10.0.77.18   PCs e celulares
                         │  bridge vmbr0
     ┌───────┬───────┬───┴───┬───────┬───────┬───────┐
   CT200   CT201   CT202   CT203   CT204   CT205   CT206 …
   ASF     bots    dados   Pi-hole Caddy   monit.  mídia
                           DNS     proxy   Kuma/Beszel
```

## Fluxo de um acesso
1. O aparelho pergunta `jellyfin.home` ao **Pi-hole** (CT203).
2. O curinga `*.home` aponta para o **Caddy** (CT204).
3. O Caddy encaminha para o IP:porta do serviço, com certificado da CA interna dele.
4. De fora de casa, o mesmo caminho passa pelo **Tailscale**: o host anuncia a rede 10.0.77.0/24.

## Monitoramento
- **Uptime Kuma**: um monitor por serviço; alertas num servidor de Discord só para isso.
- **Beszel**: CPU, memória e disco do host e de cada CT.
- **Crons do host**: temperatura, servidor ligou, backup atrasado, container parado, trava de disco.

## Backup
- Job do Proxmox de madrugada (modo suspend nos CTs).
- No fim do job, um hook acorda o PC de casa por Wake-on-LAN, copia os backups por SMB
  (porta liberada só para o IP do servidor), aplica a retenção (7 diários + 4 semanais) e desliga o PC
  se foi ele que acordou.
- Teste de restauração feito e repetível por script.
