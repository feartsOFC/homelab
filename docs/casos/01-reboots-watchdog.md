# O servidor que reiniciava sozinho toda semana

**Resumo:** o host reiniciava sem aviso mais ou menos uma vez por semana. A primeira hipótese
(queda de energia) caiu quando medi o tempo exato de cada reinício: o padrão apontava para o
watchdog do Proxmox resetando a máquina depois de um travamento de disco.

## Sintoma
- Reinícios marcados como `crash` no `last -x` (sem desligamento limpo) em várias datas de outubro.
- O log do boot anterior parava "seco": sem kernel panic, sem OOM, sem sequência de desligamento.
- Temperatura normal e SMART dos dois discos sem erros.

## Investigação
1. **Energia?** Parecia: o log some de repente. Mas o SMC da Apple mostrou `AUPO=0` (não liga sozinho
   depois de falta de luz). Se fosse corte de energia, o iMac teria ficado desligado. Ele sempre voltou sozinho.
2. **Medir o intervalo.** Comparei "última linha do log → primeira linha do kernel novo":
   - reboot limpo: **26 s** (POST + GRUB)
   - quedas: **96 s, 101 s, 102 s, 182 s**
   - ou seja, ~26 s + **60 a 75 s fixos**.
3. Esses 60 s batem com o **softdog** (`soft_margin=60`) mantido pelo `watchdog-mux` do Proxmox, que
   fica armado pelos serviços de HA mesmo sem cluster.
4. **Por que o sistema parava de alimentar o watchdog?** Tudo roda num HDD de 5400 rpm; a pressão de I/O
   (PSI) ficava alta o tempo todo. Um congelamento de ≥ 60 s no disco basta para o reset, e o erro nunca
   chega ao journal porque o disco é justamente o que travou.

## Solução
- Desliguei os serviços de HA (`pve-ha-lrm`, `pve-ha-crm`), que não fazem sentido num host único,
  e com isso o watchdog desarmou.
- Criei um aviso de trava: um serviço mede a gravação no disco a cada 20 s e manda no Discord
  quando o host congela e volta, com os processos que ficaram bloqueados.
- Guardei contadores de linha de base (power cycles e unsafe shutdowns do SMART) para distinguir
  numa próxima vez corte de energia de reset "quente".

## Resultado
<!-- TODO: preencher com o que aconteceu depois (o servidor parou de reiniciar? apareceram avisos de trava?) -->

## O que aprendi
<!-- TODO: com suas palavras -->
