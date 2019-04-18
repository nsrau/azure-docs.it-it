---
title: Ottimizzazione delle prestazioni TCP/IP per macchine virtuali di Azure | Microsoft Docs
description: Informazioni su varie comuni TCP/IP prestazioni tecniche di ottimizzazione e la loro relazione con macchine virtuali di Azure.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 1e8605a41cbe610c971b891309b2149d221b8b27
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426444"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP ottimizzazione delle prestazioni per macchine virtuali di Azure

Questo articolo illustra i più comuni tecniche di ottimizzazione delle prestazioni TCP/IP e alcuni aspetti da considerare quando vengono utilizzati per le macchine virtuali in esecuzione in Azure. Verrà fornita una panoramica delle tecniche di base ed esplorare modo in cui possono essere ottimizzate.

## <a name="common-tcpip-tuning-techniques"></a>Comuni tecniche di ottimizzazione TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>Il valore MTU, la frammentazione e l'offload invio di grandi dimensioni

#### <a name="mtu"></a>MTU

L'unità di trasmissione massima (MTU) è il frame di dimensioni più grande (pacchetto), espresso in byte, che possono essere inviati tramite un'interfaccia di rete. Il valore MTU è un'impostazione configurabile. Il valore predefinito utilizzato MTU nelle VM di Azure e l'impostazione predefinita nella maggior parte dei dispositivi di rete è a livello globale, 1.500 byte.

#### <a name="fragmentation"></a>Frammentazione

La frammentazione si verifica quando viene inviato un pacchetto che supera il valore MTU di un'interfaccia di rete. Lo stack TCP/IP interromperà il pacchetto in parti più piccole (frammenti) conformi a MTU dell'interfaccia. La frammentazione si verifica a livello di IP ed è indipendente dal protocollo sottostante (ad esempio, TCP). Quando viene inviato un pacchetto di 2.000 byte su un'interfaccia di rete con un valore MTU di 1.500, i pacchetti verranno suddivisi in un pacchetto di 1.500 byte e un pacchetto di 500 byte.

I dispositivi di rete nel percorso tra origine e destinazione possono entrambi i pacchetti di rilascio che superano il valore MTU o frammentazione del pacchetto in parti più piccole.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>I Don ' t Fragment in bit in un pacchetto IP

Il bit non frammento (DF) è un flag nell'intestazione del protocollo IP. Il bit DF indica che i dispositivi di rete nel percorso tra il mittente e ricevitore non necessario di frammentazione del pacchetto. Questo bit è possibile impostare per diversi motivi. (Vedere la sezione "Individuazione MTU di percorso" di questo articolo per un esempio). Quando un dispositivo di rete riceve un pacchetto con il set di bit Don ' t Fragment, e tale pacchetto supera il valore MTU dell'interfaccia del dispositivo, il comportamento standard è per il dispositivo eliminare il pacchetto. Il dispositivo invia un messaggio ICMP frammentazione necessaria nell'origine del pacchetto originale.

#### <a name="performance-implications-of-fragmentation"></a>Implicazioni sulle prestazioni della frammentazione

La frammentazione può avere impatto negativo sulle prestazioni. Uno dei motivi principali per l'effetto sulle prestazioni è l'impatto di CPU/memoria della frammentazione e il riassemblaggio dei pacchetti. Quando un dispositivo di rete deve la frammentazione di un pacchetto, dovrà allocare le risorse di CPU/memoria per l'esecuzione della frammentazione.

La stessa cosa accade quando il pacchetto viene riassemblato. Il dispositivo di rete deve archiviare tutti i frammenti, fino a quando non vengono ricevuti in modo che è possibile riassemblarle nel pacchetto originale. Questo processo di frammentazione e il riassemblaggio può provocare latenza.

L'altra implicazione possibili negativamente sulle prestazioni di frammentazione è che i pacchetti frammentati potrebbero arrivare non in ordine. Quando i pacchetti vengono ricevuti nell'ordine errato, alcuni tipi di dispositivi di rete possano rilasciarli. In questo caso, l'intero pacchetto deve essere ritrasmesso.

I frammenti vengono in genere eliminati dai dispositivi di sicurezza, ad esempio i firewall di rete o quando un dispositivo di rete di ricezione vengono esauriti i buffer. Quando un dispositivo di rete di ricezione vengono esauriti i buffer, un dispositivo di rete sta tentando di riassemblare un pacchetto frammentato, ma non dispone di risorse per archiviare e reassume il pacchetto.

La frammentazione può essere considerata un'operazione negativi, ma il supporto per la frammentazione è necessaria quando ci si connette a reti diverse tramite internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Vantaggi e conseguenze della modifica il valore MTU

In generale, è possibile creare una rete più efficiente se si aumenta il valore MTU. Tutti i pacchetti che vengono trasmessi dispone di informazioni di intestazione che viene aggiunto al pacchetto originale. Quando la frammentazione crea più pacchetti, intestazione ulteriori overhead e che rende la rete meno efficiente.

Di seguito è riportato un esempio. La dimensione di intestazione Ethernet è di 14 byte oltre a una sequenza di controllo frame a 4 byte per garantire la coerenza di frame. Se viene inviato un pacchetto di 2.000 byte, 18 byte di overhead Ethernet viene aggiunto nella rete. Se il pacchetto viene frammentato in un pacchetto di 1.500 byte e un pacchetto di 500 byte, ogni pacchetto avrà 18 byte dell'intestazione Ethernet, un totale di 36 byte.

Tenere presente che ad aumentare il valore MTU non crea necessariamente una rete più efficiente. Se un'applicazione invia solo i pacchetti a 500 byte, l'overhead dell'intestazione stessa esisteranno sia il valore MTU rappresenti 1.500 byte 9.000 byte. La rete diventa più efficiente solo se utilizza più grandi dimensioni dei pacchetti che sono interessati dal valore MTU.

#### <a name="azure-and-vm-mtu"></a>Azure e il valore MTU macchina virtuale

Il valore predefinito di MTU per macchine virtuali di Azure è di 1.500 byte. Lo stack di rete virtuale di Azure tenterà di frammentazione di un pacchetto in byte 1400. Ma lo stack di rete virtuale consente pacchetti fino a 2,006 byte quando è impostato il bit di Don ' t Fragment nell'intestazione IP.

Si noti che lo stack di rete virtuale non è intrinsecamente inefficiente, perché frammenti pacchetti a livello 1.400 byte anche se le macchine virtuali hanno un valore MTU di 1.500. Un'alta percentuale di pacchetti di rete sono molto inferiori a 1.400 o 1.500 byte.

#### <a name="azure-and-fragmentation"></a>Azure e sulla frammentazione

Stack di rete virtuale è configurato per eliminare "frammenti non in ordine," pacchetti frammentati, che non arrivano in base all'ordine originale frammentati. Questi pacchetti vengono eliminati principalmente a causa di una vulnerabilità di sicurezza di rete ha annunciata nel mese di novembre 2018 chiamato FragmentSmack.

FragmentSmack è un difetto in modo il kernel Linux gestiti del riassemblaggio dei pacchetti frammentati IPv4 e IPv6. Un utente malintenzionato remoto è stato possibile usare questo difetto alle operazioni riassemblaggio dei trigger frammento costosa, che potrebbe causare un attacco denial of service e un aumento della CPU nel sistema di destinazione.

#### <a name="tune-the-mtu"></a>Ottimizzare il valore MTU

È possibile configurare un valore MTU macchina virtuale di Azure, come in qualsiasi altro sistema operativo. Ma è consigliabile considerare la frammentazione che si verifica in Azure, descritta in precedenza, quando si configura un valore MTU.

I clienti per aumentare la MTU di macchina virtuale non sono invitati. Questa discussione è lo scopo di descrivere i dettagli su come Azure implementa il valore MTU ed esegue la frammentazione.

> [!IMPORTANT]
>Aumentando il valore MTU non è noto per migliorare le prestazioni e potrebbe avere un effetto negativo sulle prestazioni dell'applicazione.
>
>

#### <a name="large-send-offload"></a>Offload invio di grandi dimensioni

Offload invio di grandi dimensioni (LSO) può migliorare le prestazioni di rete tramite l'offload di segmentazione dei pacchetti per la scheda Ethernet. Quando è abilitato LSO, lo stack TCP/IP consente di creare un pacchetto TCP di grandi dimensioni e lo invia alla scheda Ethernet per la segmentazione prima di inoltrarlo. Il vantaggio di LSO è che sia possibile liberare le CPU da segmentando i pacchetti in dimensioni che sono conformi al valore MTU e che l'elaborazione per l'interfaccia Ethernet in cui viene eseguito in hardware offload. Per altre informazioni sui vantaggi di LSO, vedere [che supportano large send offload](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Quando LSO è abilitata, i clienti di Azure possono visualizzare le dimensioni di frame di grandi dimensioni quando eseguono le acquisizioni di pacchetti. Queste dimensioni di frame di grandi dimensioni potrebbero comportare alcuni clienti a individuare la frammentazione è in corso o che una MTU di grandi dimensioni viene utilizzato quando non è. Con LSO, la scheda Ethernet può annunciare una dimensione massima del segmento maggiore (MSS) allo stack TCP/IP per creare un pacchetto TCP più grande. Questo frame non segmentato intero viene quindi inoltrato alla scheda Ethernet e dovrebbe essere visibile in un'acquisizione di pacchetti eseguita sulla macchina virtuale. Ma il pacchetto verrà suddiviso in più piccolo numero di frame per la scheda Ethernet, in base al valore MTU dell'adattatore Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Adattamento della finestra TCP MSS e PMTUD

#### <a name="tcp-maximum-segment-size"></a>Dimensione massima del segmento TCP

Dimensione massima del segmento TCP (MSS Maximum) è un'impostazione che limita la dimensione dei segmenti TCP, che consente di evitare la frammentazione dei pacchetti TCP. Sistemi operativi in genere utilizzerà questa formula per impostare MSS:

`MSS = MTU - (IP header size + TCP header size)`

L'intestazione IP e l'intestazione TCP sono 20 byte o 40 byte totali. Pertanto, un'interfaccia con un valore MTU di 1.500 avrà una MSS di 1,460. Ma il MSS è configurabile.

Questa impostazione è concordata per l'handshake a tre vie TCP quando una sessione TCP viene configurata tra un'origine e destinazione. Entrambi i lati inviare un valore MSS, e il basso dei due viene usato per la connessione TCP.

Tenere presente che il MTU dell'origine e destinazione non sono gli unici fattori che determinano il valore MSS. Dispositivi di rete intermedi, come i gateway VPN, incluso il Gateway VPN di Azure, è possono modificare il valore MTU indipendentemente dall'origine e destinazione per garantire prestazioni ottimali della rete.

#### <a name="path-mtu-discovery"></a>Rilevamento del percorso

Viene negoziato MSS, ma potrebbe non indicare il MSS effettivo che può essere utilizzato. Questo avviene perché altri dispositivi di rete nel percorso tra l'origine e destinazione potrebbero essere un valore MTU inferiore di origine e destinazione. In questo caso, il dispositivo è inferiore rispetto al pacchetto il cui valore MTU eliminerà il pacchetto. Il dispositivo invierà nuovamente un messaggio ICMP frammentazione necessaria (tipo 3, 4 codice) che contiene il valore MTU. Questo messaggio ICMP consente all'host di origine per ridurre in modo appropriato il relativo percorso MTU. Il processo è denominato percorso MTU individuazione (PMTUD).

Il processo PMTUD è inefficiente e influisce sulle prestazioni di rete. L'invio di pacchetti che superano il valore MTU del percorso di rete, i pacchetti devono essere ritrasmesso con un MSS inferiore. Se il server sender non può ricevere il messaggio ICMP frammentazione necessaria, forse a causa di un firewall di rete nel percorso (noto come un *blackhole PMTUD*), il mittente non sa necessarie a ridurre la MSS, in modo continuo lo trasmetta di nuovo il pacchetto. Ecco perché non è consigliabile aumentare il valore MTU macchina virtuale di Azure.

#### <a name="vpn-and-mtu"></a>Il valore MTU e VPN

Se si usano macchine virtuali che eseguono l'incapsulamento (ad esempio VPN IPsec), esistono alcune considerazioni aggiuntive relative alle dimensioni del pacchetto e il valore MTU. Le VPN aggiungere altre intestazioni di pacchetti, che aumenta le dimensioni del pacchetto e richiede un MSS più piccoli.

Per Azure, è consigliabile impostare fissaggio TCP MSS a byte a 1.350 e tunneling valore MTU dell'interfaccia a 1.400. Per altre informazioni, vedere la [VPN pagina parametri IPSec/IKE e dispositivi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latenza, tempo di round trip e adattamento della finestra TCP

#### <a name="latency-and-round-trip-time"></a>Tempo di round trip e la latenza

Latenza di rete è regolata dalla velocità della luce in fibra ottica della rete fiber. Velocità effettiva della rete di TCP anche in modo efficace è disciplinato dal tempo di round trip (RTT) tra due dispositivi di rete.

| | | | |
|-|-|-|-|
|**Route**|**distanza**|**Tempo unidirezionale**|**RTT**|
|New York a San Francisco|4,148 km|21 ms|42 ms|
|New York a Londra|5,585 km|28 ms|56 ms|
|New York to Sydney|15,993 km|80 ms|160 ms|

Questa tabella mostra la distanza tra due posizioni linea d'aria. Nelle reti, la distanza è in genere più tempo la distanza della linea di tendenza. Ecco una semplice formula per calcolare RTT minimo come disciplinato dalla velocità della luce:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

È possibile usare 200 per la velocità di propagazione. Questa è la distanza in metri, che viene trasferito luce in 1 millisecondo.

Passiamo a New York a San Francisco come esempio. La distanza della linea di tendenza è 4,148 km. Collegare tale valore nell'equazione, si ottiene quanto segue:

`Minimum RTT = 2 * (4,148 / 200)`

L'output dell'equazione è espresso in millisecondi.

Se si desidera ottenere le migliori prestazioni di rete, la logica consiste nel selezionare le destinazioni con la distanza più breve tra di essi. È inoltre possibile progettare la rete virtuale per ottimizzare il percorso del traffico e ridurre la latenza. Per altre informazioni, vedere la sezione "Considerazioni sulla progettazione di rete" di questo articolo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Effetti di tempo di round trip e la latenza sul protocollo TCP

Tempo di round trip ha un effetto diretto sulla velocità effettiva massima di TCP. Nel protocollo TCP *dimensioni finestra* è la quantità massima di traffico che può essere inviato tramite una connessione TCP prima che il mittente deve ricevere acknowledgement dal destinatario. Se il valore MSS TCP è impostata su 1,460 e le dimensioni della finestra TCP sono impostata su 65.535, il mittente può inviare 45 pacchetti prima che debba ricevere acknowledgement dal destinatario. Se il mittente non riceve l'acknowledgement, sarà lo trasmetta di nuovo i dati. Ecco la formula:

`TCP window size / TCP MSS = packets sent`

In questo esempio, 65.535 / 1,460 viene arrotondato fino a 45.

Questo stato "in attesa di riconoscimento", un meccanismo per garantire il recapito affidabile dei dati, è ciò che fa sì che RTT influire sulla velocità effettiva TCP. Più a lungo in attesa di riconoscimento al mittente, il tempo deve attendere prima di inviare più dati.

Ecco la formula per calcolare la velocità effettiva massima di una singola connessione TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Questa tabella mostra i numero massimi megabyte o al secondo di velocità effettiva di una singola connessione TCP. (Per una migliore leggibilità, megabyte viene usato per l'unità di misura).

| | | | |
|-|-|-|-|
|**Dimensioni della finestra TCP (byte)**|**Latenza RTT (ms)**|**Massimo megabyte al secondo di velocità effettiva**|**Massimo megabit al secondo di velocità effettiva**|
|65.535|1|65.54|524.29|
|65.535|30|2.18|17.48|
|65.535|60|1.09|8.74|
|65.535|90|.73|5.83|
|65.535|120|.55|4.37|

Se viene perso, mentre il server sender ritrasmette dati che ha già inviato verrà ridotta la velocità effettiva massima di una connessione TCP.

#### <a name="tcp-window-scaling"></a>Adattamento della finestra TCP

Adattamento della finestra TCP è una tecnica che aumenta in modo dinamico le dimensioni della finestra TCP per consentire più dati da inviare prima che venga richiesta una conferma. Nell'esempio precedente, i 45 pacchetti verrebbe inviati prima che fosse necessario un acknowledgement. Se si aumenta il numero di pacchetti che possono essere inviati prima che sia necessarie una conferma, si riducono il numero di volte in cui che un server sender è in attesa di riconoscimento, che aumenta la velocità effettiva massima di TCP.

Questa tabella illustra le relazioni:

| | | | |
|-|-|-|-|
|**Dimensioni della finestra TCP (byte)**|**Latenza RTT (ms)**|**Massimo megabyte al secondo di velocità effettiva**|**Massimo megabit al secondo di velocità effettiva**|
|65.535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Ma il valore dell'intestazione TCP per la dimensione della finestra TCP è solo a 2 byte, ovvero che il valore massimo per una finestra di ricezione è 65.535. Per aumentare la dimensione massima della finestra, è stato introdotto un fattore di scala finestra TCP.

Il fattore di scala è anche un'impostazione che è possibile configurare in un sistema operativo. Ecco la formula per calcolare le dimensioni della finestra TCP con fattori di scala:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Ecco il calcolo per un fattore di scala di finestra di 3 e una dimensione della finestra di 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Un fattore di scala pari a 14 comporta una dimensione della finestra TCP pari a 14 (all'offset massimo consentito). Le dimensioni della finestra TCP sarà 1,073,725,440 byte (8,5 Gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Supporto per il ridimensionamento finestra TCP

Windows è possibile impostare diversi fattori di scala per diversi tipi di connessione. (Le classi di connessioni includono Data Center, internet e così via). Si utilizza il `Get-NetTCPConnection` comando di PowerShell per visualizzare la finestra di ridimensionamento di tipo di connessione:

```powershell
Get-NetTCPConnection
```

È possibile usare il `Get-NetTCPSetting` comando di PowerShell per visualizzare i valori di ogni classe:

```powershell
Get-NetTCPSetting
```

È possibile impostare le dimensioni della finestra TCP iniziale e il fattore di scala di TCP in Windows tramite il `Set-NetTCPSetting` comando di PowerShell. Per altre informazioni, vedere [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Queste sono le impostazioni TCP efficace per `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Fattore di scala**|**Moltiplicatore di scala**|**Formula da<br/>calcolare la dimensione massima della finestra**|
|Disabled|Nessuna|Nessuna|Dimensioni della finestra|
|Con restrizioni|4|2^4|Dimensioni della finestra * (2 ^ 4)|
|Soggetto a severe limitazioni|2|2^2|Dimensioni della finestra * (2 ^ 2)|
|Normale|8|2^8|Dimensioni della finestra * (2 ^ 8)|
|Sperimentale|14|2^14|Dimensioni della finestra * (2 ^ 14)|

Queste impostazioni sono probabilmente a influire sulle prestazioni TCP, ma tenere presente che molti altri fattori attraverso la rete internet, all'esterno del controllo di Azure che possono influire sulle prestazioni TCP.

#### <a name="increase-mtu-size"></a>Aumentare la dimensione MTU

Perché una MTU più grande implica una maggiore MSS, ci si potrebbe chiedere se aumentare il valore MTU può migliorare le prestazioni di TCP. La risposta è probabilmente negativa. Esistono vantaggi e svantaggi a dimensioni del pacchetto di là solo il traffico TCP. Come illustrato in precedenza, i fattori più importanti sulle prestazioni di velocità effettiva TCP sono dimensioni della finestra TCP, perdita di pacchetti e RTT.

> [!IMPORTANT]
> Non è consigliabile che i clienti di Azure modificare il valore MTU predefinito nelle macchine virtuali.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>La funzionalità rete accelerata e receive-side scaling

#### <a name="accelerated-networking"></a>Rete accelerata

Funzioni di rete di macchine virtuali sono sempre stati della CPU con utilizzo intensiva della macchina virtuale guest sia/host dell'hypervisor. Tutti i pacchetti che passa attraverso l'host vengano elaborato nel software dall'host di CPU, comprese tutte incapsulamento di rete virtuale e decapsulamento. In modo che la maggiore quantità di traffico che passa attraverso l'host, caricare il più elevato della CPU. E se la CPU dell'host è occupata con altre operazioni, che influirà anche la latenza e velocità effettiva della rete. Azure risolve questo problema con la funzionalità rete accelerata.

La funzionalità rete accelerata fornisce la latenza di rete ultralow coerente tramite l'hardware programmabile interna di Azure e le tecnologie come SR-IOV. Gran parte di Azure definita da software dallo stack di rete esterno le CPU e in basato su FPGA SmartNICs consente di spostare la funzionalità rete accelerata. Questa modifica consente alle applicazioni dell'utente finale di recuperare i cicli di calcolo, che memorizza e ridurre il carico nella macchina virtuale, riduzione della latenza di instabilità e l'incoerenza. In altre parole, le prestazioni possono essere più deterministica.

La funzionalità rete accelerata migliora le prestazioni consentendo il guest macchina virtuale di ignorare l'host e stabilire un percorso dati direttamente con SmartNIC dell'host. Ecco alcuni dei vantaggi della funzionalità rete accelerata:

- **Latenza più bassa / più pacchetti al secondo (pps)**: Rimuovendo il commutatore virtuale dal percorso consente di eliminare il tempo speso per i pacchetti nell'host di elaborazione dei criteri e aumenta il numero di pacchetti che possono essere elaborati nella macchina virtuale.

- **Instabilità ridotta**: L'elaborazione di commutatore virtuale varia a seconda della quantità di criterio che deve essere applicato e il carico di lavoro della CPU che esegue l'elaborazione. L'applicazione dei criteri all'hardware offload rimuove tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, eliminando la comunicazione host-per-VM e tutte le interruzioni software e i cambi di contesto.

- **Utilizzo ridotto della CPU**: Ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

Per usare la funzionalità rete accelerata, è necessario abilitarla in modo esplicito in ogni macchina virtuale applicabile. Visualizzare [creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) per le istruzioni.

#### <a name="receive-side-scaling"></a>Receive-side scaling

Scalabilità (RSS) è una tecnologia di driver di rete che distribuisce la ricezione del traffico di rete in modo più efficiente grazie alla distribuzione ricevere l'elaborazione su più CPU in un sistema multiprocessore lato di ricezione. In altre parole, RSS supporta un sistema di elaborare più traffico ricevuto in quanto utilizza tutte le CPU anziché di uno. Per informazioni più tecniche di RSS, vedere [Introduzione a receive-side scaling](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Per ottenere prestazioni ottimali quando la rete accelerata è abilitata in una macchina virtuale, è necessario abilitare RSS. RSS può anche fornire vantaggi nelle macchine virtuali che non usano la funzionalità rete accelerata. Per una panoramica di come determinare se RSS è abilitato e sulla relativa abilitazione, vedere [Ottimizza velocità effettiva della rete per macchine virtuali di Azure](http://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT e TIME_WAIT assassination

TCP TIME_WAIT è un'altra impostazione comune che influisce sulle prestazioni di rete e delle applicazioni. Nelle macchine virtuali occupate apertura e chiusura di socket molti, come i client o server (porta di origine IP:Source + porta IP:Destination di destinazione), durante il normale funzionamento del protocollo TCP, un socket specificato può finire nello stato TIME_WAIT per molto tempo. Stato TIME_WAIT è progettato per consentire eventuali dati aggiuntivi per la consegna di un socket prima di chiuderla. Quindi, in genere stack TCP/IP prevenire il riutilizzo di un socket eliminando automaticamente pacchetti SYN TCP del client.

La quantità di tempo che è un socket in TIME_WAIT è configurabile. E può variare da 30 secondi a 240 secondi. I socket sono una risorsa limitata, e il numero di socket che può essere utilizzato in qualsiasi momento è configurabile. (Il numero di socket disponibile è in genere è 30.000 circa). Se vengono utilizzati i socket disponibili, o se i client e server hanno impostazioni TIME_WAIT non corrispondenti e una macchina virtuale tenta di riutilizzare un socket nello stato TIME_WAIT, le nuove connessioni avrà esito negativo come TCP SYN i pacchetti vengono rimossi automaticamente.

Il valore di intervallo di porte per i socket in uscita è configurabile in genere entro lo stack TCP/IP di un sistema operativo. La stessa cosa vale per le impostazioni TCP TIME_WAIT e il riutilizzo di socket. La modifica di questi numeri potenzialmente utili per migliorare la scalabilità. Tuttavia, a seconda della situazione, queste modifiche potrebbero provocare problemi di interoperabilità. È necessario prestare attenzione se si modificano tali valori.

È possibile usare assassination TIME_WAIT per risolvere questa limitazione di ridimensionamento. Assassination TIME_WAIT consente a un socket di essere riusati in determinate situazioni, ad esempio, quando il numero di sequenza nel pacchetto IP della nuova connessione supera il numero di sequenza dell'ultimo pacchetto dalla connessione precedente. In questo caso, il sistema operativo consentirà la nuova connessione venga stabilita (accetterà la nuova SYN/ACK) e force chiudere la connessione precedente che era nello stato TIME_WAIT. Questa funzionalità è supportata nelle macchine virtuali Windows in Azure. Per altre informazioni sul supporto di altre macchine virtuali, rivolgersi al fornitore del sistema operativo.

Per altre informazioni sulla configurazione delle impostazioni di TCP TIME_WAIT e intervallo di porte di origine, vedere [le impostazioni che possono essere modificate per migliorare le prestazioni di rete](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fattori di rete virtuale che possono influire sulle prestazioni

### <a name="vm-maximum-outbound-throughput"></a>Velocità massima effettiva in uscita della macchina virtuale

Azure offre un'ampia gamma di dimensioni delle macchine Virtuali e i tipi, ognuno con una diversa combinazione di prestazioni. Una di queste funzionalità è la velocità effettiva rete (o larghezza di banda), che viene misurata in megabit al secondo (Mbps). Poiché le macchine virtuali sono ospitate su hardware condiviso, la capacità di rete deve essere condivisa equamente tra le macchine virtuali usando lo stesso hardware. Macchine virtuali di dimensioni maggiori vengono allocate più larghezza di banda rispetto alle macchine virtuali più piccole.

La larghezza di banda di rete allocata a ogni macchina virtuale è misurata sul traffico in uscita dalla macchina virtuale. Tutto il traffico di rete che esce dalla macchina virtuale viene conteggiato per il limite allocato, indipendentemente dalla destinazione. Ad esempio, se una macchina virtuale ha un limite di 1.000 Mbps, tale limite si applica se il traffico in uscita è destinato a un'altra macchina virtuale nella stessa rete virtuale o una all'esterno di Azure.

Il traffico in ingresso non viene misurato o limitato direttamente. Ma esistono altri fattori, ad esempio i limiti di CPU e di archiviazione, che possono influire sulla capacità di una macchina virtuale per l'elaborazione dati in ingresso.

La funzionalità rete accelerata è progettata per migliorare le prestazioni di rete, tra cui latenza, velocità effettiva e l'utilizzo della CPU. La funzionalità rete accelerata può migliorare la velocità effettiva di una macchina virtuale, ma è possibile farlo solo fino a larghezza di banda allocata della macchina virtuale.

Macchine virtuali di Azure hanno almeno un'interfaccia di rete a cui è collegata. Si potrebbe avere diversi. La larghezza di banda allocata a una macchina virtuale è la somma di tutto il traffico in uscita in tutte le interfacce di rete collegate alla macchina. In altre parole, la larghezza di banda viene allocato per singola macchina virtuale, indipendentemente dal numero di interfacce di rete è collegato alla macchina.

Velocità effettiva in uscita prevista e il numero di interfacce di rete supportate da ogni dimensione di VM sono descritti in dettaglio [macchine virtuali di dimensioni per Windows in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Per visualizzare la massima velocità effettiva, selezionare un tipo, ad esempio **generico**e quindi individuare la sezione sulla serie di dimensioni della pagina risultante (ad esempio, "serie Dv2"). Per ogni serie, è disponibile una tabella che fornisce le specifiche di rete nell'ultima colonna, dal titolo "schede di rete Max / larghezza di banda (Mbps) previsto."

Il limite di velocità effettiva si applica alla macchina virtuale. Velocità effettiva non dipende dai fattori seguenti:

- **Numero di interfacce di rete**: Il limite di larghezza di banda viene applicato alla somma di tutto il traffico in uscita dalla macchina virtuale.

- **Rete accelerata**: Anche se questa funzionalità può essere utile per ottenere il limite pubblicato, non viene modificato il limite.

- **Destinazione del traffico**: Tutte le destinazioni contano per il limite in uscita.

- **Protocollo**: Tutto il traffico in uscita su tutti i protocolli conta per il limite.

Per altre informazioni, vedere [larghezza di banda di rete macchina virtuale](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerazioni sulle prestazioni di Internet

Come illustrato in questo articolo, fattori su internet e all'esterno del controllo di Azure possono influenzare le prestazioni di rete. Ecco alcuni di questi fattori:

- **Latenza**: Il tempo di round trip tra due destinazioni può dipendere da problemi nelle reti intermedi, il traffico che non accetta il percorso di distanza "più breve" e i percorsi di peering non ottimali.

- **Perdita di pacchetti**: Perdita di pacchetti può essere causata da una congestione della rete, problemi di percorso fisico e i dispositivi di rete con prestazioni insoddisfacenti.

- **Dimensioni/frammentazione MTU**: Frammentazione lungo il percorso può causare ritardi di arrivo dei dati o nei pacchetti che arrivano non in ordine, influendo talvolta il recapito dei pacchetti.

Traceroute è un ottimo strumento per la misurazione delle caratteristiche delle prestazioni di rete (ad esempio la perdita di pacchetti e latenza) ogni percorso di rete tra un dispositivo di origine e un dispositivo di destinazione.

### <a name="network-design-considerations"></a>Considerazioni sulla progettazione di rete

Insieme a considerazioni illustrate in precedenza in questo articolo, la topologia di una rete virtuale in termini di prestazioni della rete. Ad esempio, una progettazione hub-spoke che backhauls il traffico a livello globale per una rete virtuale single-hub renderà disponibile la latenza di rete, che influirà sulle prestazioni di rete complessiva.

Il numero di dispositivi di rete che attraversa il traffico di rete può anche influire sulla latenza complessiva. Ad esempio, in una progettazione hub-spoke, se il traffico passa attraverso un'appliance virtuale hub e spoke appliance di rete virtuale prima di transito a internet, le Appliance virtuali di rete possono introdurre latenza.

### <a name="azure-regions-virtual-networks-and-latency"></a>Aree di Azure, le reti virtuali e la latenza

Aree di Azure sono costituite da più Data Center esistenti all'interno di un'area geografica generica. Questi Data Center potrebbe non essere fisicamente uno accanto a altro. In alcuni casi sono suddivisi per quanto più 10 chilometri. La rete virtuale è una sovrapposizione logica all'interno della rete fisica Data Center di Azure. Una rete virtuale non implica qualsiasi topologia di rete specifico all'interno del Data Center.

Ad esempio, due macchine virtuali che si trovano nella stessa rete virtuale e subnet potrebbero essere in diversi rack, righe o anche i Data Center. Potrebbe essere separati da feet di cavi in fibra ottica o da chilometri cavi in fibra ottica. Questa variabilità potrebbe introdurre latenza variabile (differenza di pochi millisecondi) tra VM diverse.

La posizione geografica delle macchine virtuali e la latenza risulta potenziali tra due macchine virtuali, può essere influenzati dalla configurazione del set di disponibilità e le zone di disponibilità. Ma la distanza tra i Data Center in un'area è specifico dell'area geografica e influenzato principalmente dalla topologia di Data Center nell'area.

### <a name="source-nat-port-exhaustion"></a>L'esaurimento delle porte NAT origine

Una distribuzione in Azure può comunicare con gli endpoint all'esterno di Azure sulla rete internet pubblica e/o nello spazio IP pubblici. Quando un'istanza avvia una connessione in uscita, Azure associa dinamicamente l'indirizzo IP privato a un indirizzo IP pubblico. Dopo che Azure ha creato questo mapping, il traffico di ritorno per il flusso con origine in uscita può raggiungere l'indirizzo IP privato in cui ha origine il flusso.

Per ogni connessione in uscita, Azure Load Balancer deve mantenere questo mapping per un determinato periodo di tempo. La natura multi-tenant di Azure, mantenendo il mapping per ogni flusso in uscita per ogni macchina virtuale può essere a elevato utilizzo di risorse. Quindi, vi sono limiti che vengono definiti e basati sulla configurazione di rete virtuale di Azure. In alternativa, è possibile, ad esempio, che con maggiore precisione, un determinato numero di connessioni in uscita può apportare solo una macchina virtuale di Azure in un determinato momento. Quando vengono raggiunti questi limiti, la macchina virtuale non è possibile stabilire connessioni più in uscita.

Ma questo comportamento è configurabile. Per altre informazioni su SNAT e SNAT porta esaurimento, vedere [questo articolo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Misurare le prestazioni di rete in Azure

Un numero di valori massimi di prestazioni in questo articolo riguardano la latenza di rete / round trip time (RTT) tra due macchine virtuali. In questa sezione offre alcuni suggerimenti per la modalità testare la latenza/RTT e come testare le prestazioni TCP e le prestazioni di rete della macchina virtuale. È possibile ottimizzare e test delle prestazioni i valori di TCP/IP e di rete descritti in precedenza utilizzando le tecniche descritte in questa sezione. È possibile inserire i calcoli indicati in precedenza la latenza, il valore MTU, MSS e la finestra di valori di dimensioni e confrontare i valori massimi teorici ai valori effettivi che si osserva durante i test.

### <a name="measure-round-trip-time-and-packet-loss"></a>Misurano il tempo di round trip e perdita di pacchetti

Le prestazioni TCP si basa principalmente su RTT e perdita dei pacchetti. L'utilità PING disponibile in Windows e Linux offre il modo più semplice per misurare la perdita di pacchetti e di RTT. L'output del comando PING visualizzerà la latenza minime/massime/medie tra origine e destinazione. Include anche una perdita di pacchetti. Impostazione predefinita, PING Usa il protocollo ICMP. È possibile usare PsPing per testare TCP RTT. Per altre informazioni, vedere [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Velocità effettiva di misure di una connessione TCP

NTttcp è uno strumento per test delle prestazioni TCP di una macchina virtuale di Windows o Linux. È possibile modificare diverse impostazioni di TCP e quindi testare i vantaggi usando NTttcp. Per ulteriori informazioni, vedere le risorse:

- [Larghezza di banda/velocità effettiva (NTttcp) di test](https://aka.ms/TestNetworkThroughput)

- [NTttcp Utility](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Misura effettiva della larghezza di banda di una macchina virtuale

È possibile testare le prestazioni di diversi tipi di VM, con accelerata di rete e così via, tramite uno strumento denominato iPerf. iPerf è anche disponibile in Linux e Windows. iPerf è possibile usare TCP o UDP per velocità effettiva complessiva di rete di test. i test di velocità effettiva di iPerf TCP sono influenzati dai fattori illustrati in questo articolo (ad esempio, la latenza e tempo RTT). Quindi, UDP potrebbero ottenere risultati migliori se si desidera semplicemente verificare la velocità massima.

Per altre informazioni, vedere questi articoli:

- [Risoluzione dei problemi di prestazioni di rete di Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Come convalidare la velocità effettiva della VPN verso una rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Rilevare i comportamenti TCP inefficienti

Nelle acquisizioni di pacchetti, i clienti di Azure potrebbero visualizzare pacchetti TCP con flag TCP (SACK, DUP ACK, RITRASMISSIONE e FAST RITRASMISSIONE) che potrebbero indicare problemi di prestazioni di rete. Questi pacchetti indicano in modo specifico le inefficienze rete risultanti da perdita di pacchetti. Ma la perdita di pacchetti non è necessariamente causata da problemi di prestazioni di Azure. Problemi di prestazioni può essere il risultato di problemi delle applicazioni, problemi del sistema operativo o altri problemi che potrebbero non essere direttamente correlati alla piattaforma Azure.

Inoltre, tenere presente che alcuni ritrasmissione e riconoscimenti duplicati sono normali in una rete. I protocolli TCP sono stati compilati per l'affidabilità. Evidenza di questi pacchetti TCP in un'acquisizione di pacchetti non indica necessariamente un problema di rete a livello di sistema, a meno che non siano eccessive.

Tuttavia, questi tipi di pacchetto sono indicazioni che elaborate TCP non raggiunge le prestazioni massime, per motivi illustrati nelle altre sezioni di questo articolo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si sono appresi ottimizzazione delle prestazioni TCP/IP per macchine virtuali di Azure, è possibile leggere altre considerazioni sullo [pianificazione di reti virtuali](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) o [altre informazioni sulla connessione e configurazione di reti virtuali ](https://docs.microsoft.com/en-us/azure/virtual-network/).
