---
title: Strumenti per le prestazioni Linux
titleSuffix: Azure Kubernetes Service
description: Informazioni su come individuare e risolvere i problemi comuni quando si usa il servizio Azure Kubernetes
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925605"
---
# <a name="linux-performance-troubleshooting"></a>Risoluzione dei problemi relativi alle prestazioni di LinuxLinux Performance

L'esaurimento delle risorse sulle macchine Linux è un problema comune e può manifestarsi attraverso un'ampia varietà di sintomi. Questo documento fornisce una panoramica generale degli strumenti disponibili per diagnosticare tali problemi.

Molti di questi strumenti accettano un intervallo in base al quale produrre la produzione in sequenza. Questo formato di output in genere semplifica molto l'avvistamento dei modelli. Se accettata, la chiamata `[interval]`di esempio includerà .

Molti di questi strumenti hanno una vasta storia e un'ampia gamma di opzioni di configurazione. Questa pagina fornisce solo un semplice sottoinsieme di chiamate per evidenziare i problemi comuni. La fonte canonica di informazioni è sempre la documentazione di riferimento per ogni particolare strumento. Tale documentazione sarà molto più approfondita di quanto qui fornito.

## <a name="guidance"></a>Materiale sussidiario

Sii sistematico nel tuo approccio all'analisi dei problemi di prestazioni. Due approcci comuni sono USE (utilizzo, saturazione, errori) e RED (tasso, errori, durata). RED viene in genere utilizzato nel contesto dei servizi per il monitoraggio basato su richiesta. USE viene in genere utilizzato per il monitoraggio delle risorse: per ogni risorsa in un computer, monitorare l'utilizzo, la saturazione e gli errori. I quattro tipi principali di risorse su qualsiasi computer sono cpu, memoria, disco e rete. L'utilizzo elevato, la saturazione o i tassi di errore per una di queste risorse indicano un possibile problema con il sistema. Quando si verifica un problema, esaminare la causa principale: perché la latenza di I/O del disco è elevata? I dischi o lo SKU della macchina virtuale vengono limitati? Quali processi stanno scrivendo sui dispositivi e su quali file?

Alcuni esempi di problemi comuni e indicatori per diagnosticarli:
- Limitazione delle operazioni di I/O al secondo: usare iostat per misurare le operazioni di I/O al secondo per dispositivo. Verificare che nessun singolo disco sia superiore al limite e che la somma per tutti i dischi sia inferiore al limite per la macchina virtuale.
- Limitazione larghezza di banda: usare iostat come per le operazioni di I/O al secondo, ma misurare la velocità effettiva di lettura/scrittura. Verificare che la velocità effettiva per dispositivo e aggregata sia inferiore ai limiti di larghezza di banda.
- Esaurimento SNAT: può manifestarsi come connessioni attive elevate (in uscita) in SAR. 
- Perdita di pacchetti: può essere misurata tramite proxy tramite il conteggio delle ritrasmissioni TCP rispetto al conteggio inviato/ricevuto. Entrambi `sar` `netstat` e possono mostrare queste informazioni.

## <a name="general"></a>Generale

Questi strumenti sono di uso generale e coprono le informazioni di base del sistema. Sono un buon punto di partenza per ulteriori indagini.

### <a name="uptime"></a>Uptime

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

il tempo di attività fornisce tempi di attività del sistema e medie di carico di 1, 5 e 15 minuti. Queste medie di carico corrispondono approssimativamente ai thread che eseguono il lavoro o in attesa del completamento del lavoro non interrompebile. In assoluto questi numeri possono essere difficili da interpretare, ma misurati nel tempo possono dirci informazioni utili:

- Media di 1 minuto > media di 5 minuti significa che il carico è in aumento.
- Media di 1 minuto < media di 5 minuti significa che il carico sta diminuendo.

tempo di attività può anche illuminare il motivo per cui le informazioni non sono disponibili: il problema potrebbe essere stato risolto da solo o da un riavvio prima che l'utente possa accedere alla macchina.

Le medie di carico superiori al numero di thread della CPU disponibili possono indicare un problema di prestazioni con un determinato carico di lavoro.

### <a name="dmesg"></a>Dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg scarica il buffer del kernel. Eventi come OOMKill aggiungono una voce al buffer del kernel. Trovare un OOMKill o altri messaggi di esaurimento delle risorse nei log dmesg è un forte indicatore di un problema.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`fornisce un'ampia panoramica dello stato corrente del sistema. Le intestazioni forniscono alcune utili informazioni di aggregazione:The headers provide some useful aggregate information:

- stato delle attività: in esecuzione, in stato di sospensione, arresto.
- Utilizzo della CPU, in questo caso per lo più mostrando il tempo di inattività.
- memoria di sistema totale, libera e utilizzata.

`top`può mancare processi di breve durata; alternative come `htop` `atop` e fornire interfacce simili, mentre la correzione di alcune di queste carenze.

## <a name="cpu"></a>CPU

Questi strumenti forniscono informazioni sull'utilizzo della CPU. Ciò è particolarmente utile con l'output di laminazione, in cui i modelli diventano facili da individuare.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`stampa informazioni della CPU simili all'inizio, ma suddivise per thread della CPU. Vedere tutti i core contemporaneamente può essere utile per rilevare l'utilizzo della CPU altamente sbilanciato, ad esempio quando una singola applicazione a thread utilizza un core al 100% di utilizzo. Questo problema può essere più difficile da individuare se aggregato su tutte le CPU del sistema.

### <a name="vmstat"></a>Vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`fornisce informazioni `mpstat` `top`simili e , enumerando il numero di processi in attesa sulla CPU (colonna r), le statistiche di memoria e la percentuale di tempo della CPU trascorso in ogni stato di lavoro.

## <a name="memory"></a>Memoria

La memoria è una risorsa molto importante, e fortunatamente facile da monitorare. Alcuni strumenti possono segnalare sia `vmstat`la CPU che la memoria, come . Ma strumenti `free` come possono ancora essere utili per il debug rapido.

### <a name="free"></a>libero

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`presenta informazioni di base sulla memoria totale, nonché sulla memoria utilizzata e libera. `vmstat`può essere più utile anche per l'analisi della memoria di base a causa della sua capacità di fornire l'output in sequenza.

## <a name="disk"></a>Disco

Questi strumenti misurano le operazioni di I/O al secondo del disco, le code di attesa e la velocità effettiva totale. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`fornisce informazioni approfondite sull'utilizzo del disco. Questa chiamata `-x` passa per `-y` le statistiche estese, per ignorare le `1 1` medie del sistema di stampa di output iniziale dall'avvio e per specificare si desidera un intervallo di 1 secondo, che termina dopo un blocco di output. 

`iostat`espone molte statistiche utili:

- `r/s`e `w/s` sono letture al secondo e scritture al secondo. La somma di questi valori è IOPS.
- `rkB/s`e `wkB/s` sono kilobyte letti/scritti al secondo. La somma di questi valori è velocità effettiva.
- `await`è il tempo medio di iowait in millisecondi per le richieste in coda.
- `avgqu-sz`è la dimensione media della coda nell'intervallo fornito.

In una macchina virtuale di Azure:On an Azure VM:

- la somma `r/s` `w/s` di e per un singolo dispositivo a blocchi non può superare i limiti SKU di tale disco.
- la somma `rkB/s` `wkB/s` di e per un singolo dispositivo a blocchi non può superare i limiti SKU di tale disco
- la somma `r/s` `w/s` di e per tutti i dispositivi a blocchi non può superare i limiti per lo SKU della macchina virtuale.
- la somma `rkB/s` di e 'wkB/s per tutti i dispositivi a blocchi non può superare i limiti per lo SKU VM.

Si noti che il disco del sistema operativo viene conteggiato come disco gestito dello SKU più piccolo corrispondente alla relativa capacità. Ad esempio, un disco operativo da 1024 GB corrisponde a un disco P30. I dischi ememeri e i dischi temporanei non hanno limiti di schiono singoli; sono limitati solo dai limiti completi della macchina virtuale.

Valori diversi da zero di await o avgqu-sz sono anche buoni indicatori di contesa I/O.

## <a name="network"></a>Rete

Questi strumenti misurano le statistiche di rete come la velocità effettiva, gli errori di trasmissione e l'utilizzo. Un'analisi più approfondita può esporre statistiche TCP specifiche sulla congestione e sui pacchetti eliminati.

### <a name="sar"></a>Sar

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`è uno strumento potente per una vasta gamma di analisi. Mentre questo esempio utilizza la sua capacità di misurare le statistiche di rete, è altrettanto potente per misurare il consumo di CPU e memoria. Questo esempio `sar` richiama `-n` con flag `DEV` per specificare la parola chiave (dispositivo di rete), che visualizza la velocità effettiva di rete in base al dispositivo.

- La somma `rxKb/s` `txKb/s` di ed è la velocità effettiva totale per un determinato dispositivo. Quando questo valore supera il limite per la scheda di rete di Azure di cui è stato eseguito il provisioning, i carichi di lavoro nel computer subiranno una latenza di rete maggiore.
- `%ifutil`misura l'utilizzo di un determinato dispositivo. Man mano che questo valore si avvicina al 100%, i carichi di lavoro subiranno una latenza di rete maggiore.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Questa chiamata `sar` di `TCP,ETCP` utilizza le parole chiave per esaminare le connessioni TCP. La terza colonna dell'ultima riga, "retrans", è il numero di ritrasmissioni TCP al secondo. Valori elevati per questo campo indicano una connessione di rete inaffidabile. Nella prima e nella terza riga, "attivo" significa una connessione originata dal dispositivo locale, mentre "remoto" indica una connessione in ingresso.  Un problema comune in Azure è l'esaurimento delle porte SNAT, che `sar` consente di rilevare. L'esaurimento delle porte SNAT riporrebbe valori "attivi" elevati, poiché il problema è dovuto a un'elevata frequenza di connessioni TCP in uscita avviate localmente.

Poiché `sar` accetta un intervallo, stampa l'output in sequenza e quindi stampa le righe finali dell'output contenenti i risultati medi della chiamata.

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`può introspezione una vasta gamma di statistiche di rete, qui invocato con output di riepilogo. Ci sono molti campi utili qui a seconda del problema. Un campo utile nella sezione TCP è "tentativi di connessione non riusciti". Questo può essere un'indicazione di esaurimento della porta SNAT o altri problemi che effettuano connessioni in uscita. Una frequenza elevata di segmenti ritrasmessi (anche nella sezione TCP) può indicare problemi con il recapito dei pacchetti. 
