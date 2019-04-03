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
ms.date: 3/30/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: c5d4f67e9c1e4e983133675c440b8c5b64183227
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851862"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP ottimizzazione delle prestazioni per macchine virtuali di Azure

Lo scopo di questo articolo è illustrare comuni tecniche di ottimizzazione delle prestazioni TCP/IP e le considerazioni per le macchine virtuali in esecuzione in Microsoft Azure. È importante prima di tutto di avere una conoscenza di base dei concetti e quindi illustrato il modo in cui possono essere ottimizzate.

## <a name="common-tcpip-tuning-techniques"></a>Comuni tecniche di ottimizzazione TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>Il valore MTU, la frammentazione e grandi dimensioni Offload invio (LSO)

#### <a name="explanation-of-mtu"></a>Spiegazione di MTU

L'unità di trasmissione massima (MTU) è il frame di dimensioni più grande (pacchetto), espresso in byte, che possono essere inviati tramite un'interfaccia di rete. Il valore MTU è un'impostazione configurabile e il valore predefinito che MTU usato nelle macchine virtuali di Azure e l'impostazione predefinita nella maggior parte dei dispositivi di rete è a livello globale, 1500 byte.

#### <a name="explanation-of-fragmentation"></a>Spiegazione della frammentazione

La frammentazione si verifica quando viene inviato un pacchetto che supera il valore MTU di un'interfaccia di rete. Lo stack TCP/IP interromperà il pacchetto in parti più piccole (frammenti) conformi alle interfacce di MTU. La frammentazione si verifica a livello di IP ed è indipendente dal protocollo sottostante (ad esempio, TCP). Quando un pacchetto di 2000 byte viene inviato tramite un'interfaccia di rete con un valore MTU di 1500, quindi verrà suddivisi in un unico pacchetto 1500 byte e un pacchetto di 500 byte.

I dispositivi di rete nel percorso tra origine e destinazione hanno la possibilità per eliminare i pacchetti che superano il valore MTU o alla frammentazione del pacchetto in parti più piccole.

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>Il bit "non frammento (DF)" in un pacchetto IP

Il bit di Don ' t Fragment è un flag nell'intestazione del protocollo IP. Quando è impostato il bit DF, indica che i dispositivi di rete intermedi nel percorso tra il mittente e ricevitore non necessario di frammentazione del pacchetto. Esistono molte buone ragioni per cui può essere impostato il bit (vedere la sezione rilevamento del percorso seguito per un esempio). Quando un dispositivo di rete riceve un pacchetto con il set di bit Don ' t Fragment e tale pacchetto supera il valore MTU dell'interfaccia di dispositivi, sarà il comportamento standard per il dispositivo eliminare il pacchetto e inviano periodicamente un pacchetto "Frammentazione ICMP necessita" nell'origine originale del pacchetto.

#### <a name="performance-implications-of-fragmentation"></a>Implicazioni sulle prestazioni della frammentazione

La frammentazione può avere impatto negativo sulle prestazioni. Uno dei motivi principali per l'impatto sulle prestazioni è l'impatto di CPU/memoria di frammentazione e il riassemblaggio dei pacchetti. Quando un dispositivo di rete deve la frammentazione di un pacchetto, dovrà allocare le risorse di CPU/memoria per l'esecuzione della frammentazione. Lo stesso problema deve verificarsi quando il pacchetto viene riassemblato. Il dispositivo di rete deve archiviare tutti i frammenti fino a quando non vengono ricevuti in modo che è possibile riassemblarle nel pacchetto originale. Questo processo di frammentazione/riassemblaggio può provocare latenza a causa del processo di frammentazione/riassemblaggio.

L'altra implicazione possibili negativamente sulle prestazioni di frammentazione è che i pacchetti frammentati potrebbero arrivare non in ordine. I pacchetti non in ordine possono causare alcuni tipi di dispositivi di rete per eliminare i pacchetti non in ordine - che verranno quindi richiesto all'intero pacchetto potrà essere ritrasmesso. Gli scenari tipici per l'eliminazione di frammenti includono dispositivi di sicurezza, ad esempio i firewall di rete o quando un dispositivo di rete di ricezione vengono esauriti i buffer. Quando un dispositivo di rete di ricezione vengono esauriti i buffer, un dispositivo di rete sta tentando di riassemblare un pacchetto frammentato, ma non dispone di risorse per archiviare e reassume il pacchetto.

La frammentazione può essere percepita come un operazione negativi, ma il supporto per la frammentazione è necessaria per la connessione di reti diverse tramite Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Vantaggi e conseguenze della modifica il valore MTU

In generale, aumentando il valore MTU può creare una rete più efficiente. Tutti i pacchetti che vengono trasmessi dispone di informazioni sulle intestazioni aggiuntive che viene aggiunto al pacchetto originale. Più pacchetti significa intestazione ulteriori overhead e la rete è una procedura meno efficiente di conseguenza.

Ad esempio, la dimensione di intestazione Ethernet è di 14 byte oltre a una di analisi a 4 byte Frame controllare sequenza (FCS) per garantire la coerenza di frame. Se viene inviato un pacchetto di 2000 byte, 18 byte di overhead Ethernet viene aggiunto nella rete. Se il pacchetto viene frammentato in un pacchetto di 1500 byte e un pacchetto di 500 byte, ogni pacchetto avrà 18 byte dell'intestazione Ethernet - o 36 byte. Mentre un singolo pacchetto 2000 byte sono solo un'intestazione Ethernet di 18 byte.

È importante notare che aumentare il valore MTU di per sé non necessariamente creerà una rete più efficiente. Se un'applicazione invia solo i pacchetti a 500 byte, l'overhead dell'intestazione stesso sarà presente se il valore MTU è 1500 o byte 9000. Affinché la rete essere più efficiente, quindi deve usare anche pacchetti più grandi che riguardano il valore MTU.

#### <a name="azure-and-vm-mtu"></a>Azure e il valore MTU macchina virtuale

Il valore predefinito di MTU per macchine virtuali di Azure è 1500 byte. Lo stack di rete virtuale di Azure tenterà di frammentazione di un pacchetto in byte 1400. Tuttavia, lo stack di rete virtuale di Azure consentirà pacchetti fino a 2006 byte quando è impostato il bit "Don ' t Fragment" nell'intestazione IP.

È importante notare che questa frammentazione non implica che lo stack di rete virtuale di Azure è intrinsecamente inefficiente perché frammenti pacchetti a livello byte 1400 mentre le macchine virtuali hanno un valore MTU di 1500. La realtà è che un'alta percentuale di pacchetti di rete è nettamente inferiore 1400 o byte 1500.

#### <a name="azure-and-fragmentation"></a>Azure e sulla frammentazione

Stack di rete virtuale di Azure oggi stesso è configurato per eliminare "Non in ordine di frammenti", vale a dire di pacchetti frammentati che non arrivano in base all'ordine originale frammentati. Questi pacchetti vengono eliminati principalmente a causa di una vulnerabilità di sicurezza di rete ha annunciata nel mese di novembre 2018 chiamato FragmentStack.

FragmentSmack è un difetto in modo il kernel Linux gestiti del riassemblaggio dei pacchetti frammentati IPv4 e IPv6. Un utente malintenzionato remoto è stato possibile usare questo difetto alle operazioni di riassemblaggio di trigger frammento costosa, causare un attacco denial of service e un aumento della CPU nel sistema di destinazione.

#### <a name="tune-the-mtu"></a>Ottimizzare il valore MTU

Macchine virtuali di Azure supportano una MTU può essere configurato come qualsiasi altro sistema operativo. Tuttavia, è necessario considerare la frammentazione si verifica all'interno di Azure e viene indicata in precedenza, quando si configura il valore MTU.

Azure non invita i clienti per aumentare la loro MTU VM. Questa discussione è destinata per spiegare in dettaglio come Azure implementa MTU ed esegue frammentazione oggi stesso.

> [!IMPORTANT]
>Aumentando il valore MTU non è visualizzata per migliorare le prestazioni e potrebbe avere un effetto negativo sulle prestazioni dell'applicazione.
>
>

#### <a name="large-send-offload-lso"></a>Offload invio di grandi dimensioni (LSO)

Grandi Offload invio (LSO) può migliorare le prestazioni di rete tramite l'offload di segmentazione dei pacchetti per la scheda Ethernet. Con LSO abilitata, lo stack TCP/IP crea un pacchetto TCP di grandi dimensioni e inviarlo alla scheda Ethernet per la segmentazione prima di inoltrarlo. Il vantaggio di LSO è che sia possibile liberare le CPU da segmentando i pacchetti in caso di dimensioni che sono conformi al valore MTU e che l'elaborazione per l'interfaccia Ethernet in cui viene eseguito in hardware offload. Altre informazioni sui vantaggi di LSO sono reperibile nel [prestazioni nella documentazione di scheda di rete Microsoft](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Quando LSO è abilitata, i clienti di Azure venga visualizzato le dimensioni di frame di grandi dimensioni quando acquisizioni di pacchetti verrà eseguito. Queste dimensioni di frame di grandi dimensioni possono comportare alcuni clienti a ritenere che la frammentazione o un frame jumbo che MTU viene utilizzato quando non è. Con LSO, la scheda ethernet possibile pubblicizzare un maggiore MSS allo stack TCP/IP per creare un pacchetto TCP più grande. Questo frame non segmentato intero viene quindi inoltrato alla scheda Ethernet e dovrebbe essere visibile in un'acquisizione di pacchetti eseguita sulla macchina virtuale. Tuttavia, i pacchetti verranno suddivisi in più piccolo numero di frame dall'adattatore Ethernet in base al valore MTU dell'adattatore Ethernet.

### <a name="tcpmss-window-scaling-and-pmtud"></a>Adattamento della finestra/MSS TCP e PMTUD

#### <a name="explanation-of-tcp-mss"></a>Spiegazione di TCP MSS

TCP MSS Maximum Segment Size () è un'impostazione consente di impostare le dimensioni del segmento TCP massima per evitare la frammentazione dei pacchetti TCP. Sistemi operativi verrà impostata in genere MSS MSS = MTU - dimensioni & intestazione TCP IP (20 byte o 40 byte totali). Pertanto, un'interfaccia con un valore MTU di 1500 avrà una MSS contenuto 1460. MSS, tuttavia, è possibile configurare.

Questa impostazione è concordata nell'handshake a tre vie di TCP durante una sessione TCP viene configurata tra origine e destinazione. Entrambi i lati inviare un valore MSS e minore dei due viene usato per la connessione TCP.

Dispositivi di rete intermedi, come i gateway VPN, incluso il Gateway VPN di Azure, hanno la possibilità di modificare il valore MTU indipendentemente dall'origine e destinazione per garantire prestazioni ottimali della rete. Quindi, tenere presente che il valore MTU di origine e destinazione da solo non sia i fattori principali unici valore MSS effettivo.

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>Spiegazione del rilevamento del percorso MTU (PMTUD)

Mentre viene negoziato MSS, potrebbe non indicare l'effettiva MSS che possono essere utilizzati come altri dispositivi di rete nel percorso tra origine e di destinazione può avere un valore MTU inferiore rispetto alla origine e destinazione. In questo caso, il dispositivo è inferiore rispetto al pacchetto il cui valore MTU scarta il pacchetto e inviare nuovamente un messaggio di controllo messaggio protocollo ICMP (Internet) sulla frammentazione necessita (tipo 3, 4 codice) che contiene il valore MTU. Questo messaggio ICMP consente all'host di origine per ridurre in modo appropriato il relativo percorso MTU. Il processo è denominato rilevamento del percorso MTU.

Il processo di PMTUD è intrinsecamente inefficiente e ha implicazioni per le prestazioni di rete. L'invio di pacchetti che superano un valore MTU i percorsi di rete, tali pacchetti devono essere ritrasmesso con un MSS inferiore. Se il mittente non riceve il pacchetto ICMP frammentazione necessaria, forse a causa di un firewall di rete nel percorso (noto come blackhole PMTUD), quindi il server sender non conosce che necessarie a ridurre la MSS, in modo continuo lo trasmetta di nuovo il pacchetto. Per questo motivo, non è consigliabile aumentare il valore MTU macchina virtuale di Azure.

#### <a name="vpn-considerations-with-mtu"></a>Considerazioni su VPN con il valore MTU

I clienti che usano le macchine virtuali che eseguono l'incapsulamento (ad esempio VPN IPSec) possono avere implicazioni aggiuntive al pacchetto di dimensioni e il valore MTU. Le VPN aggiungere intestazioni aggiuntive verranno aggiunti al pacchetto originale in questo modo l'aumento delle dimensioni del pacchetto e che richiedono un MSS più piccoli.

La raccomandazione corrente per Azure consiste nell'impostare fissaggio TCP MSS a 1350 byte e interfaccia tunnel MTU 1400. Altre informazioni vedere la [VPN pagina parametri IPSec/IKE e dispositivi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latenza, tempo di round trip e adattamento della finestra TCP

#### <a name="latency-and-round-trip-time"></a>Tempo di round trip e la latenza

Latenza di rete è regolata dalla velocità della luce in fibra ottica della rete fiber. La realtà è, velocità effettiva della rete di TCP effettivamente governati (pratici massimi) perché di round trip Time (RTT) tra due dispositivi di rete.

| | | | |
|-|-|-|-|
|Route|Distance|Tempo unidirezionale|Tempo di round trip (RTT)|
|New York a San Francisco|4,148 km|21 ms|42 ms|
|New York a Londra|5,585 km|28 ms|56 ms|
|New York to Sydney|15,993 km|80 ms|160 ms|

Questa tabella mostra la linea retta distanza tra due posizioni, tuttavia, nelle reti, la distanza è in genere più tempo la distanza della linea di tendenza. È una semplice formula per calcolare RTT minimo come disciplinato dalla velocità della luce: minimo RTT = 2 * (chilometri di distanza o la velocità della propagazione).

Un valore pari a 200 standard può essere utilizzato per la velocità della propagazione - valore è che la distanza in metri light viene trasferito in 1 millisecondo.

Nell'esempio a New York a San Francisco è 4,148 km distanza linea d'aria. Minimo tempo RTT = 2 * (4,148 / 20). L'output dell'equazione sarà espresso in millisecondi.

Poiché la distanza tra due posizioni fisica è una realtà predefinita, se le prestazioni di rete massima sono necessaria, quindi l'opzione più logico consiste nel selezionare le destinazioni con la distanza minima tra di essi. Poi, possono prendere decisioni di progettazione all'interno della rete virtuale per ottimizzare il percorso del traffico e ridurre la latenza. Nella sezione Considerazioni di progettazione di rete seguente vengono descritte queste considerazioni sulla rete virtuale.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Effetti di tempo di round trip e la latenza sul protocollo TCP

Round trip time (RTT) ha un effetto diretto sulla velocità effettiva massima di TCP. Il protocollo TCP è un concetto di dimensioni della finestra. Le dimensioni della finestra è la quantità massima di traffico che può essere inviato tramite una connessione TCP prima che il mittente deve ricevere acknowledgement dal destinatario. Se il valore MSS TCP è impostata su 1460 e le dimensioni della finestra TCP è impostata su 65535 quindi il mittente può inviare 45 pacchetti deve ricevere acknowledgement dal destinatario. Se non viene ricevuto acknowledgement per ritrasmettere il mittente. In questo esempio, dimensioni della finestra TCP / TCP MSS = pacchetti inviati. O 65535 / è arrotondato 1460 fino a 45.

Questo stato "in attesa di riconoscimento", come un meccanismo per creare un recapito affidabile dei dati, è quello che determina in modo efficace RTT influire sulla velocità effettiva TCP. Più a lungo in attesa di riconoscimento al mittente, più a lungo anche devono trascorrere prima di inviare più dati.

La formula per calcolare la velocità effettiva massima di una singola connessione TCP è come segue: Dimensioni della finestra / (latenza RTT in millisecondi / 1000) = numero massimo di byte al secondo. Nella tabella seguente viene formattata in megabyte per migliorare la leggibilità e Mostra il numero massimo megabyte o al secondo di velocità effettiva di una singola connessione TCP.

| | | | |
|-|-|-|-|
|Dimensioni della finestra TCP in byte|Latenza RTT<br/>in millisecondi|Massima<br/>Megabyte al secondo di velocità effettiva|Massima<br/> Megabit al secondo di velocità effettiva|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

Se si verifica alcuna perdita di pacchetti, quindi riduce la velocità effettiva massima di una connessione TCP mentre il server sender ritrasmette dati che ha già inviato.

#### <a name="explanation-of-tcp-window-scaling"></a>Spiegazione dell'adattamento della finestra TCP

Adattamento della finestra TCP è un concetto che aumenta in modo dinamico le dimensioni della finestra TCP consentendo maggiore quantità di dati da inviare prima che venga richiesta una conferma. Nell'esempio precedente, i 45 pacchetti verrebbe inviati prima che fosse necessario un acknowledgement. Se il numero di pacchetti che vengono inviati prima di un acknowledgement viene aumentato, quindi la velocità effettiva massima di TCP risultano inoltre migliorata riducendo il numero di volte in cui un server sender è in attesa di riconoscimento.

Velocità effettiva TCP è illustrata in una semplice tabella riportata di seguito:

| | | | |
|-|-|-|-|
|Dimensioni della finestra TCP<br/>in byte|RTT latenza in millisecondi|Massima<br/>Megabyte al secondo di velocità effettiva|Massima<br/> Megabit al secondo di velocità effettiva|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Tuttavia, il valore dell'intestazione TCP per la dimensione della finestra TCP è solo a 2 byte, ovvero che il valore massimo per una finestra di ricezione è 65535. Per aumentare la dimensione massima della finestra, è stato introdotto un fattore di scala finestra TCP.

Il fattore di scala è anche un'impostazione che può essere configurata in un sistema operativo. La formula per calcolare le dimensioni della finestra TCP con fattori di scala è come segue: Dimensioni della finestra TCP = dimensioni della finestra TCP in byte \* (2 ^ fattore di scala). Se il fattore di scala di finestra è 3 e dimensioni della finestra di 65535, calcolo è come segue: 65535 \* (2 ^ 3) = 262,140 byte. Un fattore di scala pari a 14 comporta una dimensione della finestra TCP pari a 14 (all'offset massimo consentito), quindi le dimensioni della finestra TCP sarà 1,073,725,440 byte (8,5 Gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Supporto per il ridimensionamento finestra TCP

Windows ha la possibilità di impostare diversi fattori di scala per una connessione in base al tipo, sono disponibili diverse classi di connessioni (datacenter, internet e così via). È possibile visualizzare la classificazione di connessione di ridimensionamento finestra con il comando powershell Get-NetTCPConnection.

```powershell
Get-NetTCPConnection
```

È possibile visualizzare i valori di ogni classe con il comando powershell Get-NetTCPSetting.

```powershell
Get-NetTCPSetting
```

Le dimensioni della finestra TCP iniziale e il fattore di ridimensionamento TCP possono essere impostati in Windows tramite il comando di powershell Set-NetTCPSetting. Altre informazioni sono reperibile nel [Set-NetTCPSetting pagina](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

Le impostazioni TCP efficace per AutoTuningLevel sono i seguenti.

| | | | |
|-|-|-|-|
|AutoTuningLevel|Fattore di scala|Moltiplicatore di scala|Formula da<br/>calcolare la dimensione massima della finestra|
|Disabled|Nessuna|Nessuna|Dimensioni della finestra|
|Con restrizioni|4|2^4|Dimensioni della finestra * (2 ^ 4)|
|Soggetto a severe limitazioni|2|2^2|Dimensioni della finestra * (2 ^ 2)|
|Normale|8|2^8|Dimensioni della finestra * (2 ^ 8)|
|Sperimentale|14|2^14|Dimensioni della finestra * (2 ^ 14)|

Anche se queste impostazioni sono probabilmente a influire sulle prestazioni TCP, si noti che molti altri fattori attraverso la rete Internet, all'esterno del controllo di Azure che possono influire sulle prestazioni TCP.

#### <a name="increase-mtu-size"></a>Aumentare la dimensione MTU

Una domanda logica è "può aumentare il valore MTU increase prestazioni TCP come un valore più grande MTU indica una maggiore MSS"? La risposta è semplice: probabilmente non è. Come illustrato, esistono vantaggi e svantaggi delle dimensioni dei pacchetti che sono applicabili di là solo il traffico TCP. Come indicato in precedenza, i fattori più importanti che influiscono su prestazioni di velocità effettiva TCP sono dimensioni della finestra TCP, perdita di pacchetti e RTT.

> [!IMPORTANT]
> Azure non è consigliabile che i clienti di Azure modifichino il valore MTU predefinito nelle macchine virtuali.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>La funzionalità rete accelerata e Receive-Side Scaling

#### <a name="accelerated-networking"></a>Rete accelerata

Funzioni di rete di macchine virtuali sono sempre stati della CPU con utilizzo intensiva sia per la VM Guest/Host dell'Hypervisor. Tutti i pacchetti che passa attraverso l'host vengano elaborato nel software dall'host di CPU - tra cui tutte le reti virtuali incapsulamento/de-capsulation. Pertanto, la maggiore quantità di traffico che passa attraverso l'host, quindi maggiore sarà il carico della CPU. E, se la CPU dell'host è occupata le altre operazioni, quindi che influirà anche la latenza e velocità effettiva della rete. Questo problema è stato risolto tramite la rete accelerata.

La funzionalità rete accelerata offre latenza estremamente bassa della rete coerenti tramite hardware programmabili interne di Azure e le tecnologie come SR-IOV. Spostando gran parte dello stack di rete definita dal software di Azure disattivata la CPU e in SmartNICs basato su FPGA, calcolare i cicli vengono recuperati dalle applicazioni dell'utente finale, l'inserimento di ridurre il carico della macchina virtuale, riduzione della latenza di instabilità e l'incoerenza. In altre parole, le prestazioni possono essere più deterministica.

La funzionalità rete accelerata consente di ottenere miglioramenti delle prestazioni, consentendo la macchina virtuale Guest ignorare l'host e stabilire un percorso dati direttamente con SmartNIC dell'host. Vantaggi della funzionalità rete accelerata sono:

- **Latenza più bassa / più pacchetti al secondo (pps)**: Rimuovendo il commutatore virtuale dal percorso dati viene eliminato il tempo di attesa dei pacchetti nell'host per l'elaborazione dei criteri, aumentando così il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.

- **Instabilità ridotta**: L'elaborazione del commutatore dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. La ripartizione del carico di applicazione dei criteri nell'hardware elimina tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, rimuovendo l'host dalle comunicazioni con la macchina virtuale nonché tutte le interruzioni software e i cambi di contesto.

- **Utilizzo ridotto della CPU**: Ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

La funzionalità rete accelerata deve essere abilitata esplicitamente su una base per ogni VM. Le istruzioni per l'abilitazione della funzionalità rete accelerata in una macchina virtuale sono disponibili nel [creare una macchina virtuale Linux con rete accelerata pagina](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling-rss"></a>Receive Side Scaling (RSS)

Receive-Side Scaling è una tecnologia di driver di rete che distribuisce la ricezione del traffico di rete in modo più efficiente grazie alla distribuzione ricevere l'elaborazione su più CPU in un sistema multiprocessore. In altre parole, RSS supporta un sistema di elaborare una maggiore quantità di traffico ricevuto in quanto utilizza tutte le CPU anziché di uno. Una discussione più tecniche su RSS, vedere la [Introduzione a Receive-Side Scaling pagina](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

RSS è necessario per ottenere prestazioni ottimali quando la rete accelerata è abilitata in una macchina virtuale. Può anche contenere vantaggi nell'uso RSS nelle macchine virtuali che non hanno la funzionalità rete accelerata abilitata. Una panoramica di come determinare se RSS è abilitato e la configurazione per abilitarlo, vedere la [Ottimizza velocità effettiva della rete per macchine virtuali di Azure pagina](http://aka.ms/FastVM).

### <a name="tcp-time-wait-and-time-wait-assassination"></a>Ora e TCP tempo attesa attesa Assassination

Un altro problema comune che influisce sulle prestazioni di rete e delle applicazioni è l'impostazione TCP tempo di attesa. Nelle macchine virtuali occupate apertura e chiusura di socket molti, come un client o server (porta di origine IP:Source + porta IP:Destination di destinazione), durante il normale funzionamento del protocollo TCP, un socket specificato può finire in uno stato di attesa del tempo per una notevole quantità di tempo. Questo stato "tempo di attesa", è progettata per consentire eventuali dati aggiuntivi per la consegna di un socket prima di chiuderla. Di conseguenza, gli stack TCP/IP a livello generale prevenire il riutilizzo di un socket eliminando automaticamente il pacchetto SYN TCP client.

Questo periodo di tempo un socket è in fase di stato di attesa è configurabile ma è stato compreso tra 30 secondi e 240 secondi. I socket sono una risorsa limitata e il numero di socket che può essere utilizzato in un determinato momento possono essere configurato (il numero rientra in genere circa 30.000 potenziali sockets). Se questo numero è esaurito, o client e server hanno impostazioni di attesa ora non corrispondenti e tenta di riutilizzare un socket in stato di attesa in una macchina virtuale, quindi le nuove connessioni avrà esito negativo come pacchetti SYN TCP vengono eliminati automaticamente.

In genere il valore di intervallo di porte per i socket in uscita, nonché le impostazioni TCP tempo di attesa e il riutilizzo di socket sono configurabili nello Stack TCP/IP di un sistema operativo. La modifica di questi numeri potenzialmente utili per migliorare la scalabilità, ma a seconda dello scenario, potrebbe introdurre problemi di interoperabilità e deve essere modificata con cautela.

Una funzionalità denominata Assassination di tempo di attesa è stato introdotto per risolvere questo problema ridimensionamento limitazione. Tempo di attesa Assassination consente a un socket di essere riutilizzato in determinati scenari, ad esempio quando il numero di sequenza nel pacchetto IP della nuova connessione supera il numero di sequenza dell'ultimo pacchetto dalla connessione precedente. In questo caso, il sistema operativo consentirà la nuova connessione venga stabilita (accettare di nuovo di SYN ACK) e force chiudere la connessione precedente che è stato in tempo di attesa dello stato. Questa funzionalità è attualmente supportata nelle macchine virtuali Windows in Azure e il supporto all'interno di altre macchine virtuali dovrebbe essere controllato dai clienti di Azure con i rispettivi fornitori del sistema operativo.

Documentazione su come configurare le impostazioni TCP tempo di attesa e intervallo di porte di origine è disponibile nel [le impostazioni che possono essere modificate alla pagina di migliorare le prestazioni di rete](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fattori di rete virtuali che possono influire sulle prestazioni

### <a name="vm-maximum-outbound-throughput"></a>Velocità massima effettiva in uscita della macchina virtuale

Azure offre un'ampia gamma di dimensioni e tipi di macchine virtuali, ognuna con una diversa combinazione di prestazioni. Una tale funzionalità delle prestazioni è rete velocità effettiva o larghezza di banda, misurata in megabit al secondo (Mbps). Poiché le macchine virtuali sono ospitate su hardware condiviso, la capacità di rete deve essere condivisa equamente tra le macchine virtuali che condividono lo stesso hardware. Alle macchine virtuali di dimensioni maggiori viene allocata una larghezza di banda relativamente maggiore rispetto alle macchine virtuali più piccole.

La larghezza di banda di rete allocata a ogni macchina virtuale è misurata sul traffico in uscita dalla macchina virtuale. Tutto il traffico di rete che esce dalla macchina virtuale viene conteggiato per il limite allocato, indipendentemente dalla destinazione. Ad esempio, se una macchina virtuale ha un limite di 1.000 Mbps, tale limite si applica se il traffico in uscita è destinato a un'altra macchina virtuale nella stessa rete virtuale o all'esterno di Azure.
Il traffico in ingresso non viene misurato o limitato direttamente. Tuttavia, esistono altri fattori, ad esempio i limiti di CPU e di archiviazione, che possono influire sulle capacità di una macchina virtuale di elaborare i dati in ingresso.

La funzionalità rete accelerata è una funzionalità progettata per migliorare le prestazioni di rete, tra cui latenza, velocità effettiva e l'utilizzo della CPU. Rete accelerata può migliorare la velocità effettiva di una macchina virtuale, è possibile farlo solo fino a quello della macchina virtuale allocata della larghezza di banda.

Le macchine virtuali di Azure devono avere almeno un'interfaccia collegata, ma possono averne anche molte. La larghezza di banda allocata a una macchina virtuale è la somma di tutto il traffico in uscita in tutte le interfacce di rete associate alla macchina virtuale. In altre parole, la larghezza di banda allocata è per macchina virtuale, indipendentemente dal numero di interfacce di rete collegate alla macchina virtuale.
 
Velocità effettiva in uscita prevista e il numero di interfacce di rete supportate da ogni dimensione di VM è descritta in dettaglio di seguito. Per visualizzare la massima velocità effettiva, selezionare un tipo, ad esempio utilizzo generico, quindi selezionare una serie di dimensioni della pagina risultante, ad esempio la serie Dv2. Ciascuna serie dispone di una tabella con specifiche di rete nell'ultima colonna con titolo, schede di rete Max / prestazioni rete previste (Mbps).

Il limite di velocità effettiva si applica alla macchina virtuale. La velocità effettiva è influenzata dai fattori seguenti:

- **Numero di interfacce di rete**: Il limite di larghezza di banda è cumulativo di tutto il traffico in uscita dalla macchina virtuale.

- **Rete accelerata**: Anche se la funzionalità può essere utile per ottenere il limite pubblicato, il limite non viene modificato.

- **Destinazione del traffico**: Tutte le destinazioni contano per il limite in uscita.

- **Protocollo**: Tutto il traffico in uscita su tutti i protocolli conta per il limite.

Oggetto [tabella di larghezza di banda massima per ogni tipo di macchina virtuale sono reperibili visitando questa pagina](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e facendo clic sul rispettivo tipo di macchina virtuale. In ogni pagina di tipo, una tabella visualizzerà il massimi schede di rete e larghezza di banda massima prevista.

Altre informazioni sulla larghezza di banda di rete della macchina virtuale sono reperibile in [larghezza di banda di rete macchina virtuale](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerazioni sulle prestazioni di Internet

Come illustrato in questo articolo, fattori su Internet e all'esterno del controllo di Azure possono influenzare le prestazioni di rete. Questi fattori sono:

- **Latenza**: Il tempo di round trip tra due destinazioni può dipendere da problemi nelle reti intermedi, il traffico non richiede la distanza "più breve" percorsi peering percorso possibili e non ottimale

- **Perdita di pacchetti**: Perdita di pacchetti può essere causata da congestione della rete, problemi di percorso fisico e i dispositivi di rete in esecuzione

- **Dimensioni/frammentazione MTU**: Frammentazione lungo il percorso può causare ritardi di arrivo dei dati o i pacchetti che arrivano non in ordine, che possono influire sul recapito dei pacchetti

Traceroute è un ottimo strumento per misurare le caratteristiche di prestazioni rete (ad esempio la perdita di pacchetti e latenza) ogni percorso di rete tra un dispositivo di origine e destinazione.

### <a name="network-design-considerations"></a>Considerazioni sulla progettazione di rete

Insieme a considerazioni precedenti, la topologia di una rete virtuale può influire sulle prestazioni della rete virtuale. Ad esempio, un hub e spoke progettazione che il traffico backhauls globalmente a una rete virtuale hub singolo verrà introducono latenza di rete e pertanto influire sulle prestazioni di rete complessiva. Analogamente, il numero di dispositivi di rete che attraversa il traffico di rete può influire sulle Latenza complessiva. Ad esempio, in una progettazione hub- spoke, se il traffico che passa attraverso uno spoke Appliance di rete virtuale e un'Appliance virtuale dell'Hub prima di transito a Internet, quindi latenza può essere introdotti dalle Appliance virtuali di rete.

### <a name="azure-regions-virtual-networks-and-latency"></a>Aree di Azure, le reti virtuali e la latenza

Aree di Azure sono costituite da più data center esistenti all'interno di un'area geografica generica. Questi Data Center potrebbe non essere fisicamente uno accanto a altro e in alcuni casi è necessario separarli con più di 10 chilometri. La rete virtuale è una sovrapposizione logica nella parte superiore di rete di centro dati fisico di Azure e una rete virtuale non implica qualsiasi topologia di rete specifico nel data center. Ad esempio, VM A e B di VM sono nella stessa rete virtuale e subnet, ma potrebbero essere in diversi rack, righe o anche i Data Center. Essi possono essere separati da feet di cavi in fibra ottica o chilometri cavi in fibra ottica. Questa realtà può introdurre latenza variabile (differenza di pochi millisecondi) tra VM diverse.

Questo posizionamento geografico e quindi la latenza tra due macchine virtuali, possono essere influenzate tramite la configurazione del set di disponibilità e le zone di disponibilità, tuttavia, distanza tra i Data Center in un'area è specifico dell'area geografica e influenzata principalmente dal topologia di Data Center nell'area.

### <a name="source-nat-port-exhaustion"></a>L'esaurimento delle porte NAT origine

Una distribuzione in Azure può comunicare con gli endpoint all'esterno di Azure nella rete Internet pubblica e/o dello spazio di IP pubblici. Quando un'istanza avvia la connessione in uscita, Azure associa dinamicamente l'indirizzo IP privato a un indirizzo IP pubblico. Dopo aver creato questo mapping, il traffico di ritorno per questo flusso con origine in uscita può raggiungere l'indirizzo IP privato da cui ha avuto origine il flusso.

Per ogni connessione in uscita, Azure Load Balancer deve mantenere questo mapping per un determinato periodo di tempo. La natura multi-tenant di Azure, mantenendo il mapping per ogni flusso in uscita per ogni macchina virtuale può essere a elevato utilizzo di risorse. Pertanto, sono presenti limiti che vengono definiti e basati sulla configurazione di rete virtuale di Azure. O dichiarato in modo più preciso - una VM di Azure è solo un determinato numero di connessioni in uscita in un determinato momento. Quando questi limiti vengono esauriti, quindi la macchina virtuale di Azure potranno apportare tutte le connessioni in uscita ulteriormente.

Questo comportamento è, tuttavia, può essere configurato. Per altre informazioni sulla [SNAT e SNAT porta esaurimento], vedere [questo articolo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Misurare le prestazioni di rete in Azure

Un numero di valori massimi di prestazioni in questo articolo è relative alla latenza di rete / round trip time (RTT) tra due macchine virtuali. In questa sezione offre alcuni suggerimenti su come testare la latenza/RTT, nonché prestazioni TCP e prestazioni di rete della macchina virtuale. I valori di TCP/IP e di rete illustrati sopra possono essere ottimizzati e testare le prestazioni usando le tecniche descritte di seguito. I valori della latenza, il valore MTU, MSS e dimensioni della finestra possono essere usati nei calcoli sopra elencati e valori massimi teorici possono essere confrontati i valori effettivi osservate durante il test.

### <a name="measure-round-trip-time-and-packet-loss"></a>Misurano il tempo di round trip e perdita di pacchetti

Le prestazioni TCP si basa principalmente su RTT e perdita di pacchetti. Il modo più semplice per misurare la perdita di pacchetti e RTT Usa l'utilità ping disponibile in Windows e Linux. L'output del comando ping visualizzerà latenza min/max/media tra un'origine e destinazione, nonché la perdita di pacchetti. Impostazione predefinita, ping Usa il protocollo ICMP. Per testare TCP RTT, PsPing può quindi essere utilizzato. Altre informazioni su PsPing sono disponibile all'indirizzo [questo collegamento](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Velocità effettiva di misure di una connessione TCP

NTttcp è uno strumento che consente di testare le prestazioni TCP di una macchina virtuale di Windows o Linux. Varie impostazioni TCP possono essere modificate e i vantaggi testati usando NTttcp. Altre informazioni su NTttcp sono reperibile nei collegamenti seguenti.

- [Larghezza di banda/velocità effettiva (NTttcp) di test](https://aka.ms/TestNetworkThroughput)

- [NTttcp Utility](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Misura effettiva della larghezza di banda di una macchina virtuale

Test delle prestazioni di diversi tipi di VM, la rete accelerata e così via, possono essere testate usando uno strumento denominato Iperf, disponibile in Linux e Windows. Iperf è possibile usare TCP o UDP per velocità effettiva complessiva di rete di test. Test di velocità effettiva TCP usando Iperf sono influenzato dai fattori illustrati in questo articolo (latenza, tempo RTT e così via). Pertanto, UDP può producono risultati migliori semplicemente verificando la massima velocità effettiva.

Sono disponibili informazioni aggiuntive seguenti:

- [Risoluzione dei problemi di prestazioni di rete di Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Come convalidare la velocità effettiva della VPN verso una rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Rilevare i comportamenti TCP inefficienti

I clienti di Azure possono vedere i pacchetti TCP con flag TCP (SACK, DUP ACK, RITRASMISSIONE e veloce lo trasmetta di nuovo) nelle acquisizioni di pacchetti che possono indicare problemi di prestazioni di rete. Questi pacchetti indicano in modo specifico le inefficienze di rete in seguito a perdita di pacchetti. Tuttavia, la perdita di pacchetti non è necessariamente a causa di problemi di prestazioni di Azure. Problemi di prestazioni potrebbe essere il risultato dell'applicazione, sistema operativo o altri problemi che potrebbero non essere direttamente correlati alla piattaforma Azure. È anche importante notare che alcuni ritrasmissione o ACK duplicati in una rete è normale, protocolli TCP sono stati compilati per l'affidabilità. E, evidenza di questi pacchetti TCP in un'acquisizione di pacchetti non indica necessariamente un problema di rete a livello di sistema a meno che non sono eccessive.

Tuttavia, si devono essere dichiarato in modo chiaro che questi tipi di pacchetto sono indicazioni che elaborate TCP non raggiungere le prestazioni massime: per motivi illustrati nelle altre sezioni.
