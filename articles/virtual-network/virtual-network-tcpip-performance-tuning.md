---
title: Ottimizzazione delle prestazioni TCP/IP per le macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle varie tecniche comuni di ottimizzazione delle prestazioni TCP/IP e sulle relative relazioni con le VM di Azure.
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
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297776"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Ottimizzazione delle prestazioni TCP/IP per macchine virtuali di Azure

Questo articolo illustra le tecniche di ottimizzazione delle prestazioni TCP/IP comuni e alcune considerazioni da considerare quando vengono usate per le macchine virtuali in esecuzione in Azure. Viene fornita una panoramica di base delle tecniche ed è possibile esplorare le modalità di ottimizzazione.

## <a name="common-tcpip-tuning-techniques"></a>Tecniche comuni di ottimizzazione di TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, frammentazione e offload di invio di grandi dimensioni

#### <a name="mtu"></a>MTU

L'unità di trasmissione massima (MTU) è il frame di dimensioni maggiori (pacchetto), specificato in byte, che può essere inviato tramite un'interfaccia di rete. La MTU è un'impostazione configurabile. Il valore MTU predefinito usato nelle macchine virtuali di Azure e l'impostazione predefinita nella maggior parte dei dispositivi di rete a livello globale sono 1.500 byte.

#### <a name="fragmentation"></a>Frammentazione

La frammentazione si verifica quando viene inviato un pacchetto che supera la MTU di un'interfaccia di rete. Lo stack TCP/IP suddividerà il pacchetto in parti più piccole (frammenti) conformi al MTU dell'interfaccia. La frammentazione si verifica a livello IP ed è indipendente dal protocollo sottostante, ad esempio TCP. Quando un pacchetto a 2.000 byte viene inviato su un'interfaccia di rete con un valore MTU pari a 1.500, il pacchetto verrà suddiviso in pacchetti da 1 1.500 byte e pacchetti da 1 500 byte.

I dispositivi di rete nel percorso tra un'origine e una destinazione possono eliminare pacchetti che superano la MTU o frammentano il pacchetto in parti più piccole.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Il bit non frammento in un pacchetto IP

Il bit not Fragment (DF) è un flag nell'intestazione del protocollo IP. Il bit DF indica che i dispositivi di rete sul percorso tra il mittente e il destinatario non devono frammentare il pacchetto. Questo bit può essere impostato per diversi motivi. Per un esempio, vedere la sezione "individuazione MTU del percorso" di questo articolo. Quando un dispositivo di rete riceve un pacchetto con il bit not Fragment impostato e il pacchetto supera l'interfaccia MTU del dispositivo, il comportamento standard è che il dispositivo elimini il pacchetto. Il dispositivo invia un messaggio di frammentazione ICMP necessario all'origine originale del pacchetto.

#### <a name="performance-implications-of-fragmentation"></a>Implicazioni relative alle prestazioni della frammentazione

La frammentazione può avere implicazioni negative sulle prestazioni. Uno dei motivi principali per l'effetto sulle prestazioni è l'impatto della CPU/memoria della frammentazione e il riassemblaggio dei pacchetti. Quando un dispositivo di rete deve frammentare un pacchetto, dovrà allocare risorse di CPU/memoria per eseguire la frammentazione.

La stessa cosa si verifica quando il pacchetto viene riassemblato. Il dispositivo di rete deve archiviare tutti i frammenti fino a quando non vengono ricevuti, in modo che possano riassemblarli nel pacchetto originale. Questo processo di frammentazione e riassemblaggio può anche causare una latenza.

L'altra possibile implicazione delle prestazioni negative della frammentazione è che i pacchetti frammentati potrebbero arrivare fuori dall'ordine. Quando i pacchetti vengono ricevuti in ordine non ordinato, alcuni tipi di dispositivi di rete possono eliminarli. In tal caso, l'intero pacchetto deve essere ritrasmesso.

I frammenti vengono in genere eliminati da dispositivi di sicurezza come i firewall di rete o quando i buffer di ricezione di un dispositivo di rete sono esauriti. Quando i buffer di ricezione di un dispositivo di rete sono esauriti, un dispositivo di rete sta provando a riassemblare un pacchetto frammentato ma non dispone delle risorse necessarie per archiviare e riassumere il pacchetto.

La frammentazione può essere considerata un'operazione negativa, ma il supporto per la frammentazione è necessario quando si connette reti diverse tramite Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Vantaggi e conseguenze della modifica del MTU

In generale, è possibile creare una rete più efficiente aumentando la MTU. Ogni pacchetto trasmesso presenta informazioni di intestazione che vengono aggiunte al pacchetto originale. Quando la frammentazione crea più pacchetti, si verifica un sovraccarico dell'intestazione e la rete risulta meno efficiente.

Ecco un esempio. Le dimensioni dell'intestazione Ethernet sono pari a 14 byte più una sequenza di controllo frame a 4 byte per garantire la coerenza dei frame. Se viene inviato un pacchetto di 1 2.000 byte, nella rete vengono aggiunti 18 byte di overhead Ethernet. Se il pacchetto è frammentato in un pacchetto a 1.500 byte e un pacchetto da 500 byte, ogni pacchetto avrà 18 byte di intestazione Ethernet, un totale di 36 byte.

Tenere presente che l'aumento del valore MTU non creerà necessariamente una rete più efficiente. Se un'applicazione invia solo pacchetti da 500 byte, sarà presente lo stesso overhead dell'intestazione se la MTU è 1.500 byte o 9.000 byte. La rete diventerà più efficiente solo se usa dimensioni dei pacchetti maggiori interessate dalla MTU.

#### <a name="azure-and-vm-mtu"></a>Azure e MTU della macchina virtuale

Il valore MTU predefinito per le macchine virtuali di Azure è 1.500 byte. Lo stack di rete virtuale di Azure tenterà di frammentare un pacchetto a 1.400 byte.

Si noti che lo stack di rete virtuale non è intrinsecamente inefficiente perché frammenta i pacchetti a 1.400 byte anche se le macchine virtuali hanno un valore MTU pari a 1.500. Una percentuale elevata di pacchetti di rete è molto inferiore a 1.400 o 1.500 byte.

#### <a name="azure-and-fragmentation"></a>Azure e frammentazione

Lo stack di rete virtuale è configurato per eliminare i frammenti "fuori ordine", ovvero i pacchetti frammentati che non arrivano nell'ordine frammentato originale. Questi pacchetti vengono eliminati principalmente a causa di una vulnerabilità di sicurezza di rete annunciata nel 2018 novembre denominata FragmentSmack.

FragmentSmack è un difetto nel modo in cui il kernel Linux ha gestito il riassemblaggio dei pacchetti IPv4 e IPv6 frammentati. Un utente malintenzionato remoto può utilizzare questo difetto per attivare operazioni di riassemblaggio dei frammenti costosi, che potrebbero causare una maggiore CPU e un attacco Denial of Service sul sistema di destinazione.

#### <a name="tune-the-mtu"></a>Ottimizzare il MTU

È possibile configurare un MTU della macchina virtuale di Azure, come in qualsiasi altro sistema operativo. È tuttavia necessario considerare la frammentazione che si verifica in Azure, descritta in precedenza, quando si configura un valore MTU.

Non invitiamo i clienti ad aumentare la MTU della macchina virtuale. Questa discussione ha lo scopo di illustrare i dettagli del modo in cui Azure implementa MTU ed esegue la frammentazione.

> [!IMPORTANT]
>L'aumento della MTU non è noto per migliorare le prestazioni e potrebbe avere un impatto negativo sulle prestazioni dell'applicazione.
>
>

#### <a name="large-send-offload"></a>Offload invio di grandi dimensioni

Un offload di invio di grandi dimensioni (LSO) può migliorare le prestazioni di rete mediante l'offload della segmentazione dei pacchetti alla scheda Ethernet. Quando LSO è abilitato, lo stack TCP/IP crea un pacchetto TCP di grandi dimensioni e lo invia alla scheda Ethernet per la segmentazione prima di inoltrarlo. Il vantaggio di LSO è il fatto che può liberare la CPU dalla segmentazione dei pacchetti in dimensioni conformi al MTU e dall'offload dell'elaborazione all'interfaccia Ethernet in cui viene eseguita nell'hardware. Per altre informazioni sui vantaggi di LSO, vedere [supporto dell'offload di grandi dimensioni](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Quando LSO è abilitato, i clienti di Azure possono visualizzare grandi dimensioni dei frame quando eseguono acquisizioni di pacchetti. Queste dimensioni di frame di grandi dimensioni possono comportare la presenza di una frammentazione da parte dei clienti o l'utilizzo di una grande MTU in caso contrario. Con LSO, la scheda Ethernet può annunciare una dimensione massima del segmento (MSS) superiore allo stack TCP/IP per creare un pacchetto TCP più grande. L'intero frame non segmentato viene quindi inviato alla scheda Ethernet e sarà visibile in un'acquisizione di pacchetti eseguita sulla macchina virtuale. Il pacchetto verrà tuttavia suddiviso in molti frame più piccoli dalla scheda Ethernet, in base alla MTU della scheda Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Ridimensionamento della finestra MSS TCP e il

#### <a name="tcp-maximum-segment-size"></a>Dimensioni massime del segmento TCP

La dimensione massima del segmento TCP (MSS) è un'impostazione che limita le dimensioni dei segmenti TCP, evitando la frammentazione dei pacchetti TCP. I sistemi operativi utilizzeranno in genere questa formula per impostare MSS:

`MSS = MTU - (IP header size + TCP header size)`

L'intestazione IP e l'intestazione TCP sono di 20 byte ciascuno o di 40 byte totali. Quindi, un'interfaccia con un valore MTU di 1.500 avrà un MSS di 1.460. Ma il MSS è configurabile.

Questa impostazione viene concordata con l'handshake TCP a tre vie quando una sessione TCP viene configurata tra un'origine e una destinazione. Entrambi i lati inviano un valore MSS e il minore dei due viene usato per la connessione TCP.

Tenere presente che i MTU di origine e di destinazione non sono gli unici fattori che determinano il valore MSS. I dispositivi di rete intermedi, come i gateway VPN, incluso il gateway VPN di Azure, possono regolare la MTU indipendentemente dall'origine e dalla destinazione per garantire prestazioni di rete ottimali.

#### <a name="path-mtu-discovery"></a>Individuazione del percorso MTU

MSS è negoziato, ma potrebbe non indicare l'effettivo MSS che è possibile usare. Ciò è dovuto al fatto che altri dispositivi di rete nel percorso tra l'origine e la destinazione potrebbero avere un valore MTU inferiore rispetto all'origine e alla destinazione. In questo caso, il dispositivo il cui MTU è inferiore a quello del pacchetto eliminerà il pacchetto. Il dispositivo invierà un messaggio di frammentazione ICMP necessario (tipo 3, codice 4) che contiene il relativo valore MTU. Questo messaggio ICMP consente all'host di origine di ridurre il proprio percorso MTU in modo appropriato. Il processo è denominato percorso MTU Discovery (il).

Il processo il non è efficiente e influiscono sulle prestazioni della rete. Quando si inviano pacchetti che superano la MTU del percorso di rete, è necessario ritrasmettere i pacchetti con un MSS inferiore. Se il mittente non riceve il messaggio di frammentazione ICMP necessario, probabilmente a causa di un firewall di rete nel percorso (noto come *il Blackhole*), il mittente non sa che deve abbassare il MSS e ritrasmette continuamente il pacchetto. Questo è il motivo per cui non è consigliabile aumentare la MTU della VM di Azure.

#### <a name="vpn-and-mtu"></a>VPN e MTU

Se si usano macchine virtuali che eseguono l'incapsulamento (ad esempio VPN IPsec), è necessario tenere presenti alcune considerazioni aggiuntive sulla dimensione del pacchetto e sull'MTU. Le VPN aggiungono più intestazioni ai pacchetti, che aumentano le dimensioni del pacchetto e richiedono un MSS più piccolo.

Per Azure, è consigliabile impostare la pressione del protocollo MSS TCP su 1.350 byte e l'interfaccia del tunnel MTU su 1.400. Per ulteriori informazioni, vedere la [pagina dispositivi VPN e parametri IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latenza, tempo di round trip e scalabilità della finestra TCP

#### <a name="latency-and-round-trip-time"></a>Latenza e tempo di round trip

La latenza di rete è regolata dalla velocità della luce su una rete in fibra ottica. La velocità effettiva di rete del protocollo TCP è regolata anche dal tempo di round trip (RTT) tra due dispositivi di rete.

| | | | |
|-|-|-|-|
|**Route**|**Distanza**|**Tempo unidirezionale**|**RTT**|
|Da New York a San Francisco|4\.148 km|21 ms|42 ms|
|Da New York a Londra|5\.585 km|28 ms|56 ms|
|Da New York a Sydney|15.993 km|80 ms|160 ms|

Questa tabella mostra la distanza linea retta tra due posizioni. In reti la distanza è in genere più lunga della distanza linea retta. Ecco una semplice formula per calcolare il valore RTT minimo come regolato dalla velocità della luce:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

È possibile utilizzare 200 per la velocità di propagazione. Si tratta della distanza, in metri, con cui la luce viene spostata in un millisecondo.

Prendiamo come esempio New York a San Francisco. La distanza linea retta è 4.148 km. Inserendo tale valore nell'equazione, si ottiene quanto segue:

`Minimum RTT = 2 * (4,148 / 200)`

L'output dell'equazione è in millisecondi.

Per ottenere le migliori prestazioni di rete, l'opzione logica consiste nel selezionare le destinazioni con la distanza più breve tra di esse. È inoltre consigliabile progettare la rete virtuale per ottimizzare il percorso di traffico e ridurre la latenza. Per ulteriori informazioni, vedere la sezione "Considerazioni sulla progettazione della rete" di questo articolo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Effetti sulla latenza e sul tempo di round trip su TCP

Il tempo di Round Trip ha un effetto diretto sulla velocità effettiva massima TCP. Nel protocollo TCP, le *dimensioni della finestra* corrispondono alla quantità massima di traffico che può essere inviata tramite una connessione TCP prima che il mittente debba ricevere il riconoscimento dal ricevitore. Se il protocollo MSS TCP è impostato su 1.460 e le dimensioni della finestra TCP sono impostate su 65.535, il mittente può inviare i pacchetti 45 prima di ricevere il riconoscimento dal ricevitore. Se il mittente non riceve la conferma, i dati vengono ritrasmessi. Ecco la formula:

`TCP window size / TCP MSS = packets sent`

In questo esempio 65.535/1.460 viene arrotondato per eccesso a 45.

Questo stato "in attesa di riconoscimento", un meccanismo per garantire un recapito affidabile dei dati, è ciò che causa la velocità effettiva TCP da RTT. Più a lungo il mittente resta in attesa di riconoscimento, maggiore è il tempo necessario per attendere prima di inviare più dati.

Di seguito è illustrata la formula per calcolare la velocità effettiva massima di una singola connessione TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Questa tabella mostra la velocità effettiva massima in megabyte/al secondo di una singola connessione TCP. Per migliorare la leggibilità, i megabyte vengono utilizzati per l'unità di misura.

| | | | |
|-|-|-|-|
|**Dimensioni finestra TCP (byte)**|**Latenza RTT (MS)**|**Velocità effettiva massima di megabyte/secondo**|**Velocità effettiva massima di megabit/secondo**|
|65.535|1|65,54|524,29|
|65.535|30|2,18|17,48|
|65.535|60|1.09|8,74|
|65.535|90|73|5,83|
|65.535|120|55|4.37|

Se i pacchetti vengono persi, la velocità effettiva massima di una connessione TCP verrà ridotta mentre il mittente ritrasmette i dati già inviati.

#### <a name="tcp-window-scaling"></a>Scalabilità della finestra TCP

La scalabilità della finestra TCP è una tecnica che aumenta in modo dinamico le dimensioni della finestra TCP per consentire l'invio di più dati prima che sia necessaria una conferma. Nell'esempio precedente, vengono inviati 45 pacchetti prima che venisse richiesta una conferma. Se si aumenta il numero di pacchetti che è possibile inviare prima che sia necessario un riconoscimento, si riduce il numero di volte in cui un mittente è in attesa di riconoscimento, aumentando la velocità effettiva massima TCP.

Questa tabella illustra le relazioni seguenti:

| | | | |
|-|-|-|-|
|**Dimensioni finestra TCP (byte)**|**Latenza RTT (MS)**|**Velocità effettiva massima di megabyte/secondo**|**Velocità effettiva massima di megabit/secondo**|
|65.535|30|2,18|17,48|
|131.070|30|4.37|34,95|
|262.140|30|8,74|69,91|
|524.280|30|17,48|139,81|

Tuttavia, il valore dell'intestazione TCP per la dimensione della finestra TCP è di soli 2 byte, il che significa che il valore massimo per una finestra di ricezione è 65.535. Per aumentare le dimensioni massime della finestra, è stato introdotto un fattore di scala della finestra TCP.

Il fattore di scala è anche un'impostazione che è possibile configurare in un sistema operativo. Di seguito è illustrata la formula per il calcolo delle dimensioni della finestra TCP usando i fattori di scala:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Ecco il calcolo per un fattore di scala della finestra 3 e una dimensione della finestra di 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Un fattore di scala pari a 14 comporta una dimensione della finestra TCP pari a 14 (l'offset massimo consentito). Le dimensioni della finestra TCP saranno di 1.073.725.440 byte (8,5 Gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Supporto per il ridimensionamento delle finestre TCP

Windows può impostare diversi fattori di scalabilità per diversi tipi di connessione. (Le classi di connessioni includono Data Center, Internet e così via). Usare il `Get-NetTCPConnection` comando di PowerShell per visualizzare il tipo di connessione per la scalabilità della finestra:

```powershell
Get-NetTCPConnection
```

È possibile usare il `Get-NetTCPSetting` comando di PowerShell per visualizzare i valori di ogni classe:

```powershell
Get-NetTCPSetting
```

È possibile impostare le dimensioni iniziali della finestra TCP e il fattore di scalabilità TCP in `Set-NetTCPSetting` Windows tramite il comando di PowerShell. Per ulteriori informazioni, vedere [set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Queste sono le impostazioni TCP valide per `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Fattore di scala**|**Moltiplicatore di scala**|**Formula per<br/>calcolare le dimensioni massime della finestra**|
|Disabled|Nessuna|Nessuna|Dimensioni finestra|
|Con restrizioni|4|2 ^ 4|Dimensioni finestra * (2 ^ 4)|
|Con restrizioni elevata|2|2^2|Dimensioni finestra * (2 ^ 2)|
|Normale|8|2 ^ 8|Dimensioni finestra * (2 ^ 8)|
|Sperimentale|14|2^14|Dimensioni finestra * (2 ^ 14)|

Queste impostazioni sono le più probabilmente influiscono sulle prestazioni TCP, ma tenere presente che molti altri fattori in Internet, al di fuori del controllo di Azure, possono influire sulle prestazioni TCP.

#### <a name="increase-mtu-size"></a>Aumentare le dimensioni MTU

Poiché un valore MTU più grande significa un MSS più grande, è possibile chiedersi se l'aumento del valore MTU può aumentare le prestazioni del protocollo TCP. La risposta è probabilmente negativa. Sono disponibili vantaggi e svantaggi per le dimensioni del pacchetto oltre al solo traffico TCP. Come illustrato in precedenza, i fattori più importanti che influiscono sulle prestazioni della velocità effettiva TCP sono le dimensioni della finestra TCP, la perdita di pacchetti e RTT.

> [!IMPORTANT]
> Non è consigliabile che i clienti di Azure modifichino il valore MTU predefinito nelle macchine virtuali.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Rete accelerata e Receive-Side Scaling

#### <a name="accelerated-networking"></a>Rete accelerata

Le funzioni di rete delle macchine virtuali hanno in passato un utilizzo intensivo della CPU sia nella VM guest che nell'hypervisor/host. Ogni pacchetto che transita attraverso l'host viene elaborato in software dalla CPU dell'host, inclusi tutti gli decapsulamento di rete virtuale e l'incapsulamento. Quindi, più il traffico passa attraverso l'host, più elevato è il carico della CPU. E se la CPU dell'host è occupata con altre operazioni, questo influirà anche sulla velocità effettiva e sulla latenza della rete. Azure risolve questo problema con la rete accelerata.

La rete accelerata offre una latenza di rete bassissimo coerente tramite l'hardware programmabile interno di Azure e tecnologie come SR-IOV. La rete accelerata sposta gran parte dello stack di rete definito dal software di Azure dalle CPU e in SmartNICs basato su FPGA. Questa modifica consente alle applicazioni dell'utente finale di recuperare i cicli di calcolo, riducendo il carico della macchina virtuale, riducendo l'instabilità e l'incoerenza nella latenza. In altre parole, le prestazioni possono essere più deterministiche.

Rete accelerata consente di migliorare le prestazioni consentendo alla VM guest di ignorare l'host e stabilire un percorso di DataPath direttamente con il SmartNIC di un host. Ecco alcuni vantaggi della rete accelerata:

- **Latenza più bassa/pacchetti superiori al secondo (PPS)** : La rimozione del Commuter virtuale dal percorso di DataPath Elimina i pacchetti temporali spesi nell'host per l'elaborazione dei criteri e aumenta il numero di pacchetti che possono essere elaborati nella macchina virtuale.

- **Jitter ridotto**: L'elaborazione del Commuter virtuale dipende dalla quantità di criteri che devono essere applicati e dal carico di lavoro della CPU che esegue l'elaborazione. L'offload dell'applicazione dei criteri all'hardware consente di rimuovere tale variabilità distribuendo i pacchetti direttamente alla macchina virtuale, eliminando la comunicazione da host a macchina virtuale e tutti gli interrupt software e i cambi di contesto.

- **Riduzione dell'utilizzo della CPU**: Ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

Per usare la rete accelerata, è necessario abilitarla in modo esplicito in ogni VM applicabile. Per istruzioni, vedere [creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) .

#### <a name="receive-side-scaling"></a>Receive-Side Scaling

Receive-Side Scaling (RSS) è una tecnologia di driver di rete che distribuisce la ricezione del traffico di rete in modo più efficiente distribuendo l'elaborazione della ricezione tra più CPU in un sistema multiprocessore. In termini semplici, RSS consente a un sistema di elaborare un traffico più ricevuto, perché usa tutte le CPU disponibili anziché una sola. Per una discussione più tecnica su RSS, vedere [Introduzione a Receive-Side Scaling](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Per ottenere prestazioni ottimali quando è abilitata la funzionalità rete accelerata in una macchina virtuale, è necessario abilitare RSS. RSS può anche fornire vantaggi alle macchine virtuali che non usano la rete accelerata. Per una panoramica su come determinare se RSS è abilitato e come abilitarlo, vedere ottimizzare la [velocità effettiva della rete per le macchine virtuali di Azure](https://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TIME_WAIT TCP e TIME_WAIT

TCP TIME_WAIT è un'altra impostazione comune che influiscono sulle prestazioni di rete e dell'applicazione. Nelle VM occupate che aprono e chiudono molti socket, sia come client che come server (IP di origine: porta di origine + IP di destinazione: porta di destinazione), durante il normale funzionamento di TCP, un socket specificato può finire nello stato TIME_WAIT per molto tempo. Lo stato TIME_WAIT ha lo scopo di consentire il recapito di eventuali dati aggiuntivi su un socket prima di chiuderlo. Pertanto, gli stack TCP/IP impediscono in genere il riutilizzo di un socket eliminando automaticamente il pacchetto SYN TCP del client.

La quantità di tempo in cui un socket è in TIME_WAIT è configurabile. Può variare da 30 secondi a 240 secondi. I socket sono una risorsa finita e il numero di socket che possono essere usati in un determinato momento è configurabile. Il numero di socket disponibili è in genere pari a circa 30.000. Se vengono utilizzati i socket disponibili o se i client e i server presentano impostazioni TIME_WAIT non corrispondenti e una macchina virtuale tenta di riutilizzare un socket in uno stato TIME_WAIT, le nuove connessioni avranno esito negativo perché i pacchetti SYN TCP vengono eliminati automaticamente.

Il valore per l'intervallo di porte per i socket in uscita è in genere configurabile nello stack TCP/IP di un sistema operativo. Lo stesso vale per le impostazioni TCP TIME_WAIT e il riutilizzo dei socket. La modifica di questi numeri può migliorare potenzialmente la scalabilità. Tuttavia, a seconda della situazione, queste modifiche potrebbero causare problemi di interoperabilità. È necessario prestare attenzione se si modificano questi valori.

Per risolvere questo limite di scalabilità, è possibile usare TIME_WAIT Assassination. L'assassinio TIME_WAIT consente il riutilizzo di un socket in determinate situazioni, ad esempio quando il numero di sequenza nel pacchetto IP della nuova connessione supera il numero di sequenza dell'ultimo pacchetto della connessione precedente. In questo caso, il sistema operativo consentirà la creazione della nuova connessione (accetterà il nuovo SYN/ACK) e forzerà la chiusura della connessione precedente che si trovava in uno stato TIME_WAIT. Questa funzionalità è supportata nelle macchine virtuali Windows in Azure. Per informazioni sul supporto in altre macchine virtuali, rivolgersi al fornitore del sistema operativo.

Per informazioni sulla configurazione delle impostazioni TIME_WAIT TCP e dell'intervallo di porte di origine, vedere [impostazioni che possono essere modificate per migliorare le prestazioni di rete](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fattori della rete virtuale che possono influire sulle prestazioni

### <a name="vm-maximum-outbound-throughput"></a>Velocità effettiva massima in uscita VM

Azure offre un'ampia gamma di dimensioni e tipi di macchine virtuali, ognuna con una diversa combinazione di funzionalità per le prestazioni. Una di queste funzionalità è la velocità effettiva di rete (o larghezza di banda), misurata in megabit al secondo (Mbps). Poiché le macchine virtuali sono ospitate su hardware condiviso, la capacità di rete deve essere condivisa equamente tra le macchine virtuali utilizzando lo stesso hardware. Alle macchine virtuali più grandi viene allocata una larghezza di banda maggiore rispetto alle macchine virtuali più piccole

La larghezza di banda di rete allocata a ogni macchina virtuale è misurata sul traffico in uscita dalla macchina virtuale. Tutto il traffico di rete che esce dalla macchina virtuale viene conteggiato per il limite allocato, indipendentemente dalla destinazione. Se, ad esempio, una macchina virtuale ha un limite di 1.000 Mbps, questo limite si applica se il traffico in uscita è destinato a un'altra macchina virtuale nella stessa rete virtuale o a un altro all'esterno di Azure.

Il traffico in ingresso non viene misurato o limitato direttamente. Tuttavia, esistono altri fattori, ad esempio i limiti di CPU e archiviazione, che possono influire sulla capacità di una macchina virtuale di elaborare i dati in ingresso.

La funzionalità rete accelerata è progettata per migliorare le prestazioni di rete, tra cui latenza, velocità effettiva e utilizzo della CPU. La rete accelerata può migliorare la velocità effettiva di una macchina virtuale, ma può farlo solo fino alla larghezza di banda allocata della macchina virtuale.

Alle macchine virtuali di Azure è associata almeno un'interfaccia di rete. Potrebbero essere presenti diverse. La larghezza di banda allocata a una macchina virtuale è la somma di tutto il traffico in uscita tra tutte le interfacce di rete collegate alla macchina. In altre parole, la larghezza di banda viene allocata in base alle singole macchine virtuali, indipendentemente dal numero di interfacce di rete collegate alla macchina.

La velocità effettiva in uscita prevista e il numero di interfacce di rete supportate da ogni dimensione di VM sono descritte in dettaglio in [dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Per visualizzare la velocità effettiva massima, selezionare un tipo come **utilizzo generico**, quindi individuare la sezione relativa alla serie di dimensioni nella pagina risultante (ad esempio, "dv2-Series"). Per ogni serie è presente una tabella che fornisce le specifiche di rete nell'ultima colonna, denominata "Max NIC/expected Network Bandwidth (Mbps)".

Il limite di velocità effettiva si applica alla macchina virtuale. La velocità effettiva non è influenzata da questi fattori:

- **Numero di interfacce di rete**: Il limite di larghezza di banda si applica alla somma di tutto il traffico in uscita dalla macchina virtuale.

- **Rete accelerata**: Sebbene questa funzionalità possa essere utile per raggiungere il limite pubblicato, non modifica il limite.

- **Destinazione del traffico**: Tutte le destinazioni vengono conteggiate verso il limite in uscita.

- **Protocollo**: Tutto il traffico in uscita su tutti i protocolli viene conteggiato fino al limite.

Per altre informazioni, vedere [larghezza di banda di rete della macchina virtuale](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerazioni sulle prestazioni Internet

Come illustrato in questo articolo, i fattori su Internet e al di fuori del controllo di Azure possono influire sulle prestazioni della rete. Ecco alcuni dei fattori seguenti:

- **Latenza**: Il tempo di round trip tra due destinazioni può essere influenzato da problemi nelle reti intermedie, dal traffico che non accetta il percorso di distanza "più breve" e da percorsi di peering non ottimali.

- **Perdita di pacchetti**: La perdita di pacchetti può essere causata dalla congestione della rete, dai problemi del percorso fisico e dalle sottoprestazioni dei dispositivi di rete.

- **Dimensioni/frammentazione MTU**: La frammentazione lungo il tracciato può causare ritardi nell'arrivo dei dati o nei pacchetti che arrivano senza ordine, che possono influire sul recapito dei pacchetti.

Traceroute è uno strumento efficace per misurare le caratteristiche delle prestazioni di rete, ad esempio la perdita di pacchetti e la latenza, lungo ogni percorso di rete tra un dispositivo di origine e un dispositivo di destinazione.

### <a name="network-design-considerations"></a>Considerazioni sulla progettazione della rete

Insieme alle considerazioni illustrate in precedenza in questo articolo, la topologia di una rete virtuale può influire sulle prestazioni della rete. Ad esempio, una progettazione hub e spoke che consente di eseguire il backhaul del traffico a livello globale in una rete virtuale a hub singolo introdurrà la latenza di rete, che influirà sulle prestazioni complessive della rete.

Il numero di dispositivi di rete che passano attraverso il traffico di rete può influire anche sulla latenza complessiva. Ad esempio, in una progettazione hub e spoke, se il traffico passa attraverso un'appliance virtuale di rete spoke e un'appliance virtuale Hub prima del passaggio a Internet, le appliance virtuali di rete possono introdurre latenza.

### <a name="azure-regions-virtual-networks-and-latency"></a>Aree di Azure, reti virtuali e latenza

Le aree di Azure sono costituite da più data center che si trovano all'interno di un'area geografica generale. Questi Data Center potrebbero non essere fisicamente vicini tra loro. In alcuni casi sono separati da un importo di 10 chilometri. La rete virtuale è una sovrapposizione logica nella parte superiore della rete dei data center fisici di Azure. Una rete virtuale non implica alcuna topologia di rete specifica all'interno del Data Center.

Ad esempio, due macchine virtuali che si trovano nella stessa rete virtuale e nella stessa subnet potrebbero trovarsi in rack, righe o anche data center diversi. Possono essere separati da piedi di cavi in fibra ottica o da chilometri di cavi in fibra ottica. Questa variazione potrebbe introdurre una latenza variabile (una differenza di pochi millisecondi) tra macchine virtuali diverse.

La posizione geografica delle macchine virtuali e la potenziale latenza risultante tra due macchine virtuali possono essere influenzate dalla configurazione dei set di disponibilità e zone di disponibilità. Tuttavia, la distanza tra i Data Center in un'area è specifica dell'area e principalmente influenzata dalla topologia del data center nell'area geografica.

### <a name="source-nat-port-exhaustion"></a>Esaurimento delle porte NAT di origine

Una distribuzione in Azure può comunicare con endpoint all'esterno di Azure sulla rete Internet pubblica e/o nello spazio IP pubblico. Quando un'istanza avvia una connessione in uscita, Azure esegue in modo dinamico il mapping dell'indirizzo IP privato a un indirizzo IP pubblico. Dopo che Azure ha creato questo mapping, il traffico di ritorno per il flusso originato in uscita può raggiungere anche l'indirizzo IP privato da cui ha avuto origine il flusso.

Per ogni connessione in uscita, il Azure Load Balancer deve mantenere questo mapping per un certo periodo di tempo. Con la natura multi-tenant di Azure, la gestione di questo mapping per ogni flusso in uscita per ogni macchina virtuale può richiedere un utilizzo intensivo delle risorse. Sono quindi presenti limiti impostati e basati sulla configurazione della rete virtuale di Azure. In alternativa, per affermare più precisamente, una macchina virtuale di Azure può solo creare un certo numero di connessioni in uscita in un determinato momento. Quando vengono raggiunti questi limiti, la macchina virtuale non sarà in grado di effettuare altre connessioni in uscita.

Questo comportamento è tuttavia configurabile. Per ulteriori informazioni sull'esaurimento delle porte SNAT e SNAT, vedere [questo articolo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Misurare le prestazioni di rete in Azure

Un numero massimo di prestazioni in questo articolo è correlato alla latenza di rete o al tempo di round trip (RTT) tra due macchine virtuali. In questa sezione vengono forniti alcuni suggerimenti su come testare la latenza/RTT e come verificare le prestazioni TCP e le prestazioni della rete VM. È possibile ottimizzare e testare le prestazioni dei valori TCP/IP e di rete descritti in precedenza usando le tecniche descritte in questa sezione. È possibile inserire i valori di latenza, MTU, MSS e dimensioni finestra nei calcoli forniti in precedenza e confrontare i valori massimi teorici con i valori effettivi osservati durante i test.

### <a name="measure-round-trip-time-and-packet-loss"></a>Misurare il tempo di round trip e la perdita di pacchetti

Le prestazioni TCP si basano principalmente su RTT e la perdita di pacchetti. L'utilità PING disponibile in Windows e Linux rappresenta il modo più semplice per misurare RTT e la perdita di pacchetti. L'output del comando PING indicherà la latenza minima/massima/media tra un'origine e una destinazione. Verrà visualizzata anche la perdita di pacchetti. Per impostazione predefinita, PING usa il protocollo ICMP. È possibile usare PsPing per testare il RTT TCP. Per ulteriori informazioni, vedere [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Misurare la velocità effettiva effettiva di una connessione TCP

NTttcp è uno strumento per il test delle prestazioni TCP di una macchina virtuale Linux o Windows. È possibile modificare diverse impostazioni TCP e quindi testare i vantaggi usando NTttcp. Per ulteriori informazioni, vedere le risorse:

- [Test della larghezza di banda/velocità effettiva (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Utilità NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Misurare la larghezza di banda effettiva di una macchina virtuale

È possibile testare le prestazioni di diversi tipi di VM, rete accelerata e così via usando uno strumento denominato iPerf. iPerf è disponibile anche in Linux e Windows. iPerf può usare TCP o UDP per testare la velocità effettiva complessiva della rete. i test della velocità effettiva TCP iPerf sono influenzati dai fattori descritti in questo articolo, ad esempio latenza e RTT. Quindi, UDP potrebbe produrre risultati migliori se si vuole solo testare la velocità effettiva massima.

Per altre informazioni, vedere questi articoli:

- [Risoluzione dei problemi relativi alle prestazioni della rete Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Come convalidare la velocità effettiva della VPN verso una rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Rilevare comportamenti TCP inefficienti

Nelle acquisizioni di pacchetti, i clienti di Azure potrebbero visualizzare pacchetti TCP con flag TCP (SACK, ACK DUP, ritrasmissione e ritrasmissione veloce) che potrebbero indicare problemi di prestazioni di rete. Questi pacchetti indicano in modo specifico le inefficienze di rete derivanti dalla perdita di pacchetti. La perdita di pacchetti, tuttavia, non è necessariamente causata da problemi di prestazioni di Azure. I problemi di prestazioni possono essere causati da problemi dell'applicazione, da problemi del sistema operativo o da altri problemi che potrebbero non essere direttamente correlati alla piattaforma Azure.

Tenere inoltre presente che alcuni ACK di ritrasmissione e duplicati sono normali in una rete. I protocolli TCP sono stati compilati per essere affidabili. L'evidenza di questi pacchetti TCP in un'acquisizione di pacchetti non indica necessariamente un problema di rete sistematico, a meno che non siano eccessivi.

Tuttavia, questi tipi di pacchetti sono indicazioni che la velocità effettiva TCP non raggiunge le prestazioni massime, per i motivi illustrati in altre sezioni di questo articolo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come eseguire l'ottimizzazione delle prestazioni TCP/IP per le macchine virtuali di Azure, è possibile leggere altre considerazioni per la [pianificazione delle reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) o per altre informazioni [sulla connessione e sulla configurazione di reti virtuali](https://docs.microsoft.com/azure/virtual-network/).
