---
title: Panoramica del servizio di analisi degli errori | Microsoft Docs
description: Questo articolo descrive il servizio di analisi degli errori di Service Fabric per provocare errori ed eseguire scenari di test sui servizi dell&quot;utente.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/01/2017
ms.author: anmola
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c05081685532aecbe29e9bf380f0278e775ad6b7
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="introduction-to-the-fault-analysis-service"></a>Introduzione al servizio di analisi degli errori
Il servizio di analisi degli errori è progettato per testare servizi basati su Microsoft Azure Service Fabric. Con il servizio di analisi degli errori è possibile causare errori significativi ed eseguire scenari di test completi delle applicazioni. Tali errori e scenari verificano e convalidano i numerosi stati e le transizioni sperimentate da un servizio per la relativa durata, il tutto in modo controllato, sicuro e coerente.

Le azioni sono i singoli errori destinati a un servizio a scopo di test. Uno sviluppatore di servizi può utilizzarle come blocchi predefiniti per scrivere scenari complicati. Ad esempio:

* Riavviare un nodo per simulare un qualsiasi numero di situazioni in cui un computer o una VM viene riavviata.
* Spostare una replica del servizio con stato per simulare il bilanciamento del carico, il failover o l'aggiornamento dell'applicazione.
* Richiamare la perdita del quorum in un servizio con stato per creare una situazione in cui le operazioni di scrittura non possono continuare perché il numero delle repliche di "backup" o "secondarie" non è sufficiente per accettare nuovi dati.
* Richiamare la perdita di dati in un servizio con stato per creare una situazione in cui tutto lo stato in memoria sia completamente cancellato.

Gli scenari sono operazioni complesse costituite da una o più azioni. Il servizio di analisi degli errori offre due scenari completi incorporati:

* Scenario di caos
* Scenario di failover

## <a name="testing-as-a-service"></a>Test come servizio
Il servizio di analisi degli errori è un servizio di sistema di Service Fabric che viene avviato automaticamente con un cluster di Service Fabric. Questo servizio funge da host per l'inserimento di errori, l'esecuzione degli scenari di test e l'analisi dell'integrità. 

![Servizio di analisi degli errori][0]

Quando viene avviato uno scenario di test o un'azione di errore, viene inviato al servizio di analisi degli errori un comando per eseguire tale scenario o azione. Il servizio di analisi degli errori è con stato, quindi è in grado di eseguire errori e scenari e convalidare i risultati in modo affidabile. Ad esempio, uno scenario di test a esecuzione prolungata può essere eseguito in modo affidabile dal servizio di analisi degli errori. E poiché i test vengono eseguiti all'interno del cluster, il servizio può esaminare lo stato del cluster e i servizi per offrire informazioni più dettagliate sugli errori.

## <a name="testing-distributed-systems"></a>Test dei sistemi distribuiti
Infrastruttura di servizi rende molto più semplice il processo di scrittura e gestione delle applicazioni scalabili distribuite. Allo stesso modo, il servizio di analisi degli errori semplifica il test di un'applicazione distribuita. Esistono tre problemi principali che devono essere risolti durante il test:

1. La simulazione e la generazione di errori che potrebbero verificarsi negli scenari reali: uno degli aspetti importanti di Service Fabric è che consente alle applicazioni distribuite di correggere vari errori. Tuttavia, per verificare che l'applicazione sia in grado di correggere questi errori, è necessario un meccanismo per simulare e generare questi errori reali in un ambiente di test controllato.
2. La capacità di generare errori correlati: gli errori di base nel sistema, ad esempio gli errori di rete e gli errori dei computer, sono facili da riprodurre singolarmente. Generare un numero significativo di scenari che possono verificarsi nel mondo reale in seguito alle interazioni di questi singoli errori non è semplice.
3. L'esperienza unificata tra vari livelli di sviluppo e distribuzione: esistono molti sistemi di fault injection che consentono di generare diversi tipi di errori. Tuttavia, quando si passa da uno scenario di sviluppo in un ambiente di piccole dimensioni all'esecuzione degli stessi test in ambienti di test di grandi dimensioni per usarli nella produzione, l'esperienza è insoddisfacente.

Sebbene esistano numerosi meccanismi per risolvere questi problemi, manca ancora un sistema in grado di eseguire le stesse operazioni con le garanzie richieste, direttamente da un ambiente di sviluppo di piccole dimensioni ai test nei cluster di produzione. Il servizio di analisi degli errori consente agli sviluppatori di applicazioni di concentrarsi sul test della logica di business. Il servizio di analisi degli errori offre tutte le funzionalità necessarie per testare l'interazione del servizio con il sistema distribuito sottostante.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulazione e generazione di scenari di errore reali
Per verificare l'affidabilità di un sistema distribuito in caso di errori, è necessario un meccanismo per generare errori. Sebbene dal punto di vista teorico generare un errore come un nodo inattivo sembri semplice, comporta gli stessi problemi di coerenza che si tenta di risolvere con Service Fabric. Se ad esempio si vuole arrestare un nodo, il flusso di lavoro richiesto è il seguente:

1. Eseguire dal client una richiesta di arresto del nodo.
2. Inviare la richiesta al nodo giusto.
   
    a. Se il nodo non viene trovato, la richiesta ha esito negativo.
   
    b. Se il nodo viene trovato, la richiesta viene restituita solo se il nodo è stato arrestato.

Per verificare l'errore dalla prospettiva del test è necessario sapere che, quando viene provocato, l'errore si verifica effettivamente. La garanzia offerta da Service Fabric consiste nel fatto che il nodo sarà disattivato oppure lo era già quando il comando lo ha raggiunto. In entrambi i casi il test deve essere in grado di verificare lo stato e procedere con l'esatta convalida (riuscito o non riuscito). Un sistema implementato all'esterno di Service Fabric per eseguire lo stesso set di errori potrebbe implicare numerosi problemi di rete, hardware e software che impedirebbero al sistema stesso di offrire le garanzie indicate in precedenza. In presenza dei problemi elencati, Service Fabric esegue la riconfigurazione dello stato del cluster per risolvere i problemi, pertanto il servizio di analisi degli errori continua a essere in grado di offrire le garanzie corrette.

### <a name="generating-required-events-and-scenarios"></a>Generazione degli eventi e degli scenari richiesti
Se è difficile iniziare a simulare un errore reale in modo coerente, essere in grado di generare errori correlati è ancora più complesso. Ad esempio, in un servizio persistente con stato si verifica una perdita di dati quando si verificano i seguenti eventi:

1. Nella replica viene rilevato solo un quorum di scrittura delle repliche. Tutte le repliche secondarie restano dietro quelle primarie.
2. Il quorum di scrittura si interrompe in quanto le repliche si arrestano a causa dell'arresto di un pacchetto di codice o di un nodo.
3. Il quorum di scrittura non può tornare attivo perché i dati per le repliche sono stati persi a causa di un danno del disco o della ricreazione dell'immagine del computer.

Questi errori correlati si verificano effettivamente nel mondo reale, anche se non con la stessa frequenza dei singoli errori. La possibilità di testare questi scenari prima che si verifichino nell'ambiente di produzione è fondamentale. Ancora più importante è la possibilità di simulare questi scenari con carichi di lavoro di produzione in circostanze controllate, ad esempio durante la giornata con tutti gli ingegneri al lavoro, il che è di gran lunga preferibile al rischio che lo scenario si verifichi per la prima volta nell'ambiente di produzione alle 2:00.

### <a name="unified-experience-across-different-environments"></a>Esperienza unificata tra ambienti diversi
Di solito, la pratica consisteva nel creare tre set di esperienze diversi, uno per l'ambiente di sviluppo, uno per i test e uno per la produzione. Il modello era quello indicato di seguito:

1. Nell'ambiente di sviluppo generare transizioni di stato che consentissero unit test dei singoli metodi.
2. Nell'ambiente di test riprodurre errori per consentire ai test end-to-end di fare pratica con scenari di errore diversi.
3. Mantenere l'ambiente di produzione originario per impedire qualsiasi errore non naturale e garantire una risposta umana estremamente rapida all'errore.

In Service Fabric il servizio di analisi degli errori consente di capovolgere tutto questo e di usare la stessa metodologia dall'ambiente di sviluppo alla produzione. A questo scopo è possibile procedere in due modi:

1. Per indurre errori controllati, usare le API del servizio di analisi degli errori da un ambiente di piccole dimensioni fino ai cluster di produzione.
2. Per fare in modo che il cluster induca errori automaticamente, usare il servizio di analisi degli errori per generare errori automatici. Il controllo della frequenza degli errori tramite la configurazione consente al servizio stesso di essere sottoposto a test in modo diverso in ambienti differenti.

Con Service Fabric, sebbene la scala degli errori sarà diversa in ambienti differenti, il meccanismo effettivo sarà identico. Ciò consente l'uso di un codice molto più rapido per la pipeline di distribuzione e la possibilità di testare i servizi con carichi di lavoro reali.

## <a name="using-the-fault-analysis-service"></a>Uso del servizio di analisi degli errori
**C#**

Le funzionalità del servizio di analisi degli errori si trovano nello spazio dei nomi System.Fabric nel pacchetto NuGet Microsoft.ServiceFabric. Per usare le funzionalità del servizio di analisi degli errori, includere il pacchetto NuGet come riferimento nel progetto.

**PowerShell**

Per usare PowerShell, è necessario installare Service Fabric SDK. Dopo avere installato l'SDK, viene caricato automaticamente il modulo ServiceFabric di PowerShell.

## <a name="next-steps"></a>Passaggi successivi
Per creare servizi effettivamente a livello di cloud, è fondamentale assicurarsi, prima e dopo la distribuzione, che i servizi siano in grado di resistere agli errori reali. Con i servizi attuali, la possibilità di innovare rapidamente e spostare velocemente il codice nella produzione è molto importante. Il servizio di analisi degli errori consente agli sviluppatori dei servizi di ottenere esattamente questo risultato.

A questo punto è possibile iniziare a testare le applicazioni e i servizi con gli [scenari di test](service-fabric-testability-scenarios.md) predefiniti oppure creare i propri scenari di test tramite le [azioni di errore](service-fabric-testability-actions.md) offerte dal servizio di analisi degli errori.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png

