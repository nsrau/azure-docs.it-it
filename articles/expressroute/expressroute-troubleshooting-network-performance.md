---
title: Risoluzione dei problemi di prestazioni di rete virtuale di Azure | Documenti Microsoft
description: Questa pagina fornisce un metodo standardizzato di test delle prestazioni di collegamento di rete di Azure.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="troubleshooting-network-performance"></a>Risoluzione dei problemi di prestazioni di rete
## <a name="overview"></a>Panoramica
Azure offre metodi stabile e veloce per connettersi alla rete locale a Azure. Metodi come VPN Site-to-Site ed ExpressRoute correttamente utilizzati dai clienti piccoli e grandi dimensioni per eseguire le attività di business in Azure. Ma cosa accade quando le prestazioni non soddisfano la previsione o esperienze precedenti? Questo documento consente di standardizzare la modalità di che test e della linea di base, l'ambiente specifico.

Questo documento illustra come è possibile in modo coerente e facilmente verificare latenza di rete e larghezza di banda tra due host. Questo documento fornisce anche alcuni consigli sui modi per esaminare la rete di Azure e sono utili per individuare i punti di problema. Gli strumenti descritti e script di PowerShell richiedono due host nella rete (in delle estremità del collegamento testato). Deve essere un host Windows Server o Desktop, l'altra può essere Windows o Linux. 

>[!NOTE]
>L'approccio alla risoluzione dei problemi, gli strumenti e i metodi usati sono le preferenze personali. Questo documento descrive gli strumenti e l'approccio che spesso richiedono. L'approccio sarà probabilmente diversa, non c'è niente di sbagliato con diversi approcci per la risoluzione dei problemi. Tuttavia, se non si dispone di un approccio stabilito, questo documento può iniziare al percorso per la creazione di propri metodi, strumenti e le preferenze per la risoluzione dei problemi di rete.
>
>

## <a name="network-components"></a>Componenti di rete
Prima di approfondire la risoluzione dei problemi, esaminiamo alcuni termini e i componenti comuni. Questa discussione garantisce che si sta pensando di ogni componente nella catena di end-to-end che consente la connessione in Azure.
[![1]][1]

Livello più alto, descrivo tre domini di routing di rete;

- la rete di Azure (cloud blu a destra)
- Internet o rete WAN (verde cloud al centro)
- la rete aziendale (cloud rosa chiaro a sinistra)

Esaminando il diagramma da destra a sinistra, Esaminiamo brevemente ciascun componente:
 - **Macchina virtuale** : il server potrebbe essere più schede di rete, verificare che le route statiche, route predefinite e l'invio di impostazioni del sistema operativo e la ricezione di quello che si sospetta che il traffico è. Inoltre, ogni SKU VM è una limitazione della larghezza di banda. Se si usa uno SKU VM più piccoli, il traffico è limitato dalla larghezza di banda disponibile per la scheda NIC. In genere usare un DS5v2 per test (e quindi Elimina al termine di test per risparmiare denaro) per garantire una sufficiente larghezza di banda della macchina virtuale.
 - **NIC** -assicurarsi di conoscere l'indirizzo IP privato è assegnato per la scheda di rete in questione.
 - **NIC NSG** : si potrebbe essere NSGs specifico applicato a livello di interfaccia di rete, verificare che sia appropriato per il traffico che si sta tentando di passare il set di regole gruppo. Ad esempio, verificare le porte 5201 per iPerf, 3389 per RDP o 22 per SSH sono aperte per consentire il traffico di test passare.
 - **Subnet rete virtuale** -la scheda di rete viene assegnata a una subnet specifica, assicurarsi di conoscere quale uno e le regole associato alla subnet.
 - **GRUPPO subnet** - esattamente come l'interfaccia di rete, NSGs possono essere applicati anche alla subnet. Verificare che sia appropriato per il traffico che si sta tentando di passare il set di regole gruppo. (per il traffico in ingresso per la scheda di rete alla subnet che gruppo si applica prima, quindi di NSG NIC, al contrario per il traffico in uscita dalla macchina virtuale di NSG NIC applica innanzitutto quindi il gruppo Subnet entra in gioco).
 - **Subnet UDR** -le route definite dall'utente può indirizzare il traffico a un hop intermedio (ad esempio un firewall o un servizio di bilanciamento del carico). Assicurarsi di conoscere se sussiste una UDR in luogo per il traffico e, se così dove vengono memorizzate e operazioni per il traffico dell'hop successivo. ad esempio, un firewall potrebbe passare una parte del traffico e negare il traffico tra due host stesso.
 - **Subnet del gateway / gruppo / UDR** -esattamente come la subnet VM, la subnet del gateway può avere NSGs e UDRs. Assicurarsi di conoscere se sono e quali effetti dispongano del traffico.
 - **Il Gateway di rete virtuale (ExpressRoute)** -dopo aver abilitata la VPN o peering (ExpressRoute), non vi sono molte impostazioni che possono influire sulla modalità o se le route del traffico. Se si dispone di più circuiti ExpressRoute o tunnel VPN connesso allo stesso Gateway di rete virtuale, è da tenere in considerazione le impostazioni del peso di connessione come questa preferenza di connessione influisce sull'impostazione e interessa il percorso del traffico.
 - **Filtro di routing** (non illustrato) - un filtro di route solo si applicano a Microsoft di Peering su ExpressRoute, ma è fondamentale per controllare se ma non vengono visualizzati le route che si prevede che nel Peering Microsoft. 

A questo punto, si è nella parte del collegamento WAN. Questo dominio di routing può essere il provider di servizi, la rete azienda WAN o Internet. Molti hop, tecnologie e le società coinvolte con questi collegamenti possono renderlo piuttosto difficile risolvere i problemi. Spesso, si procede alla regola sia Azure e le reti aziendali innanzitutto prima di passare a questa raccolta di aziende e hop.

Nel diagramma precedente, all'estrema sinistra è una rete azienda. A seconda delle dimensioni dell'azienda, il dominio di routing può essere di alcuni dispositivi di rete tra è e la rete WAN o più livelli di dispositivi in una rete campus o aziendale.

Considerata la complessità di questi tre diversi generale ambienti di rete, spesso è ottimale per avviare i bordi e tenta di visualizzare in cui sono buone prestazioni e in cui comporta una riduzione. Questo approccio consente di identificare il dominio di routing problema dei tre e concentrare la risoluzione dei problemi in tale ambiente specifico.

## <a name="tools"></a>Strumenti
La maggior parte dei problemi di rete possono essere analizzati e isolata utilizzando strumenti quali ping e traceroute di base. È raro che è necessario passare come deep come un'analisi del pacchetto come Wireshark. Per facilitare la risoluzione dei problemi, il Toolkit di connettività di Azure (AzureCT) è stato sviluppato per inserire alcuni di questi strumenti in un pacchetto semplice. Test delle prestazioni, desidera utilizzare iPerf e PSPing. iPerf è uno strumento di uso comune e viene eseguita la maggior parte dei sistemi operativi. iPerf è adatta per i test delle prestazioni di base ed è piuttosto facile da usare. PSPing è uno strumento di ping sviluppato da SysInternals. PSPing è un modo semplice per eseguire il ping ICMP e TCP in un unico comando anche facile da usare. Entrambi questi strumenti vengono lightweight e "installati" semplicemente copiando i file in una directory nell'host.

Ho inserito tutti questi strumenti e i metodi in un modulo di PowerShell (AzureCT) che è possibile installare e utilizzare.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - il Toolkit di connettività di Azure
Il modulo AzureCT PowerShell include due componenti [il test della disponibilità] [ Availability Doc] e [test delle prestazioni][Performance Doc]. Questo documento riguarda solo con test delle prestazioni, pertanto consente lo stato attivo sui due comandi collegamento prestazioni in questo modulo di PowerShell.

Esistono tre passaggi di base da usare il toolkit per il test delle prestazioni. 1) installare il modulo di PowerShell, 2) installare il supporto iPerf applicazioni e PSPing 3) eseguire il test delle prestazioni.

1. Installazione del modulo di PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Questo comando Scarica il modulo di PowerShell e lo installa localmente.

2. Installare le applicazioni di supporto
    ```powershell
    Install-LinkPerformance
    ```
    Questo comando AzureCT installa iPerf e PSPing in una nuova directory "C:\ACTTools", viene aperto anche le porte di Windows Firewall per consentire l'ICMP e porta 5201 traffico (iPerf).

3. Eseguire il test delle prestazioni

    Innanzitutto, nell'host remoto è necessario installare ed eseguire iPerf in modalità server. Verificare inoltre l'host remoto è in ascolto su entrambi 3389 (RDP for Windows) o 22 (SSH per Linux) e che consente il traffico sulla porta 5201 per iPerf. Se l'host remoto windows, installare il AzureCT ed eseguire il comando di installazione LinkPerformance iPerf e le regole del firewall necessarie per iniziare a iPerf correttamente in modalità server. 
    
    Una volta pronto il computer remoto, aprire PowerShell nel computer locale e avviare il test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Questo comando esegue una serie di simultanea test di carico e latenza per calcolare la capacità della larghezza di banda e latenza del collegamento di rete.

4. Esaminare l'output dei test

    Il formato di output di PowerShell simile a:

    [![4]][4]

    I risultati dettagliati di tutte le iPerf e PSPing test sono in singoli file di testo nella directory strumenti AzureCT in "C:\ACTTools".

## <a name="troubleshooting"></a>risoluzione dei problemi
Se il test delle prestazioni non produce i risultati previsti, immaginando perché dovrebbe essere una procedura guidata progressiva. Dato il numero di componenti del percorso, un approccio sistematico in genere fornisce un percorso più rapido per la risoluzione del passaggio intorno e potenzialmente inutilmente eseguire più volte di test stesso.

>[!NOTE]
>Lo scenario illustrato è un problema di prestazioni, non un problema di connettività. La procedura sarebbe diversa se il traffico non è stata passando affatto.
>
>

Richiesta di verifica in primo luogo, i presupposti. È ragionevole le aspettative? Ad esempio, se si dispone di un circuito ExpressRoute da 1 Gbps e 100 ms di latenza è ragionevole aspettarsi di completo 1 Gbps di traffico considerato le caratteristiche di prestazioni del protocollo TCP su collegamenti ad alta latenza. Vedere il [fa riferimento a sezione](#references) per altre su presupposti relativi alle prestazioni.

Successivamente, consigliabile iniziare lungo i bordi tra domini di routing e provare a isolare il problema in un dominio di routing principale singolo. la rete aziendale, la rete WAN o la rete di Azure. Gli utenti spesso è possibile eseguire facilmente individuale "casella di colore nero" nel percorso, mentre rimprovero casella nera, potrebbero essere significativamente ritardate risoluzione soprattutto se il problema è effettivamente in un'area che sia la possibilità di apportare modifiche. Assicurarsi di eseguire il diligente prima di consegnare al provider di servizi o provider di servizi Internet.

Dopo aver identificato il dominio di routing principale che contiene il problema, è necessario creare un diagramma dell'area in questione. In una lavagna, blocco note o Visio come diagramma fornisce una concreta "mappa battaglia" per consentire un approccio metodico su Isola ulteriormente il problema. È possibile pianificare i punti di test e aggiornare la mappa come si cancella aree o un'analisi più approfondita durante l'esecuzione del test.

Dopo aver creato un diagramma, è possibile iniziare a dividere la rete in segmenti e circoscrivere il problema. Scoprire in cui viene eseguito e in caso contrario. Mantenere spostando i punti di test per isolare down il componente di origine.

Inoltre, non dimenticare di esaminare gli altri livelli del modello OSI. È facile per concentrarsi su rete e i livelli 1-3 (livelli di rete fisica e dati), ma i problemi possono anche essere backup Layer 7 nel livello dell'applicazione. Mantenere molta attenzione e verificare i presupposti.

## <a name="advanced-expressroute-troubleshooting"></a>Risoluzione dei problemi ExpressRoute avanzati
Se non si è certi in cui è effettivamente il bordo del cloud, l'isolamento di componenti di Azure può risultare difficile. Quando si usa ExpressRoute, il bordo è un componente di rete denominato di Microsoft Enterprise Edge (MSEE). **Quando si usa ExpressRoute**, il MSEE è il primo punto di contatto in rete di Microsoft e l'ultimo hop in uscita dalla rete Microsoft. Quando si crea un oggetto connessione tra il gateway di rete virtuale e il circuito ExpressRoute, si apportano effettivamente una connessione per il MSEE. Riconoscendo il MSEE come hop il primo o ultimo (a seconda di quale direzione si archivieranno) è fondamentale per l'isolamento dei problemi di rete di Azure a una delle due dimostrare che il problema è in Azure o ulteriormente a valle in una rete WAN o alla rete aziendale. 

[![2]][2]

>[!NOTE]
> Si noti che il MSEE non è nel cloud di Azure. ExpressRoute è effettivamente alla periferia della rete Microsoft non è effettivamente in Azure. Una volta che si è connessi con ExpressRoute per un MSEE, si è connessi alla rete Microsoft, da qui, sarà quindi possibile passare a uno dei servizi cloud, ad esempio Office 365 (con Peering Microsoft) o Azure (con Private e/o Peering Microsoft).
>
>

Se sono connessi due reti virtuali (VNets A e B nel diagramma) il **stesso** circuito ExpressRoute, è possibile eseguire una serie di test per isolare il problema in Azure (o provare a non è incluso in Azure)
 
### <a name="test-plan"></a>Piano di test
1. Eseguire il test di Get-LinkPerformance tra VM1 e VM2. Questo test consente di comprendere a se il problema è locale o meno. Se questo test produce i risultati della larghezza di banda e latenza accettabile, è possibile contrassegnare la rete locale della rete virtuale come valido.
2. Supponendo che la rete locale virtuale traffico sia valido, eseguire il test di Get-LinkPerformance tra VM1 e VM3. Questo test esegue la connessione attraverso la rete di Microsoft down il MSEE e riportarla in Azure. Se questo test produce i risultati della larghezza di banda e latenza accettabile, è possibile contrassegnare la rete di Azure come valido.
3. Se è stato stabilito Azure, è possibile eseguire una sequenza di test simile nella rete aziendale. Se che anche le prove e, è necessario rivolgersi al provider o un ISP per diagnosticare la connessione di rete WAN. Esempio: Eseguire il test tra due succursali, o tra il tecnico e un server del data center. A seconda di ciò che si sta testando, trovare gli endpoint (PC, server e così via) che puoi verificare che il percorso.

>[!IMPORTANT]
> È fondamentale che per ogni test si contrassegnano l'ora del giorno che si esegue il test e registrare i risultati in una posizione comune (in genere OneNote o Excel). Ciascuna esecuzione dei test deve essere identico output in modo è possibile confrontare i dati risultanti in esecuzioni dei test e non "fori" nei dati. La coerenza tra più test è principalmente che il AzureCT usare per la risoluzione dei problemi. Il trucco non è l'esatto negli scenari di carico, ma eseguire il *magic* è costituita dal fatto che viene visualizzato un *output coerente di dati e test* da tutti i test. L'ora di registrazione e con dati coerenti a ogni singolo è particolarmente utile se si trova in un secondo momento che il problema si verifica raramente. Essere accurati con inizio la raccolta di dati e si eviterà di ore di test ripetuti gli stessi scenari (devo in questo modo rigido molti anni).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Il problema è limitato, che cosa devo fare?
È più possibile isolare il problema più semplice è correggere, tuttavia, spesso si raggiunge il punto in cui non è possibile tornare più profondo o ulteriormente con la risoluzione dei problemi. Esempio: possibile vedere il collegamento tra il provider di servizi tenendo hop tramite Europe, ma è il percorso previsto in Asia. Questo punto è quando è necessario raggiungere per assistenza. Chi è chiedere è dipende dal dominio di routing che è isolato il problema alla, o ancora migliore se si è in grado di limitare la ricerca fino a un componente specifico.

Per problemi di rete aziendale, il reparto IT interno o un provider di servizi di supporto di rete (che può essere il produttore dell'hardware) sia in grado di facilitare la configurazione del dispositivo o di riparazione hardware.

Per la rete WAN, condividere i risultati di test con il Provider del servizio o un ISP può contribuire ottenerle avviato ed evitare illustrando alcune delle stesso interamente che hai testato già. Tuttavia, non essere offended se si desidera verificare i risultati autonomamente. "Attendibili ma verificare" è un buon motto quando la risoluzione dei problemi in base ai risultati segnalati di altri utenti.

Con Azure, una volta che è possibile isolare il problema nel modo più dettagliato di come sia possibile, è necessario esaminare il [documentazione della rete Azure] [ Network Docs] e quindi, se è ancora necessario [aprire un ticket di supporto][Ticket Link].

## <a name="references"></a>Riferimenti
### <a name="latencybandwidth-expectations"></a>Aspettative/latenza della larghezza di banda
>[!TIP]
> Latenza geografica (chilometri o miglia) tra i punti finali che si sta testando è di gran lunga il componente più grande di latenza. Mentre la latenza di apparecchiature (componenti fisici e virtuali, il numero di hop, e così via) è coinvolti, geography ha dimostrato di essere il componente più grande di Latenza complessiva, quando si gestiscono le connessioni WAN. È inoltre importante notare che la distanza è la distanza del fiber eseguire non la linea di tendenza o distanza Mappa stradale. La distanza è estremamente difficile ottenere con accuratezza. Di conseguenza, in genere utilizzare una calcolatrice di distanza città su internet e sa che questo metodo è una misura dimostra corrette, ma è sufficiente impostare una previsione generale.
>
>

Ho un programma di installazione di ExpressRoute Seattle, Washington negli Stati Uniti. La tabella seguente illustra la latenza e della larghezza di banda visto test in diverse posizioni di Azure. Ho stimato la distanza geografica tra ogni estremità del test.

Impostazione del test:
 - Un server fisico che esegue Windows Server 2016 con 10 Gbps NIC, connessi a un circuito ExpressRoute.
 - Un circuito ExpressRoute Premium 10 Gbps nella posizione identificata con BGP abilitato.
 - Una rete virtuale di Azure con un gateway UltraPerformance nella regione specificata.
 - Una DS5v2 macchina virtuale in esecuzione Windows Server 2016 in rete virtuale. La macchina virtuale è stata non di dominio aggiunto, generato da quello predefinito immagine di Azure (ottimizzazione o personalizzazione) con AzureCT installato.
 - Tutti i test è stato utilizzando il comando Get-LinkPerformance AzureCT con un test di carico di 5 minuti per tutti i sei esecuzioni di test. Ad esempio: 

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Il flusso di dati per ogni test era il carico che passano dal locale server fisico (client iPerf Seattle) fino a macchina virtuale di Azure (iPerf server nell'area di Azure elencato).
 - I dati della colonna "Latenza" sono del test di carico No (un test di latenza TCP senza iPerf in esecuzione).
 - I dati della colonna "Max Bandwidth" sono dal test di carico flusso TCP 16 con una dimensione della finestra di 1 Mb.

[![3]][3]

### <a name="latencybandwidth-results"></a>Risultati di latenza/larghezza di banda
>[!IMPORTANT]
> Questi numeri sono per uso generale. Molti fattori influiscono sulla latenza e anche se questi valori sono coerenti in genere nel tempo, le condizioni in Azure o di rete di provider di servizi possono inviare il traffico attraverso percorsi diversi in qualsiasi momento, pertanto la latenza e della larghezza di banda possono dipendere. In genere, gli effetti di queste modifiche non comportano differenze significative.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Località|Azure<br/>Region|Stimato<br/>Distanza (km)|Latenza|1 sessione<br/>Larghezza di banda|Massima<br/>Larghezza di banda|
| Seattle | Stati Uniti occidentali 2        |    km 191 |   5 ms | 262.0 Mbit/sec |  3.74 Gbits/sec | 21
| Seattle | Stati Uniti occidentali          |  km 1,094 |  18 ms |  82.3 Mbit/sec |  3.70 Gbits/sec | 20
| Seattle | Stati Uniti centrali       |  km 2,357 |  40 ms |  38,8 Mbit/sec |  2.55 Gbits/sec | 17
| Seattle | Stati Uniti centro-meridionali |  km 2,877 |  51 ms |  30,6 Mbit/sec |  2,49 Gbits/sec | 19
| Seattle | Stati Uniti centro-settentrionali |  km 2,792 |  55 ms |  27.7 Mbit/sec |  2.19 Gbits/sec | 18
| Seattle | Stati Uniti orientali 2        |  km 3,769 |  ms 73 |  21.3 Mbit/sec |  1.79 Gbits/sec | 16
| Seattle | Stati Uniti orientali          |  km 3,699 |  74 ms |  21.1 Mbit/sec |  1,78 Gbits/sec | 15
| Seattle | Giappone orientale       |  km 7,705 | 106 ms |  14.6 Mbit/sec |  1.22 Gbits/sec | 28
| Seattle | Regno Unito meridionale         |  km 7,708 | 146 ms |  10.6 Mbit/sec |   896 Mbit/sec | 24
| Seattle | Europa occidentale      |  km 7,834 | 153 ms |  10.2 Mbit/sec |   761 Mbit/sec | 23
| Seattle | Australia orientale   | km 12,484 | 165 ms |   9.4 Mbit/sec di |   794 Mbit/sec | 26
| Seattle | Asia sudorientale   | km 12,989 | 170 ms |   9.2 Mbit/sec |   756 Mbit/sec | 25
| Seattle | Brasile meridionale *   | km 10,930 | 189 ms |   8.2 Mbit/sec |   699 Mbit/sec | 22
| Seattle | India meridionale      | km 12,918 | 202 ms |   7.7 Mbit/sec |   634 Mbit/sec | 27

\*La latenza in Brasile è un buon esempio in cui la distanza di linea retta è notevolmente diverso dalla fiber eseguire distanza. Aspetta che la latenza in prossimità di 160 ms, ma è effettivamente 189 ms. Questa differenza contro la previsione può indicare un problema di rete in un punto, ma è probabile che il fiber esecuzione non va in Brasile in una linea retta e dispone di un km 1.000 aggiuntiva o spostamento per ottenere in Brasile da Seattle.

## <a name="next-steps"></a>Passaggi successivi
1. Scaricare il Toolkit di Azure connettività da GitHub all'indirizzo [http://aka.ms/AzCT][ACT]
2. Seguire le istruzioni per [collegare il test delle prestazioni][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "componenti di rete di azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "la risoluzione dei problemi di ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "ambiente di Test delle prestazioni"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Output di PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index#pivot=services&panel=network
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: http://aka.ms/AzCT











