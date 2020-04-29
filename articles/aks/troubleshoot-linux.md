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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77925605"
---
# <a name="linux-performance-troubleshooting"></a>Risoluzione dei problemi relativi alle prestazioni di Linux

L'esaurimento delle risorse nei computer Linux è un problema comune che può manifestarsi attraverso un'ampia gamma di sintomi. In questo documento viene fornita una panoramica di alto livello degli strumenti disponibili per la diagnosi di tali problemi.

Molti di questi strumenti accettano un intervallo in cui produrre output in sequenza. Questo formato di output rende in genere più semplice l'individuazione di modelli. Laddove accettata, la chiamata di esempio includerà `[interval]`.

Molti di questi strumenti hanno una cronologia estesa e un ampio set di opzioni di configurazione. Questa pagina fornisce solo un subset semplice di chiamate per evidenziare i problemi comuni. L'origine di informazioni canoniche è sempre la documentazione di riferimento per ogni particolare strumento. La documentazione sarà molto più approfondita rispetto a quanto specificato qui.

## <a name="guidance"></a>Indicazioni

Essere sistematici nell'approccio per analizzare i problemi di prestazioni. Vengono usati due approcci comuni (utilizzo, saturazione, errori) e rosso (frequenza, errori, durata). Il rosso viene in genere usato nel contesto dei servizi per il monitoraggio basato su richiesta. L'uso viene in genere usato per il monitoraggio delle risorse: per ogni risorsa in un computer, monitora l'utilizzo, la saturazione e gli errori. I quattro tipi principali di risorse in qualsiasi computer sono CPU, memoria, disco e rete. Un utilizzo elevato, saturazione o frequenza degli errori per una di queste risorse indica un possibile problema con il sistema. Quando esiste un problema, esaminare la causa principale: perché la latenza di i/o del disco è elevata? Lo SKU di dischi o macchine virtuali è limitato? Quali processi scrivono nei dispositivi e in quali file?

Di seguito sono riportati alcuni esempi di problemi e indicatori comuni per la relativa diagnosi:
- Limitazione IOPS: usare iostat per misurare IOPS per dispositivo. Assicurarsi che non vi sia un singolo disco superiore al limite e che la somma di tutti i dischi sia inferiore al limite della macchina virtuale.
- Limitazione della larghezza di banda: usare iostat come per IOPS, ma misurare la velocità effettiva di lettura/scrittura. Assicurarsi che sia la velocità effettiva per ogni dispositivo che quella aggregata siano inferiori ai limiti della larghezza di banda.
- Esaurimento SNAT: può essere manifesto come connessioni attive (in uscita) elevate in SAR. 
- Perdita di pacchetti: può essere misurata dal proxy tramite il numero di ritrasmissioni TCP rispetto al conteggio inviato/ricevuto. Sia `sar` che `netstat` possono visualizzare queste informazioni.

## <a name="general"></a>Generale

Questi strumenti sono a scopo generale e comprendono informazioni di base sul sistema. Si tratta di un valido punto di partenza per un'analisi più approfondita.

### <a name="uptime"></a>tempo

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

il tempo di esecuzione garantisce tempi di esecuzione del sistema e medie di carico di 1, 5 e 15 minuti. Queste medie di carico corrispondono approssimativamente ai thread che operano o attendono il completamento del lavoro di continuità. In assoluto questi numeri possono essere difficili da interpretare, ma misurati nel tempo possono indicare informazioni utili:

- media di 1 minuto > media di 5 minuti indica un aumento del carico.
- media di 1 minuto < media di 5 minuti significa che il carico diminuisce.

il tempo di indisponibilità può anche illuminare il motivo per cui le informazioni non sono disponibili: il problema può essere risolto autonomamente o mediante un riavvio prima che l'utente possa accedere al computer.

Le medie di carico superiori al numero di thread CPU disponibili possono indicare un problema di prestazioni con un determinato carico di lavoro.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg Scarica il buffer del kernel. Gli eventi come OOMKill aggiungono una voce al buffer del kernel. Trovare un OOMKill o altri messaggi di esaurimento delle risorse nei log di dmesg è un indicatore forte di un problema.

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

`top`fornisce una panoramica generale dello stato del sistema corrente. Le intestazioni forniscono alcune utili informazioni di aggregazione:

- stato delle attività: in esecuzione, in sospensione, arrestato.
- Utilizzo della CPU, in questo caso la maggior parte del tempo di inattività.
- memoria di sistema totale, gratuita e utilizzata.

`top`potrebbero mancare processi di breve durata; alternative come `htop` e `atop` forniscono interfacce analoghe durante la correzione di alcune di queste limitazioni.

## <a name="cpu"></a>CPU

Questi strumenti forniscono informazioni sull'utilizzo della CPU. Questa operazione è particolarmente utile con l'output in sequenza, in cui i modelli diventano facilmente individuabili.

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

`mpstat`stampa le informazioni di CPU simili nella parte superiore, ma suddivise in base al thread della CPU. La visualizzazione di tutti i core in una sola volta può essere utile per rilevare l'utilizzo della CPU altamente sbilanciata, ad esempio quando un'applicazione a thread singolo usa un core al 100% di utilizzo. Questo problema può essere più difficile da individuare quando viene aggregato su tutte le CPU del sistema.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`fornisce informazioni `mpstat` simili e `top`, enumerando il numero di processi in attesa di CPU (colonna r), statistiche della memoria e percentuale del tempo di CPU impiegato in ogni stato di lavoro.

## <a name="memory"></a>Memoria

La memoria è una risorsa molto importante e fortunatamente facile da tenere traccia. Alcuni strumenti possono segnalare CPU e memoria, ad esempio `vmstat`. Tuttavia, gli `free` strumenti come possono essere ancora utili per il debug rapido.

### <a name="free"></a>libero

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`vengono fornite informazioni di base sulla memoria totale, oltre che sulla memoria libera e utilizzata. `vmstat`può essere più utile anche per l'analisi della memoria di base grazie alla possibilità di fornire output in sequenza.

## <a name="disk"></a>Disco

Questi strumenti misurano IOPS del disco, le code di attesa e la velocità effettiva totale. 

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

`iostat`fornisce informazioni approfondite sull'utilizzo dei dischi. Questa chiamata passa `-x` per le statistiche estese `-y` , per ignorare le medie iniziali del sistema di stampa dell'output dall' `1 1` avvio e per specificare che si desidera un intervallo di 1 secondo, che termina dopo un blocco di output. 

`iostat`espone molte statistiche utili:

- `r/s`e `w/s` sono letture al secondo e scritture al secondo. La somma di questi valori è IOPS.
- `rkB/s`e `wkB/s` sono kilobyte letti/scritti al secondo. La somma di questi valori è la velocità effettiva.
- `await`tempo medio di iowait in millisecondi per le richieste in coda.
- `avgqu-sz`dimensioni medie della coda nell'intervallo specificato.

In una macchina virtuale di Azure:

- la somma di `r/s` e `w/s` per un singolo dispositivo a blocchi non può superare i limiti dello SKU del disco.
- la somma di `rkB/s` e `wkB/s` per un singolo dispositivo a blocchi non può superare i limiti dello SKU del disco
- la somma di `r/s` e `w/s` per tutti i dispositivi a blocchi non può superare i limiti per lo SKU della macchina virtuale.
- la somma di `rkB/s` è wkB/s per tutti i dispositivi a blocchi non può superare i limiti per lo SKU della macchina virtuale.

Si noti che il disco del sistema operativo viene conteggiato come un disco gestito dello SKU più piccolo corrispondente alla capacità. Ad esempio, un disco del sistema operativo 1024GB corrisponde a un disco P30. I dischi temporanei del sistema operativo e i dischi temporanei non hanno limiti di singoli dischi. sono limitati solo dai limiti completi della macchina virtuale.

Anche i valori diversi da zero di await o avgqu-SZ sono indicatori validi per la contesa di i/o.

## <a name="network"></a>Rete

Questi strumenti misurano le statistiche di rete, ad esempio velocità effettiva, errori di trasmissione e utilizzo. Un'analisi più approfondita può esporre statistiche TCP con granularità fine sulla congestione e sui pacchetti eliminati.

### <a name="sar"></a>-r.a.s.

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

`sar`è uno strumento potente per un'ampia gamma di analisi. Sebbene in questo esempio venga utilizzata la capacità di misurare le statistiche di rete, è ugualmente potente per misurare l'utilizzo della CPU e della memoria. Questo esempio richiama `sar` con `-n` flag per specificare la parola `DEV` chiave (dispositivo di rete), visualizzando la velocità effettiva della rete per dispositivo.

- La somma di `rxKb/s` e `txKb/s` è la velocità effettiva totale per un determinato dispositivo. Quando questo valore supera il limite per la scheda di interfaccia di rete di Azure di cui è stato effettuato il provisioning, i carichi di lavoro nel computer aumenteranno la latenza
- `%ifutil`misura l'utilizzo per un determinato dispositivo. Poiché questo valore si avvicina al 100%, i carichi di lavoro riscontrano una maggiore latenza di rete.

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

Questa chiamata di `sar` usa le parole `TCP,ETCP` chiave per esaminare le connessioni TCP. La terza colonna dell'ultima riga, "retrans", è il numero di ritrasmissioni TCP al secondo. Valori elevati per questo campo indicano una connessione di rete non affidabile. Nella prima e terza riga "Active" indica che la connessione è stata originata dal dispositivo locale, mentre "Remote" indica una connessione in ingresso.  Un problema comune in Azure è l'esaurimento delle porte SNAT `sar` , che può essere utile per il rilevamento. L'esaurimento delle porte SNAT si manifesterà come valori "attivi" elevati, perché il problema è dovuto a una frequenza elevata di connessioni TCP avviate localmente e in uscita.

Come `sar` accetta un intervallo, stampa l'output in sequenza e quindi stampa le righe finali di output contenenti i risultati medi della chiamata.

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

`netstat`può analizzare un'ampia gamma di statistiche di rete, richiamate con l'output di riepilogo. Sono disponibili molti campi utili a seconda del problema. Un campo utile nella sezione TCP è "tentativi di connessione non riusciti". Potrebbe trattarsi di un'indicazione dell'esaurimento delle porte SNAT o di altri problemi che rendono le connessioni in uscita. Una frequenza elevata di segmenti ritrasmessi (anche nella sezione TCP) può indicare problemi di recapito dei pacchetti. 
