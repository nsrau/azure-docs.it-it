---
title: Ottimizzazione delle prestazioni di TCP/IP per le macchine virtuali di Azure Documenti Microsoft
description: Informazioni su varie tecniche comuni di ottimizzazione delle prestazioni TCP/IP e sulla relativa relazione con le macchine virtuali di Azure.Learn various common TCP/IP performance tuning techniques and their relationship to Azure VMs.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68297776"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Ottimizzazione delle prestazioni TCP/IP per le macchine virtuali di Azure

Questo articolo illustra le tecniche comuni di ottimizzazione delle prestazioni TCP/IP e alcuni aspetti da considerare quando vengono usate per le macchine virtuali in esecuzione in Azure.This article discusses common TCP/IP performance tuning techniques and some things to consider when you use them for virtual machines running on Azure. Esso fornirà una panoramica di base delle tecniche ed esplorare come possono essere sintonizzati.

## <a name="common-tcpip-tuning-techniques"></a>Tecniche comuni di ottimizzazione TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, frammentazione e scarica di invio di grandi dimensioni

#### <a name="mtu"></a>MTU

L'unità massima di trasmissione (MTU) è il frame di dimensioni più grandi (pacchetto), specificato in byte, che può essere inviato tramite un'interfaccia di rete. L'MTU è un'impostazione configurabile. L'MTU predefinito usato nelle macchine virtuali di Azure e l'impostazione predefinita nella maggior parte dei dispositivi di rete a livello globale è 1.500 byte.

#### <a name="fragmentation"></a>Frammentazione

La frammentazione si verifica quando viene inviato un pacchetto che supera l'MTU di un'interfaccia di rete. Lo stack TCP/IP suddividerà il pacchetto in pezzi più piccoli (frammenti) conformi all'MTU dell'interfaccia. La frammentazione si verifica a livello IP ed è indipendente dal protocollo sottostante (ad esempio TCP). Quando un pacchetto da 2.000 byte viene inviato su un'interfaccia di rete con un MTU di 1.500, il pacchetto verrà suddiviso in un pacchetto da 1.500 byte e un pacchetto da 500 byte.

I dispositivi di rete nel percorso tra un'origine e una destinazione possono eliminare i pacchetti che superano l'MTU o frammentare il pacchetto in parti più piccole.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Il bit Don't Fragment in un pacchetto IP

Il bit Don't Fragment (DF) è un flag nell'intestazione del protocollo IP. Il bit DF indica che i dispositivi di rete nel percorso tra il mittente e il destinatario non devono frammentare il pacchetto. Questo bit potrebbe essere impostato per molte ragioni. (Vedere la sezione "Path MTU Discovery" di questo articolo per un esempio.) Quando un dispositivo di rete riceve un pacchetto con il bit Don't Fragment impostato e tale pacchetto supera l'interfaccia MTU del dispositivo, il comportamento standard prevede che il dispositivo eschi il pacchetto. Il dispositivo invia un messaggio ICMP Fragmentation Needed all'origine originale del pacchetto.

#### <a name="performance-implications-of-fragmentation"></a>Implicazioni delle prestazioni della frammentazionePerformance implications of fragmentation

La frammentazione può avere implicazioni negative sulle prestazioni. Uno dei motivi principali dell'effetto sulle prestazioni è l'impatto CPU/memoria della frammentazione e del riassemblaggio dei pacchetti. Quando un dispositivo di rete deve frammentare un pacchetto, dovrà allocare risorse di CPU/memoria per eseguire la frammentazione.

La stessa cosa accade quando il pacchetto viene riassemblato. Il dispositivo di rete deve memorizzare tutti i frammenti fino a quando non vengono ricevuti in modo da poterli riassemblare nel pacchetto originale. Questo processo di frammentazione e riassemblaggio può anche causare latenza.

L'altra possibile implicazione negativa per le prestazioni della frammentazione è che i pacchetti frammentati potrebbero arrivare in ordine. Quando i pacchetti vengono ricevuti in ordine, alcuni tipi di dispositivi di rete possono eliminarli. Quando ciò accade, l'intero pacchetto deve essere ritrasmesso.

I frammenti vengono in genere eliminati dai dispositivi di sicurezza come i firewall di rete o quando i buffer di ricezione di un dispositivo di rete vengono esauriti. Quando i buffer di ricezione di un dispositivo di rete sono esauriti, un dispositivo di rete sta tentando di riassemblare un pacchetto frammentato ma non dispone delle risorse per archiviare e riprendere il pacchetto.

La frammentazione può essere vista come un'operazione negativa, ma il supporto per la frammentazione è necessario quando si connettono reti diverse su Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Vantaggi e conseguenze della modifica dell'MTU

In generale, è possibile creare una rete più efficiente aumentando l'MTU. Ogni pacchetto trasmesso ha informazioni di intestazione che vengono aggiunte al pacchetto originale. Quando la frammentazione crea più pacchetti, c'è più sovraccarico di intestazione e questo rende la rete meno efficiente.

Di seguito è riportato un esempio. La dimensione dell'intestazione Ethernet è di 14 byte più una sequenza di controllo dei frame a 4 byte per garantire la coerenza dei frame. Se viene inviato un pacchetto da 2.000 byte, nella rete vengono aggiunti 18 byte di sovraccarico Ethernet. Se il pacchetto è frammentato in un pacchetto di 1.500 byte e un pacchetto di 500 byte, ogni pacchetto avrà 18 byte di intestazione Ethernet, per un totale di 36 byte.

Tieni presente che l'aumento dell'MTU non creerà necessariamente una rete più efficiente. Se un'applicazione invia solo pacchetti da 500 byte, lo stesso sovraccarico dell'intestazione esisterà se l'MTU è di 1.500 byte o 9.000 byte. La rete diventerà più efficiente solo se utilizza pacchetti di dimensioni maggiori che sono interessati dall'MTU.

#### <a name="azure-and-vm-mtu"></a>Azure and VM MTU

L'MTU predefinito per le macchine virtuali di Azure è 1.500 byte. Lo stack della rete virtuale di Azure tenterà di frammentare un pacchetto a 1.400 byte.

Si noti che lo stack di rete virtuale non è intrinsecamente inefficiente perché frammenta i pacchetti a 1.400 byte anche se le macchine virtuali hanno un MTU di 1.500. Una percentuale elevata di pacchetti di rete è molto inferiore a 1.400 o 1.500 byte.

#### <a name="azure-and-fragmentation"></a>Azure e frammentazione

Lo stack di reti virtuali è impostato per eliminare "i frammenti in ordine", ovvero i pacchetti frammentati che non arrivano nell'ordine frammentato originale. Questi pacchetti vengono eliminati principalmente a causa di una vulnerabilità di sicurezza di rete annunciata nel novembre 2018 chiamato FragmentSmack.

FragmentSmack è un difetto nel modo in cui il kernel Linux ha gestito il riassemblaggio di pacchetti IPv4 e IPv6 frammentati. Un utente malintenzionato remoto potrebbe utilizzare questo difetto per attivare costose operazioni di riassemblaggio dei frammenti, che potrebbero portare a un aumento della CPU e di una negazione del servizio nel sistema di destinazione.

#### <a name="tune-the-mtu"></a>Regolare l'MTU

È possibile configurare un'unità MTU della macchina virtuale di Azure, come in qualsiasi altro sistema operativo. Tuttavia, è necessario considerare la frammentazione che si verifica in Azure, descritta in precedenza, quando si configura un'autenticazione a più file.

Non incoraggiamo i clienti ad aumentare le MTU delle macchine virtuali. Questa discussione ha lo scopo di spiegare i dettagli di come Azure implementa MTU ed esegue la frammentazione.

> [!IMPORTANT]
>L'aumento dell'MTU non è noto per migliorare le prestazioni e potrebbe avere un effetto negativo sulle prestazioni dell'applicazione.
>
>

#### <a name="large-send-offload"></a>Carico di invio di grandi dimensioni

L'offload di invio di grandi dimensioni (LSO) può migliorare le prestazioni di rete scaricando la segmentazione dei pacchetti sulla scheda Ethernet. Quando LSO è abilitato, lo stack TCP/IP crea un pacchetto TCP di grandi dimensioni e lo invia alla scheda Ethernet per la segmentazione prima di inoltrarlo. Il vantaggio di LSO è che può liberare la CPU dalla segmentazione dei pacchetti in dimensioni conformi all'MTU e scaricare tale elaborazione all'interfaccia Ethernet in cui viene eseguita nell'hardware. Per ulteriori informazioni sui vantaggi di LSO, vedere [Supporto di offload](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)di invio di grandi dimensioni .

Quando LSO è abilitato, i clienti di Azure potrebbero visualizzare frame di grandi dimensioni quando eseguono acquisizioni di pacchetti. Queste dimensioni di frame di grandi dimensioni potrebbero portare alcuni clienti a pensare che la frammentazione è in corso o che un grande MTU viene utilizzato quando non lo è. Con LSO, la scheda Ethernet può annunciare una dimensione massima del segmento (MSS) maggiore nello stack TCP/IP per creare un pacchetto TCP più grande. L'intero frame non segmentato viene quindi inoltrato alla scheda Ethernet e sarebbe visibile in un'acquisizione di pacchetti eseguita nella macchina virtuale. Ma il pacchetto verrà suddiviso in molti frame più piccoli dalla scheda Ethernet, secondo l'MTU della scheda Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Ridimensionamento delle finestre MSS TCP e PMTUD

#### <a name="tcp-maximum-segment-size"></a>Dimensione massima segmento TCP

La dimensione massima dei segmenti TCP (MSS) è un'impostazione che limita la dimensione dei segmenti TCP, evitando la frammentazione dei pacchetti TCP. I sistemi operativi utilizzano in genere questa formula per impostare MSS:

`MSS = MTU - (IP header size + TCP header size)`

L'intestazione IP e l'intestazione TCP sono 20 byte ciascuno, o 40 byte in totale. Quindi un'interfaccia con un MTU di 1.500 avrà un MSS di 1.460. Ma il MSS è configurabile.

Questa impostazione viene concordata nell'handshake a tre vie TCP quando viene impostata una sessione TCP tra un'origine e una destinazione. Entrambi i lati inviano un valore MSS e il più basso dei due viene utilizzato per la connessione TCP.

Tenere presente che le MTU dell'origine e della destinazione non sono gli unici fattori che determinano il valore MSS. I dispositivi di rete intermedi, ad esempio i gateway VPN, incluso il gateway VPN di Azure, possono modificare l'MTU indipendentemente dall'origine e dalla destinazione per garantire prestazioni di rete ottimali.

#### <a name="path-mtu-discovery"></a>Individuazione MTU percorso

MSS viene negoziato, ma potrebbe non indicare il MSS effettivo che può essere utilizzato. Ciò è dovuto al fatto che altri dispositivi di rete nel percorso tra l'origine e la destinazione potrebbero avere un valore MTU inferiore rispetto all'origine e alla destinazione. In questo caso, il dispositivo il cui MTU è inferiore a quello del pacchetto interromperà il pacchetto. Il dispositivo invierà un messaggio ICMP Fragmentation Needed (Tipo 3, Codice 4) che contiene il relativo MTU. Questo messaggio ICMP consente all'host di origine di ridurre il percorso MTU in modo appropriato. Il processo è denominato Path MTU Discovery (PMTUD).

Il processo PMTUD è inefficiente e influisce sulle prestazioni della rete. Quando vengono inviati pacchetti che superano l'MTU di un percorso di rete, i pacchetti devono essere ritrasmessi con un MSS inferiore. Se il mittente non riceve il messaggio ICMP Fragmentation Needed, forse a causa di un firewall di rete nel percorso (comunemente indicato come *blackhole PMTUD*), il mittente non sa che è necessario abbassare il MSS e ritrasmetterà continuamente il pacchetto. Questo è il motivo per cui non è consigliabile aumentare l'MTU della macchina virtuale di Azure.This is why we don't recommend increasing the Azure VM MTU.

#### <a name="vpn-and-mtu"></a>VPN e MTU

Se si utilizzano macchine virtuali che eseguono l'incapsulamento (ad esempio VPN IPsec), esistono alcune considerazioni aggiuntive relative alle dimensioni del pacchetto e al mTU. Le VPN aggiungono più intestazioni ai pacchetti, il che aumenta la dimensione del pacchetto e richiede un MSS più piccolo.

Per Azure, è consigliabile impostare il bloccaggio TCP MSS su 1.350 byte e l'interfaccia del tunnel MTU su 1.400.For Azure, we recommend that you set TCP MSS claming to 1,350 bytes and tunnel interface MTU to 1,400. Per ulteriori informazioni, vedere la [pagina Dispositivi VPN e parametri IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latenza, tempo di andata e ritorno e ridimensionamento delle finestre TCP

#### <a name="latency-and-round-trip-time"></a>Latenza e tempo di andata e ritorno

La latenza di rete è regolata dalla velocità della luce su una rete in fibra ottica. La velocità effettiva di rete di TCP è inoltre regolata in modo efficace dal tempo di andata e ritorno (RTT) tra due dispositivi di rete.

| | | | |
|-|-|-|-|
|**Route**|**Distance**|**Tempo unidirezionale**|**Rtt**|
|Da New York a San Francisco|4.148 km|21 ms|42 ms|
|Da New York a Londra|5.585 km|28 ms|56 ms|
|Da New York a Sydney|15.993 km|80 ms|160 ms|

Questa tabella mostra la distanza in linea retta tra due posizioni. Nelle reti, la distanza è in genere più lunga della distanza in linea retta. Ecco una semplice formula per calcolare RTT minimo come regolato dalla velocità della luce:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

È possibile utilizzare 200 per la velocità di propagazione. Questa è la distanza, in metri, che la luce viaggia in 1 millisecondo.

Portiamo New York a San Francisco come esempio. La distanza in linea retta è di 4.148 km. Collegando quel valore all'equazione, otteniamo quanto segue:

`Minimum RTT = 2 * (4,148 / 200)`

L'output dell'equazione è in millisecondi.

Se si desidera ottenere le migliori prestazioni di rete, l'opzione logica consiste nel selezionare le destinazioni con la distanza più breve tra di esse. È inoltre consigliabile progettare la rete virtuale per ottimizzare il percorso del traffico e ridurre la latenza. Per ulteriori informazioni, vedere la sezione "Considerazioni sulla progettazione della rete" di questo articolo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Effetti di latenza e tempo di andata e ritorno sul TCP

Il tempo di andata e ritorno ha un effetto diretto sulla velocità effettiva TCP massima. Nel protocollo TCP, *la dimensione* della finestra è la quantità massima di traffico che può essere inviata tramite una connessione TCP prima che il mittente debba ricevere la conferma dal destinatario. Se il sistema MSS TCP è impostato su 1.460 e la dimensione della finestra TCP è impostata su 65.535, il mittente può inviare 45 pacchetti prima di dover ricevere la conferma dal destinatario. Se il mittente non ottiene la conferma, ritrasmetterà i dati. La formula è:

`TCP window size / TCP MSS = packets sent`

In questo esempio, 65.535 / 1.460 viene arrotondato per e00 a 45.

Questo stato di "in attesa di riconoscimento", un meccanismo per garantire un recapito affidabile dei dati, è ciò che causa RTT per influenzare la velocità effettiva TCP. Più tempo il mittente attende la conferma, più tempo deve attendere prima di inviare più dati.

Di seguito è riportata la formula per calcolare la velocità effettiva massima di una singola connessione TCP:Here's the formula for calculating the maximum throughput of a single TCP connection:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Questa tabella mostra la velocità effettiva massima di megabyte al secondo di una singola connessione TCP. Per la leggibilità, i megabyte vengono utilizzati per l'unità di misura.

| | | | |
|-|-|-|-|
|**Dimensioni finestra TCP (byte)**|**Latenza RTT (ms)**|**Velocità effettiva massima di megabyte/secondo**|**Velocità effettiva media megabit/secondo**|
|65.535|1|65.54|524.29|
|65.535|30|2.18|17.48|
|65.535|60|1.09|8.74|
|65.535|90|.73|5.83|
|65.535|120|.55|4.37|

Se i pacchetti vengono persi, la velocità effettiva massima di una connessione TCP verrà ridotta mentre il mittente ritrasmette i dati già inviati.

#### <a name="tcp-window-scaling"></a>Ridimensionamento delle finestre TCP

Il ridimensionamento della finestra TCP è una tecnica che aumenta dinamicamente le dimensioni della finestra TCP per consentire l'invio di più dati prima che venga richiesto un riconoscimento. Nell'esempio precedente, 45 pacchetti verrebbero inviati prima che fosse necessario un riconoscimento. Se si aumenta il numero di pacchetti che possono essere inviati prima che sia necessaria una conferma, si riduce il numero di volte in cui un mittente è in attesa di conferma, aumentando la velocità effettiva massima TCP.

Questa tabella illustra tali relazioni:

| | | | |
|-|-|-|-|
|**Dimensioni finestra TCP (byte)**|**Latenza RTT (ms)**|**Velocità effettiva massima di megabyte/secondo**|**Velocità effettiva media megabit/secondo**|
|65.535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Ma il valore dell'intestazione TCP per la dimensione della finestra TCP è lungo solo 2 byte, il che significa che il valore massimo per una finestra di ricezione è 65.535. Per aumentare le dimensioni massime della finestra, è stato introdotto un fattore di scala della finestra TCP.

Il fattore di scala è anche un'impostazione che è possibile configurare in un sistema operativo. Ecco la formula per calcolare le dimensioni della finestra TCP utilizzando i fattori di scala:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Di seguito è riportato il calcolo per un fattore di scala della finestra pari a 3 e una dimensione della finestra di 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Un fattore di scala pari a 14 determina una dimensione della finestra TCP pari a 14 (offset massimo consentito). La dimensione della finestra TCP sarà 1.073.725.440 byte (8,5 gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Supporto per il ridimensionamento delle finestre TCPSupport for TCP window scaling

Windows può impostare diversi fattori di ridimensionamento per diversi tipi di connessione. Le classi di connessioni includono data center, Internet e così via. Utilizzare il `Get-NetTCPConnection` comando PowerShell per visualizzare il tipo di connessione di ridimensionamento della finestra:You use the PowerShell command to view the window scaling connection type:

```powershell
Get-NetTCPConnection
```

È possibile `Get-NetTCPSetting` usare il comando PowerShell per visualizzare i valori di ogni classe:You can use the PowerShell command to view the values of each class:

```powershell
Get-NetTCPSetting
```

È possibile impostare le dimensioni iniziali della finestra `Set-NetTCPSetting` TCP e il fattore di scala TCP in Windows usando il comando PowerShell.You can set the initial TCP window size and TCP scaling factor in Windows by using the PowerShell command. Per ulteriori informazioni, vedere [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Queste sono le impostazioni `AutoTuningLevel`TCP efficaci per:

| | | | |
|-|-|-|-|
|**Regolazione automaticaLevel**|**Fattore di scala**|**Moltiplicatore di scalabilità**|**Formula<br/>per calcolare le dimensioni massime della finestra**|
|Disabled|nessuno|nessuno|Dimensioni finestra|
|Con restrizioni|4|4 4|Dimensione della finestra: 2x4)|
|Altamente limitato|2|2n2|Dimensione della finestra: 2/2)|
|Normale|8|28 %amp;|Dimensione della finestra: (2-8)|
|Sperimentale|14|14 X18|Dimensione della finestra: 2/14|

Queste impostazioni sono le più propense a influire sulle prestazioni TCP, ma tenere presente che molti altri fattori in Internet, al di fuori del controllo di Azure, possono anche influire sulle prestazioni TCP.

#### <a name="increase-mtu-size"></a>Aumentare le dimensioni MTU

Poiché un MTU più grande significa un MSS più grande, ci si potrebbe chiedere se l'aumento dell'MTU può aumentare le prestazioni TCP. La risposta è probabilmente negativa. Ci sono pro e contro per la dimensione del pacchetto oltre il solo traffico TCP. Come illustrato in precedenza, i fattori più importanti che influiscono sulle prestazioni di velocità effettiva TCP sono le dimensioni della finestra TCP, la perdita di pacchetti e RTT.

> [!IMPORTANT]
> Non è consigliabile che i clienti di Azure modifichino il valore MTU predefinito nelle macchine virtuali.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Rete accelerata e scalabilità lato ricezione

#### <a name="accelerated-networking"></a>Rete accelerata

Le funzioni di rete delle macchine virtuali sono state storicamente un utilizzo intensivo della CPU sia nella macchina virtuale guest che nell'hypervisor/host. Ogni pacchetto che transita attraverso l'host viene elaborato in software dalla CPU host, inclusi tutti gli incapsulamenti e i decapsulamenti della rete virtuale. Quindi, maggiore è il traffico che passa attraverso l'host, maggiore è il carico della CPU. E se la CPU host è occupata con altre operazioni, che influenzerà anche la velocità effettiva della rete e la latenza. Azure risolve questo problema con una rete accelerata.

La rete accelerata fornisce una latenza di rete ultrabassa costante tramite l'hardware programmabile interno di Azure e tecnologie come SR-IOV. La rete accelerata sposta gran parte dello stack di rete definito dal software di Azure dalle CPU e dalle SmartNIC basate su FPGA. Questa modifica consente alle applicazioni per l'utente finale di recuperare i cicli di calcolo, il che comporta meno carico nella macchina virtuale, riducendo il jitter e l'incoerenza di latenza. In altre parole, le prestazioni possono essere più deterministiche.

La rete accelerata migliora le prestazioni consentendo alla macchina virtuale guest di ignorare l'host e stabilire un percorso dati direttamente con SmartNIC di un host. Ecco alcuni vantaggi della rete accelerata:

- **Latenza inferiore / pacchetti superiori al secondo (pps):** la rimozione del commutatore virtuale dal percorso dati elimina il tempo impiegato dai pacchetti nell'host per l'elaborazione dei criteri e aumenta il numero di pacchetti che possono essere elaborati nella macchina virtuale.

- **Instabilità ridotta:** l'elaborazione del commutatore virtuale dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. L'offload dell'applicazione dei criteri all'hardware rimuove tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, eliminando la comunicazione da host a VM e tutti gli interrupt software e i cambi di contesto.

- **Riduzione dell'utilizzo della CPU**: L'esclusione del commutatore virtuale nell'host comporta un minore utilizzo della CPU per l'elaborazione del traffico di rete.

Per usare una rete accelerata, è necessario abilitarla in modo esplicito in ogni macchina virtuale applicabile. Per istruzioni, vedere [Creare una macchina virtuale Linux con la rete accelerata.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

#### <a name="receive-side-scaling"></a>Ridimensionamento lato ricezione

Receive Side Scaling (RSS) è una tecnologia di driver di rete che distribuisce la ricezione del traffico di rete in modo più efficiente distribuendo l'elaborazione delle ricevute tra più CPU in un sistema multiprocessore. In termini semplici, RSS consente a un sistema di elaborare più traffico ricevuto perché utilizza tutte le CPU disponibili invece di una sola. Per una descrizione più tecnica di RSS, vedere [Introduzione per ricevere il ridimensionamento laterale](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Per ottenere le migliori prestazioni quando la rete accelerata è abilitata in una macchina virtuale, è necessario abilitare RSS. RSS può anche offrire vantaggi nelle macchine virtuali che non usano reti accelerate. Per una panoramica su come determinare se RSS è abilitato e su come abilitarlo, vedere [Ottimizzare la velocità effettiva](https://aka.ms/FastVM)di rete per le macchine virtuali di Azure.

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TIME_WAIT TCP e TIME_WAIT assassinio

Il TIME_WAIT TCP è un'altra impostazione comune che influisce sulle prestazioni della rete e dell'applicazione. Nelle macchine virtuali occupate che aprono e chiudono molti socket, come client o come server (Source IP:Source Port - Destination IP:Destination Port), durante il normale funzionamento di TCP, un determinato socket può finire in uno stato TIME_WAIT per molto tempo. Lo stato TIME_WAIT ha lo scopo di consentire il recapito di dati aggiuntivi su un socket prima di chiuderlo. Gli stack TCP/IP in genere impediscono il riutilizzo di un socket eliminando automaticamente il pacchetto TCP SYN del client.

Quantità di tempo per cui un socket è in TIME_WAIT è configurabile. Potrebbe variare da 30 secondi a 240 secondi. I socket sono una risorsa limitata e il numero di socket che possono essere utilizzati in qualsiasi momento è configurabile. (Il numero di socket disponibili è in genere di circa 30.000.) Se i socket disponibili vengono utilizzati o se i client e i server dispongono di impostazioni di TIME_WAIT non corrispondenti e una macchina virtuale tenta di riutilizzare un socket in uno stato di TIME_WAIT, le nuove connessioni avranno esito negativo in quanto i pacchetti TCP SYN vengono eliminati automaticamente.

Il valore per l'intervallo di porte per i socket in uscita è in genere configurabile all'interno dello stack TCP/IP di un sistema operativo. La stessa cosa vale per le impostazioni di TIME_WAIT TCP e il riutilizzo dei socket. La modifica di questi numeri può potenzialmente migliorare la scalabilità. Tuttavia, a seconda della situazione, queste modifiche potrebbero causare problemi di interoperabilità. È necessario prestare attenzione se si modificano questi valori.

È possibile utilizzare TIME_WAIT'attrattiva per risolvere questa limitazione di scalabilità. TIME_WAIT'assassinio consente di riutilizzare una presa in determinate situazioni, ad esempio quando il numero di sequenza nel pacchetto IP della nuova connessione supera il numero di sequenza dell'ultimo pacchetto dalla connessione precedente. In questo caso, il sistema operativo consentirà di stabilire la nuova connessione (accetterà il nuovo SYN/ACK) e forzerà chiudere la connessione precedente che si trovava in uno stato di TIME_WAIT. Questa funzionalità è supportata nelle macchine virtuali Windows in Azure.This capability is supported on Windows VMs in Azure. Per informazioni sul supporto in altre macchine virtuali, rivolgersi al fornitore del sistema operativo.

Per informazioni sulla configurazione delle impostazioni di TIME_WAIT TCP e dell'intervallo di porte di origine, vedere Impostazioni che possono essere modificate per migliorare le prestazioni di [rete.](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fattori di rete virtuale che possono influire sulle prestazioniVirtual network factors that can affect performance

### <a name="vm-maximum-outbound-throughput"></a>Velocità effettiva massima in uscita della macchina virtualeVM maximum outbound throughput

Azure offre un'ampia gamma di dimensioni e tipi di macchine virtuali, ognuno con una combinazione diversa di funzionalità di prestazioni. Una di queste funzionalità è la velocità effettiva di rete (o larghezza di banda), misurata in megabit al secondo (Mbps). Poiché le macchine virtuali sono ospitate su hardware condiviso, la capacità di rete deve essere condivisa equamente tra le macchine virtuali che utilizzano lo stesso hardware. Alle macchine virtuali più grandi viene allocata una larghezza di banda maggiore rispetto alle macchine virtuali più piccole.

La larghezza di banda di rete allocata a ogni macchina virtuale è misurata sul traffico in uscita dalla macchina virtuale. Tutto il traffico di rete che esce dalla macchina virtuale viene conteggiato per il limite allocato, indipendentemente dalla destinazione. For example, if a virtual machine has a 1,000-Mbps limit, that limit applies whether the outbound traffic is destined for another virtual machine in the same virtual network or one outside of Azure.

Il traffico in ingresso non viene misurato o limitato direttamente. Esistono tuttavia altri fattori, ad esempio i limiti di CPU e archiviazione, che possono influire sulla capacità di una macchina virtuale di elaborare i dati in ingresso.

La rete accelerata è progettata per migliorare le prestazioni della rete, tra cui latenza, velocità effettiva e utilizzo della CPU. La rete accelerata può migliorare la velocità effettiva di una macchina virtuale, ma può farlo solo fino alla larghezza di banda allocata della macchina virtuale.

Le macchine virtuali di Azure hanno almeno un'interfaccia di rete collegata. Potrebbero averne diversi. La larghezza di banda allocata a una macchina virtuale è la somma di tutto il traffico in uscita tra tutte le interfacce di rete collegate alla macchina. In altre parole, la larghezza di banda viene allocata in base alla macchina virtuale, indipendentemente dal numero di interfacce di rete collegate alla macchina.

La velocità effettiva in uscita prevista e il numero di interfacce di rete supportate da ogni dimensione di macchina virtuale sono descritti in dettaglio in [Dimensioni per le macchine virtuali Windows in Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Per visualizzare la velocità effettiva massima, selezionare un tipo, ad **esempio Uso generale**, quindi individuare la sezione relativa alla serie di dimensioni nella pagina risultante (ad esempio, "Serie Dv2"). Per ogni serie, c'è una tabella che fornisce le specifiche di rete nell'ultima colonna, che si intitola "Max NICs / Larghezza di banda di rete prevista (Mbps)."

Il limite di velocità effettiva si applica alla macchina virtuale. La velocità effettiva non è influenzata da questi fattori:Throughput is not affected by these factors:

- **Numero di interfacce di rete:** il limite di larghezza di banda si applica alla somma di tutto il traffico in uscita dalla macchina virtuale.

- **Rete accelerata**: Anche se questa funzionalità può essere utile per raggiungere il limite pubblicato, non cambia il limite.

- **Destinazione del traffico**: tutte le destinazioni contano per il limite in uscita.

- **Protocollo**: tutto il traffico in uscita su tutti i protocolli conta per il limite.

Per ulteriori informazioni, vedere Larghezza di banda della [rete della macchina virtuale](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerazioni sulle prestazioni Internet

Come illustrato in questo articolo, i fattori su Internet e al di fuori del controllo di Azure possono influire sulle prestazioni della rete. Ecco alcuni di questi fattori:

- **Latenza :** Il tempo di andata e ritorno tra due destinazioni può essere influenzato da problemi su reti intermedie, dal traffico che non prende il percorso di distanza "più breve" e da percorsi di peering non ottimali.

- **Perdita di pacchetti:** la perdita di pacchetti può essere causata da congestione della rete, problemi di percorso fisico e dispositivi di rete con prestazioni insufficienti.

- **Dimensione/Frammentazione MTU**: La frammentazione lungo il percorso può causare ritardi nell'arrivo dei dati o in pacchetti che arrivano fuori ordine, che possono influire sulla consegna dei pacchetti.

Traceroute è un buon strumento per misurare le caratteristiche delle prestazioni di rete (ad esempio la perdita di pacchetti e la latenza) lungo ogni percorso di rete tra un dispositivo di origine e un dispositivo di destinazione.

### <a name="network-design-considerations"></a>Considerazioni sulla progettazione della rete

Oltre alle considerazioni descritte in precedenza in questo articolo, la topologia di una rete virtuale può influire sulle prestazioni della rete. Ad esempio, una progettazione hub-and-spoke che esegue il backhaul del traffico a livello globale verso una rete virtuale a hub singolo introdurrà la latenza di rete, che influirà sulle prestazioni complessive della rete.

Il numero di dispositivi di rete attraverso i quali passa il traffico di rete può influire anche sulla latenza complessiva. Ad esempio, in una progettazione hub-and-spoke, se il traffico passa attraverso un'appliance virtuale di rete spoke e un'appliance virtuale hub prima di transitare verso Internet, le appliance virtuali di rete possono introdurre latenza.

### <a name="azure-regions-virtual-networks-and-latency"></a>Aree di Azure, reti virtuali e latenzaAzure regions, virtual networks, and latency

Le aree di Azure sono costituite da più data center presenti all'interno di un'area geografica generale. Questi data center potrebbero non essere fisicamente uno accanto all'altro. In alcuni casi sono separati da ben 10 chilometri. La rete virtuale è una sovrapposizione logica in cima alla rete del data center fisico di Azure.The virtual network is a logical overlay on the Azure physical datacenter network. Una rete virtuale non implica alcuna topologia di rete specifica all'interno del data center.

Ad esempio, due macchine virtuali che si trovano nella stessa rete virtuale e subnet potrebbero trovarsi in rack, righe o persino data center diversi. Potrebbero essere separati da piedi di cavo in fibra ottica o da chilometri di cavo in fibra ottica. Questa variazione potrebbe introdurre latenza variabile (differenza di pochi millisecondi) tra macchine virtuali diverse.

Il posizionamento geografico delle macchine virtuali e la potenziale latenza risultante tra due macchine virtuali possono essere influenzati dalla configurazione dei set di disponibilità e delle zone di disponibilità. Tuttavia, la distanza tra i data center in un'area è specifica dell'area e influisce principalmente sulla topologia del data center nell'area.

### <a name="source-nat-port-exhaustion"></a>Esaurimento della porta NAT di origine

Una distribuzione in Azure può comunicare con endpoint esterni ad Azure in Internet pubblico e/o nello spazio IP pubblico. Quando un'istanza avvia una connessione in uscita, Azure esegue dinamicamente il mapping dell'indirizzo IP privato a un indirizzo IP pubblico. Dopo aver creato questo mapping, restituire il traffico per il flusso originato in uscita può raggiungere anche l'indirizzo IP privato da cui ha avuto origine il flusso.

Per ogni connessione in uscita, Azure Load Balancer deve mantenere questo mapping per un certo periodo di tempo. Con la natura multi-tenant di Azure, mantenere questo mapping per ogni flusso in uscita per ogni macchina virtuale può richiedere un utilizzo intensivo delle risorse. Esistono quindi limiti impostati e basati sulla configurazione della rete virtuale di Azure.So there are limits that are set and based on the configuration of the Azure Virtual Network. In alternativa, per dire più precisamente, una macchina virtuale di Azure può effettuare solo un determinato numero di connessioni in uscita in un determinato momento. Quando vengono raggiunti questi limiti, la macchina virtuale non sarà in grado di effettuare più connessioni in uscita.

Ma questo comportamento è configurabile. Per ulteriori informazioni sull'esaurimento delle porte SNAT e SNAT, vedere [questo articolo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Misurare le prestazioni di rete in AzureMeasure network performance on Azure

Un numero di valori massimi di prestazioni in questo articolo sono correlati alla latenza di rete / tempo di andata e ritorno (RTT) tra due macchine virtuali. In questa sezione vengono forniti alcuni suggerimenti su come testare la latenza/RTT e su come testare le prestazioni TCP e le prestazioni della rete VM. È possibile ottimizzare e testare le prestazioni tcp/IP e i valori di rete descritti in precedenza utilizzando le tecniche descritte in questa sezione. È possibile inserire i valori di latenza, MTU, MSS e dimensioni della finestra nei calcoli forniti in precedenza e confrontare i valori massimi teorici con i valori effettivi osservati durante il test.

### <a name="measure-round-trip-time-and-packet-loss"></a>Misurare il tempo di andata e ritorno e la perdita di pacchetti

Le prestazioni TCP si basano fortemente su RTT e perdita di pacchetti. L'utilità PING disponibile in Windows e Linux fornisce il modo più semplice per misurare RTT e perdita di pacchetti. L'output di PING mostrerà la latenza minima/massima/media tra un'origine e una destinazione. Mostrerà anche la perdita di pacchetti. PING utilizza il protocollo ICMP per impostazione predefinita. È possibile utilizzare PsPing per testare TCP RTT. Per ulteriori informazioni, vedere [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Misurare la velocità effettiva di una connessione TCPMeasure actual throughput of a TCP connection

NTttcp is a tool for testing the TCP performance of a Linux or Windows VM. È possibile modificare varie impostazioni TCP e quindi verificare i vantaggi utilizzando NTttcp. Per ulteriori informazioni, vedere le risorse:

- [Test di larghezza di banda/velocità effettiva (NTttcp)Bandwidth/Throughput testing (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Utilità NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Misurare la larghezza di banda effettiva di una macchina virtualeMeasure actual bandwidth of a virtual machine

È possibile testare le prestazioni di diversi tipi di macchine virtuali, rete accelerata e così via, usando uno strumento denominato iPerf.You can test the performance of different VM types, accelerated networking, and so on, by using a tool called iPerf. iPerf è disponibile anche su Linux e Windows. iPerf può usare TCP o UDP per testare la velocità effettiva complessiva della rete. I test di velocità effettiva TCP di iPerf sono influenzati dai fattori descritti in questo articolo (ad esempio latenza e RTT). Pertanto UDP potrebbe produrre risultati migliori se si desidera solo testare la velocità effettiva massima.

Per altre informazioni, vedere questi articoli:

- [Risoluzione dei problemi relativi alle prestazioni di rete ExpressrouteTroubleshooting Expressroute network performance](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Come convalidare la velocità effettiva della VPN verso una rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Rilevare comportamenti TCP inefficienti

Nelle acquisizioni di pacchetti, i clienti di Azure potrebbero visualizzare pacchetti TCP con flag TCP (SACK, DUP ACK, RETRANSMIT e FAST RETRANSMIT) che potrebbero indicare problemi di prestazioni di rete. Questi pacchetti indicano in modo specifico le inefficienze di rete derivanti dalla perdita di pacchetti. Ma la perdita di pacchetti non è necessariamente causata da problemi di prestazioni di Azure.But packet loss isn't necessarily caused by Azure performance problems. Problemi di prestazioni potrebbero essere il risultato di problemi dell'applicazione, problemi del sistema operativo o altri problemi che potrebbero non essere direttamente correlati alla piattaforma Azure.Performance problems could be the result of application problems, operating system problems, or other problems that might not be directly related to the Azure platform.

Inoltre, tenere presente che alcune ritrasmissioni e ACK duplicati sono normali in una rete. I protocolli TCP sono stati creati per essere affidabili. La prova di questi pacchetti TCP in un'acquisizione di pacchetti non indica necessariamente un problema di rete sistemica, a meno che non siano eccessivi.

Tuttavia, questi tipi di pacchetti indicano che la velocità effettiva TCP non raggiunge le massime prestazioni, per motivi illustrati in altre sezioni di questo articolo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso l'ottimizzazione delle prestazioni TCP/IP per le macchine virtuali di Azure, è possibile leggere altre considerazioni per [la pianificazione](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) delle reti virtuali o altre informazioni sulla connessione e la configurazione delle reti [virtuali.](https://docs.microsoft.com/azure/virtual-network/)
