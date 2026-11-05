# Container .NET que achava ter 16 GB

**Resumo:** o ASF (aplicação .NET em Docker, dentro de um container LXC de 1 GB) era morto pelo kernel
a cada ~33 h, mas o Docker dizia que o container tinha saído normalmente.

## Sintoma
- O processo `dotnet` sumia; `docker inspect` mostrava saída 0 e `OOMKilled=false`.
- No log do host: OOM do cgroup do LXC, com o processo usando ~917 MB.

## Causa
- O limite de memória era do **LXC**, não do container Docker. Dentro dele, `memory.max` valia `max`.
- O .NET dimensiona o heap pelo limite que enxerga: "via" os 16 GB do host e deixava o heap crescer
  até bater no teto do LXC. Quem matava era o kernel do host, por isso o Docker não percebia.

## Correção
- `mem_limit: 1g` no Compose: agora o container enxerga o limite e o coletor de lixo se ajusta.
- Modo de otimização de memória do próprio ASF.
- LXC com folga (1,5 GB) acima do limite do container.
- Um cron mediu a memória por um dia antes de apagar os backups dos arquivos alterados.

## O que aprendi
<!-- TODO -->
