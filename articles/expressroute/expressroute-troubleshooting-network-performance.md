---
title: Risoluzione dei problemi di prestazioni della rete virtuale di Microsoft Azure | Microsoft Docs
description: Questa pagina fornisce un metodo standardizzato di test delle prestazioni dei collegamenti di rete di Azure.
services: expressroute
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2017
ms.author: jonor
ms.openlocfilehash: 56f011632a2aa3ef0632efd5ace472c0fc79a329
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="troubleshooting-network-performance"></a>Risoluzione dei problemi di prestazioni di rete
## <a name="overview"></a>Panoramica
Azure offre metodi stabili e veloci per connettersi ad Azure dalla rete locale. Metodi come VPN da sito a sito ed ExpressRoute vengono usati correttamente dai clienti di piccole e grandi dimensioni per eseguire le attività di business in Azure. Ma cosa succede quando le prestazioni non sono all'altezza delle aspettative o delle esperienze passate? Questo documento consente di standardizzare come testare e pianificare il proprio ambiente specifico.

Questo documento mostra come testare la latenza di rete e la larghezza di banda tra due host in modo semplice e coerente. Fornisce inoltre suggerimenti su come esaminare la rete di Azure e isolare le aree problematiche. Lo script e gli strumenti PowerShell presentati richiedono due host sulla rete (su ciascuna estremità del collegamento sottoposto a test). Uno degli host deve avere Windows Server o Windows Desktop, l'altro può avere Windows o Linux. 

>[!NOTE]
>L'approccio alla risoluzione dei problemi, gli strumenti e i metodi usati variano in base alle preferenze personali. Questo documento descrive gli strumenti e l'approccio usati più di frequente. L'approccio dell'utente sarà probabilmente diverso, è normale considerare diversi approcci per risolvere i problemi. Tuttavia, se non si dispone di un approccio consolidato, questo documento può introdurre l'utente allo sviluppo di metodi, strumenti e preferenze personalizzati per la risoluzione dei problemi di rete.
>
>

## <a name="network-components"></a>Componenti di rete
Prima di approfondire la risoluzione dei problemi, esaminiamo alcuni componenti e termini comuni. Attraverso questa discussione verrà preso in esame ogni componente della catena end-to-end che fornisce connettività in Azure.
[![1]][1]

In generale, descriverò tre domini di routing di rete principali:

- La rete di Azure (cloud blu a destra)
- Internet o WAN (cloud verde al centro)
- La rete aziendale (cloud rosa chiaro a sinistra)

Diamo una breve occhiata a ciascun componente del diagramma, da destra a sinistra:
 - **Virtual Machine**: il server può avere più schede di interfaccia di rete. Verificare che le route statiche, le route predefinite e le impostazioni del sistema operativo stiano inviando e ricevendo il traffico nel modo previsto. Inoltre, ogni SKU della macchina virtuale ha una limitazione della larghezza di banda. Se si usa uno SKU della macchina virtuale più piccolo, il traffico è limitato dalla larghezza di banda disponibile per la scheda di interfaccia di rete. In genere nei test uso un DS5v2 (si consiglia quindi di eliminarlo al termine del test per risparmiare denaro) in modo da garantire una larghezza di banda sufficiente alla macchina virtuale.
 - **NIC**: assicurarsi di conoscere l'indirizzo IP privato assegnato alla scheda di interfaccia di rete.
 - **NIC NSG**: è possibile applicare gruppi di sicurezza di rete specifici a livello della scheda di interfaccia di rete. Verificare che il set di regole dei gruppi di sicurezza di rete sia appropriato per il traffico da passare. Ad esempio, assicurarsi che le porte 5201 per iPerf, 3389 per RDP o 22 per SSH siano aperte per consentire il passaggio del traffico di test.
 - **VNet Subnet**: la scheda di interfaccia di rete è assegnata a una subnet specifica. Assicurarsi di conoscere qual'è e quali sono le regole ad essa associate.
 - **Subnet NSG**: proprio come la scheda di interfaccia di rete, anche i gruppi di sicurezza di rete possono essere applicati alla subnet. Verificare che il set di regole dei gruppi di sicurezza di rete sia appropriato per il traffico da passare. (per il traffico che entra nella scheda di interfaccia di rete, si applica prima la Subnet NSG, poi la NIC NSG, mentre per il traffico che lascia la macchina virtuale, la NIC NSG viene applicata per prima, quindi entra in gioco la Subnet NSG).
 - **Subnet UDR**: le route definite dall'utente possono indirizzare il traffico verso un hop intermedio (ad esempio un firewall o un servizio di bilanciamento del carico). Verificare che sia presente una route definita dall'utente per il traffico e, in tal caso, dove va e cosa farà quell'hop successivo al proprio traffico. (ad esempio, un firewall può consentire il passaggio di un traffico mentre può negarne un altro tra gli stessi due host).
 - **Subnet gateway / NSG / UDR**: proprio come la subnet della macchina virtuale, la subnet del gateway può avere gruppi di sicurezza di rete e route definite dall'utente. Verificare la loro possibile presenza e il loro impatto sul traffico.
 - **VNet Gateway (ExpressRoute)**: dopo aver abilitato la VPN o il peering (ExpressRoute) non vi sono molte impostazioni che possono influenzare il routing del traffico o l'esistenza di questo routing. Se sono presenti più circuiti ExpressRoute o tunnel VPN collegati allo stesso VNet Gateway, è necessario conoscere l'impostazione del peso della connessione in quanto influisce sulle preferenze di connessione e sul percorso del traffico.
 - **Route Filter** (non illustrato): un filtro di routing si applica solo al Peering Microsoft su ExpressRoute, ma è fondamentale verificare che le route previste non vengano visualizzate sul Peering Microsoft. 

A questo punto si è nella parte WAN del collegamento. Questo dominio di routing può essere il provider di servizi, la WAN aziendale o Internet. Molti hop, tecnologie e aziende coinvolti in questi collegamenti possono rendere piuttosto difficile la risoluzione dei problemi. Spesso si fa in modo di escludere Azure e le reti aziendali prima di passare a questa moltitudine di aziende e hop.

Nel diagramma precedente all'estrema sinistra è collocata la rete aziendale dell'utente. A seconda delle dimensioni dell'azienda, questo dominio di routing può consolidare alcuni dispositivi di rete tra l'utente e la WAN o più livelli di dispositivi su una rete aziendale.

Data la complessità di questi tre diversi ambienti di rete generali, è spesso ottimale iniziare dalle estremità e cercare di mostrare dove le prestazioni sono buone e dove invece si degradano. Questo approccio può aiutare a identificare quale dei tre domini di routing è problematico e quindi concentrare la risoluzione dei problemi su quell'ambiente specifico.

## <a name="tools"></a>Strumenti
La maggior parte dei problemi di rete può essere analizzata e isolata usando strumenti di base come ping e traceroute. È raro che si debba arrivare a eseguire l'analisi dei pacchetti usando uno strumento come Wireshark. Per facilitare la risoluzione dei problemi, Azure Connectivity Toolkit (AzureCT) è stato sviluppato per inserire alcuni di questi strumenti in un unico pacchetto. Per il test delle prestazioni è preferibile usare iPerf e PSPing. iPerf è uno strumento di uso comune e viene eseguito sulla maggior parte dei sistemi operativi. iPerf è indicato per il test delle prestazioni di base ed è relativamente facile da usare. PSPing è uno strumento di test ping sviluppato da SysInternals. PSPing agevola l'esecuzione dei ping ICMP e TCP con un unico comando facile da usare. Entrambi questi strumenti sono leggeri e vengono "installati" semplicemente copiando i file in una directory sull'host.

Ho incluso tutti questi strumenti e questi metodi in un modulo PowerShell (AzureCT) che è possibile installare e usare.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT: Azure Connectivity Toolkit
Il modulo PowerShell di AzureCT include due componenti: [Availability Testing][Availability Doc] (Test della disponibilità) e [Performance Testing][Performance Doc] (Test delle prestazioni). Poiché questo documento tratta solo il Performance Testing (Test delle prestazioni), occorre concentrarsi sui due comandi del Link Performance (Prestazioni dei collegamenti) inclusi in questo modulo PowerShell.

L'uso di questo kit di strumenti per il Performance Testing (Test delle prestazioni) include tre passaggi fondamentali. 1) Installare il modulo PowerShell, 2) Installare le applicazioni di supporto iPerf e PSPing 3) Eseguire il test delle prestazioni.

1. Installare il modulo PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Questo comando scarica il modulo PowerShell e lo installa localmente.

2. Installare le applicazioni di supporto
    ```powershell
    Install-LinkPerformance
    ```
    Questo comando di AzureCT installa iPerf e PSPing in una nuova directory "C:\ACTTools". Apre inoltre le porte di Windows Firewall per consentire il traffico ICMP e tramite la porta 5201 (iPerf).

3. Eseguire il test delle prestazioni

    Sull'host remoto, è necessario prima installare ed eseguire iPerf in modalità server. Verificare inoltre che l'host remoto sia in ascolto sulla porta 3389 (RDP per Windows) o 22 (SSH per Linux) e che consenta il traffico sulla porta 5201 per iPerf. Se l'host remoto dispone di Windows, installare AzureCT ed eseguire il comando Install-LinkPerformance per configurare iPerf e le regole del firewall necessarie per avviare correttamente iPerf in modalità server. 
    
    Una volta che il computer remoto è pronto, aprire PowerShell sul computer locale e avviare il test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Questo comando esegue una serie di test simultanei di carico e latenza per stimare la latenza e la capacità di larghezza di banda del collegamento di rete.

4. Esaminare l'output dei test

    Il formato di output di PowerShell è simile a questo:

    [![4]][4]

    I risultati dettagliati di tutti i test iPerf e PSPing si trovano in singoli file di testo nella directory degli strumenti di AzureCT in "C:\ACTTools".

## <a name="troubleshooting"></a>risoluzione dei problemi
Se il test delle prestazioni non fornisce i risultati attesi, può essere consigliabile adottare un approccio graduale passo dopo passo. Dato il numero di componenti nel percorso, un approccio sistematico di solito risolve i problemi più velocemente di un approccio empirico in cui lo stesso test può essere eseguito inutilmente più volte.

>[!NOTE]
>Nello scenario qui illustrato è necessario risolvere un problema di prestazioni, non di connettività. La procedura sarebbe diversa se il traffico fosse completamente bloccato.
>
>

Innanzitutto, occorre riconsiderare le proprie ipotesi. Le proprie aspettative sono ragionevoli? Ad esempio, se si dispone di un circuito ExpressRoute da 1 Gbps e di una latenza di 100 ms, non è ragionevole prevedere il traffico massimo di 1 Gbps, viste le caratteristiche di prestazioni del TCP sui collegamenti ad alta latenza. Vedere la sezione [Riferimenti](#references) per altre informazioni sulle ipotesi relative alle prestazioni.

Successivamente è consigliabile iniziare dai margini dei domini di routing e provare a isolare il problema su un singolo dominio di routing primario: la rete aziendale, la WAN o la rete di Azure. Di solito si incolpa la "black box" nel percorso. Incolpare la black box è molto facile, ma questo può ritardare significativamente la risoluzione, specialmente se il problema si trova effettivamente in un'area in cui è possibile apportare modifiche. Assicurarsi di effettuare tutti i controlli necessari prima di contattare il provider di servizi o ISP.

Dopo aver identificato il dominio di routing principale che contiene il problema, è necessario creare un diagramma dell'area in questione. Che sia su una lavagna, un blocco note o Visio, un diagramma fornisce un concreto "piano di battaglia" per isolare il problema in modo metodico. È possibile pianificare i punti di test e aggiornare la mappa man mano che ci si allontana dalle aree o si approfondiscono le analisi durante l'avanzamento del test.

Dopo aver creato un diagramma è possibile iniziare a suddividere la rete in segmenti e circoscrivere il problema. Stabilire dove funziona e dove no. Continuare a spostare i punti di test per isolare il componente incriminato.

Non dimenticare inoltre di esaminare gli altri livelli del modello OSI. È facile concentrarsi sulla rete e sui livelli 1-3 (livelli fisici, dati e rete), ma i problemi possono anche essere presenti nel livello 7, quello dell'applicazione. Mantenere la mente aperta e verificare le ipotesi.

## <a name="advanced-expressroute-troubleshooting"></a>Risoluzione avanzata dei problemi di ExpressRoute
Se non si conoscono i perimetri del cloud, l'isolamento dei componenti di Azure può essere difficile. Quando si usa ExpressRoute, il perimetro è un componente di rete denominato Microsoft Enterprise Edge (MSEE). **Quando si usa ExpressRoute**, MSEE è il primo punto di contatto nella rete di Microsoft e l'ultimo hop fuori dalla rete Microsoft. Quando si crea un oggetto di connessione tra il gateway della rete virtuale e il circuito ExpressRoute, si sta effettivamente eseguendo una connessione a MSEE. Riconoscere MSEE come primo o ultimo hop (a seconda della direzione in cui si sta procedendo) è fondamentale per isolare i problemi di Azure Network, per dimostrare che il problema risiede in Azure o più a valle nella WAN o nella rete aziendale. 

[![2]][2]

>[!NOTE]
> Si noti che MSEE non si trova nel cloud di Azure. ExpressRoute si trova effettivamente ai margini della rete Microsoft, non proprio in Azure. Una volta che si è connessi a un MSEE con ExpressRoute, si è connessi alla rete Microsoft, dalla quale si può quindi accedere a qualsiasi servizio cloud, come Office 365 (con Peering Microsoft) o Azure (con Peering Microsoft e/o privato).
>
>

Se due reti virtuali (VNet A e B nel diagramma) sono connesse allo **stesso** circuito ExpressRoute, è possibile eseguire una serie di test per isolare il problema in Azure (oppure dimostrare che non si trova in Azure)
 
### <a name="test-plan"></a>Piano di test
1. Eseguire il test di Get-LinkPerformance tra VM1 e VM2. Questo test consente di comprendere se il problema è locale o meno. Se questo test produce risultati accettabili di latenza e larghezza di banda, è possibile contrassegnare la rete virtuale locale come corretta.
2. Supponendo che il traffico della rete virtuale locale sia corretto, eseguire il test Get-LinkPerformance tra VM1 e VM3. Questo test esegue la connessione attraverso la rete Microsoft fino a MSEE e di nuovo verso Azure. Se questo test produce risultati accettabili di latenza e larghezza di banda, è possibile contrassegnare la rete di Azure come corretta.
3. Se Azure viene escluso, è possibile eseguire una sequenza analoga di test sulla rete aziendale. Se anche questi test hanno esito positivo, è giunto il momento di collaborare con il proprio provider di servizi o ISP per eseguire la diagnosi della connessione della rete WAN. Esempio: Eseguire il test tra due filiali, o tra il desktop e un server del data center. A seconda di ciò che si sta testando, trovare gli endpoint (server, PC e così via) che possono verificare tale percorso.

>[!IMPORTANT]
> È fondamentale che per ogni test si prenda nota dell'ora del giorno in cui si esegue il test e che si registrino i risultati in una posizione comune (ad esempio OneNote o Excel). Ciascuna esecuzione dei test deve avere un output identico, in modo da poter confrontare i dati risultanti dalle esecuzioni dei test e da non avere "buchi" nei dati. La coerenza tra più test è la ragione principale per cui utilizzo AzureCT nella risoluzione dei problemi. Il *trucco* non sta negli scenari di caricamento esatti caricati, ma nel fatto di ottenere un *output di dati e test coerenti* da tutti i test. Registrare l'ora e avere dati coerenti ogni volta è particolarmente utile se in seguito si scopre che il problema è sporadico. Occorre essere accurati all'inizio della raccolta di dati, in modo da evitare di ripetere per ore i test degli stessi scenari (l'ho imparato a mie spese molti anni fa).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Il problema è stato isolato, ora cosa si deve fare?
Più si riesce a isolare il problema più semplice sarà correggerlo, tuttavia spesso si arriva a un punto in cui non è più possibile proseguire ulteriormente con la risoluzione dei problemi. Esempio: si nota che il collegamento attraverso il proprio provider di servizi usa hop in Europa, mentre l'intero percorso dovrebbe essere in Asia. Questo è il momento in cui occorre contattare l'assistenza. Il contatto di assistenza varia in base al dominio di routing su cui è stato isolato il problema, o meglio ancora, il componente specifico identificato come interessato.

Per problemi di rete aziendale, il reparto IT interno o un provider di servizi di supporto per la rete (ad esempio il produttore dell'hardware) può offrire assistenza nella configurazione del dispositivo o nella riparazione dell'hardware.

Per quel che riguarda la WAN, la condivisione dei risultati del test con il provider di servizi o l'ISP può aiutarli a tenere tutto sotto controllo e impedire che riprendano alcuni dei test già effettuati. Non occorre offendersi se decidessero di verificare di persona i risultati già ottenuti. "Fidati, ma controlla" è un buon motto quando si tratta di risolvere i problemi dai risultati forniti da altre persone.

Con Azure, una volta isolato il problema nel modo più preciso possibile, è necessario esaminare la [Documentazione di Microsoft Azure] [ Network Docs] e quindi, se ancora necessario, [aprire un ticket di supporto][Ticket Link].

## <a name="references"></a>Riferimenti
### <a name="latencybandwidth-expectations"></a>Aspettative in termini di latenza e larghezza di banda
>[!TIP]
> La latenza geografica (miglia o chilometri) tra gli endpoint che si stanno testando è di gran lunga il più importante componente della latenza. Sebbene esista la latenza delle apparecchiature (componenti fisici e virtuali, numero di hop, ecc.), la geografia si è dimostrata il più grande componente della latenza complessiva quando si tratta di connessioni WAN. È anche importante notare che la distanza è la distanza in fibra ottica, non in linea d'aria o su mappa stradale. Ottenere una distanza precisa è estremamente difficile. Di conseguenza, generalmente uso un calcolatore di distanza tra le città su Internet. Questo metodo è certamente approssimativo, ma è sufficiente per definire un'aspettativa generale.
>
>

Ho un'installazione di ExpressRoute a Seattle, Washington negli Stati Uniti. La tabella seguente mostra la latenza e la larghezza di banda che ho trovato eseguendo i test in diverse posizioni di Azure. Ho stimato la distanza geografica tra ciascuna estremità del test.

Configurazione di test:
 - Un server fisico che esegue Windows Server 2016 con una scheda di interfaccia di rete da 10 Gbps, collegata a un circuito ExpressRoute.
 - Un circuito ExpressRoute Premium da 10 Gbps nella posizione identificata con Peering privato abilitato.
 - Una rete virtuale di Azure con un gateway UltraPerformance nella regione specificata.
 - Una macchina virtuale DS5v2 che esegue Windows Server 2016 sulla rete virtuale. La macchina virtuale non è stata aggiunta a un dominio ed è stata generata dall'immagine di Azure predefinita (nessuna ottimizzazione o personalizzazione) con AzureCT installato.
 - Tutti i test sono stati eseguiti usando il comando Get-LinkPerformance di AzureCT, con un test di carico di 5 minuti per ciascuna delle sei esecuzioni dei test. Ad esempio: 

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Per ciascun test, il carico del flusso di dati variava dal server fisico locale (client iPerf in Seattle) alla macchina virtuale di Azure (server iPerf nella regione di Azure elencata).
 - I dati della colonna "Latenza" provengono dal test senza carico (un test di latenza TCP senza iPerf in esecuzione).
 - I dati della colonna "Massima larghezza di banda" provengono dal test di carico del flusso di 16 TCP con una dimensione della finestra di 1 Mb.

[![3]][3]

### <a name="latencybandwidth-results"></a>Risultati di latenza e larghezza di banda
>[!IMPORTANT]
> Usare questi valori solo come riferimento generale. Molti fattori influiscono sulla latenza e, sebbene questi valori siano generalmente coerenti nel tempo, le condizioni all'interno di Azure o della rete dei provider di servizi possono inviare il traffico attraverso percorsi diversi in qualsiasi momento, il che può influire sulla latenza e sulla larghezza di banda. Generalmente, queste modifiche non determinano differenze significative.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Località|Azure<br/>Region|Distanza<br/>stimata (km)|Latenza|1 Sessione<br/>Larghezza di banda|Massima<br/>Larghezza di banda|
| Seattle | Stati Uniti occidentali 2        |    191 km |   5 ms | 262,0 Mbit/sec |  3,74 Gbit/sec | 21
| Seattle | Stati Uniti occidentali          |  1.094 km |  18 ms |  82,3 Mbit/sec |  3,70 Gbit/sec | 20
| Seattle | Stati Uniti centrali       |  2.357 km |  40 ms |  38,8 Mbit/sec |  2,55 Gbit/sec | 17
| Seattle | Stati Uniti centro-meridionali |  2.877 km |  51 ms |  30,6 Mbit/sec |  2,49 Gbit/sec | 19
| Seattle | Stati Uniti centro-settentrionali |  2.792 km |  55 ms |  27,7 Mbit/sec |  2,19 Gbit/sec | 18
| Seattle | Stati Uniti orientali 2        |  3.769 km |  73 ms |  21,3 Mbit/sec |  1,79 Gbit/sec | 16
| Seattle | Stati Uniti orientali          |  3.699 km |  74 ms |  21,1 Mbit/sec |  1,78 Gbit/sec | 15
| Seattle | Giappone orientale       |  7.705 km | 106 ms |  14,6 Mbit/sec |  1,22 Gbit/sec | 28
| Seattle | Regno Unito meridionale         |  7.708 km | 146 ms |  10,6 Mbit/sec |   896 Mbit/sec | 24
| Seattle | Europa occidentale      |  7.834 km | 153 ms |  10,2 Mbit/sec |   761 Mbit/sec | 23
| Seattle | Australia orientale   | 12.484 km | 165 ms |   9,4 Mbit/sec |   794 Mbit/sec | 26
| Seattle | Asia sudorientale   | 12.989 km | 170 ms |   9,2 Mbit/sec |   756 Mbit/sec | 25
| Seattle | Brasile meridionale *   | 10.930 km | 189 ms |   8,2 Mbit/sec |   699 Mbit/sec | 22
| Seattle | India meridionale      | 12.918 km | 202 ms |   7,7 Mbit/sec |   634 Mbit/sec | 27

\* La latenza in Brasile è un buon esempio in cui la distanza in linea d'aria differisce notevolmente dalla distanza in fibra ottica. Mentre la latenza dovrebbe normalmente essere di circa 160 ms, è di 189 ms. Questa differenza rispetto alle mie aspettative potrebbe indicare un problema di rete da qualche parte, ma molto probabilmente la fibra ottica in Brasile non va in linea retta e percorre circa 1.000 km in più per arrivare da Seattle.

## <a name="next-steps"></a>Passaggi successivi
1. Scaricare Azure Connectivity Toolkit da GitHub all'indirizzo [http://aka.ms/AzCT][ACT]
2. Seguire le istruzioni per il [test delle prestazioni dei collegamenti][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Componenti di rete di Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Risoluzione dei problemi di ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Ambiente di test delle prestazioni"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Output di PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index#pivot=services&panel=network
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: http://aka.ms/AzCT











