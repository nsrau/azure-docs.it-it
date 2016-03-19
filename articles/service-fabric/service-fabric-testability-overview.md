<properties
   pageTitle="Panoramica di Testabilità | Microsoft Azure"
   description="Questo articolo descrive il sottosistema Testabilità in Service Fabric per provocare errori ed eseguire gli scenari di test con i servizi dell'utente."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="rsinha"/>

# Panoramica di Testabilità

Il sottosistema Testabilità è progettato per il test di servizi basati su Microsoft Azure Service Fabric. Con Testabilità è possibile causare errori significativi ed eseguire scenari. Tali errori e scenari verificano e convalidano i numerosi stati e le transizioni sperimentate da un servizio per la relativa durata, il tutto in modo controllato, sicuro e coerente.

Testabilità offre azioni e scenari che consentono di abilitare tali funzionalità. Le azioni sono i singoli errori destinati a un servizio a scopo di test. Uno sviluppatore di servizi può utilizzarle come blocchi predefiniti per scrivere scenari complicati. Ad esempio:

  * Riavviare un nodo per simulare un qualsiasi numero di situazioni in cui un computer o una VM viene riavviata.

  * Spostare una replica del servizio con stato per simulare il bilanciamento del carico, il failover o l’aggiornamento dell'applicazione.

  * Richiamare la perdita del quorum in un servizio con stato per creare una situazione in cui le operazioni di scrittura non possono continuare perché il numero delle repliche di "backup" o "secondarie" non è sufficiente per accettare nuovi dati.

  * Richiamare la perdita di dati in un servizio con stato per creare una situazione in cui tutto lo stato in memoria sia completamente cancellato.

Gli scenari sono operazioni complesse costituite da una o più azioni. Queste azioni sono comandi di PowerShell e chiamate API C#, pertanto possono assumere qualsiasi forma: servizi a esecuzione prolungata, comandi di PowerShell, applicazioni della riga di comando e così via. In Testabilità vengono forniti due scenari predefiniti:

  * Scenario di caos
  * Scenario di failover

Testabilità espone PowerShell e le API C#. In questo modo lo sviluppatore del servizio ha una maggiore flessibilità con lo script di PowerShell e un maggiore controllo delle API C#, a seconda delle esigenze.

## Importanza di Testabilità

Infrastruttura di servizi rende molto più semplice il processo di scrittura e gestione delle applicazioni scalabili distribuite. Allo stesso modo, il sottosistema Testabilità in Service Fabric semplifica il test di un'applicazione distribuita. Esistono tre problemi principali che devono essere risolti durante il test:

1. La simulazione e la generazione di errori che potrebbero verificarsi negli scenari reali: uno degli aspetti importanti di Service Fabric è che consente alle applicazioni distribuite di correggere vari errori. Tuttavia, per verificare che l'applicazione sia in grado di correggere questi errori, è necessario un meccanismo per simulare e generare questi errori reali in un ambiente di test controllato.

2. La capacità di generare errori correlati: gli errori di base nel sistema, ad esempio gli errori di rete e gli errori dei computer, sono facili da riprodurre singolarmente. generare il numero significativo di scenari che possono verificarsi nel mondo reale a seguito delle interazioni di questi singoli errori non è semplice.

3. L'esperienza unificata tra vari livelli di sviluppo e distribuzione: esistono molti sistemi di fault injection che consentono di generare diversi tipi di errori. Tuttavia, quando si passa da uno scenario di sviluppo in un ambiente di piccole dimensioni all'esecuzione degli stessi test in ambienti di test di grandi dimensioni per usarli nella produzione, l'esperienza è insoddisfacente.

Sebbene esistano numerosi meccanismi per risolvere questi problemi, manca ancora un sistema in grado di eseguire le stesse operazioni con le garanzie richieste, direttamente da un ambiente di sviluppo di piccole dimensioni ai test nei cluster di produzione. Il sottosistema Testabilità consente agli sviluppatori di applicazioni di concentrarsi sul test della logica di business. Testabilità offre tutte le funzionalità necessarie per testare l'interazione del servizio con il sistema distribuito sottostante.

### Simulazione e generazione di scenari di errore reali

Per verificare l'affidabilità di un sistema distribuito in caso di errori, è necessario un meccanismo per generare errori. Sebbene dal punto di vista teorico generare un errore come un nodo inattivo sembri semplice, comporta gli stessi problemi di coerenza che si tenta di risolvere con Service Fabric. Se ad esempio si vuole arrestare un nodo, il flusso di lavoro richiesto è il seguente:

1. Eseguire dal client una richiesta di arresto del nodo.

2. Inviare la richiesta al nodo giusto.

    a. Se il nodo non viene trovato, la richiesta ha esito negativo.

    b. Se il nodo viene trovato, la richiesta viene restituita solo se il nodo è stato arrestato.

Per verificare l'errore dalla prospettiva del test è necessario sapere che, quando viene provocato, l'errore si verifica effettivamente. La garanzia offerta da Service Fabric consiste nel fatto che il nodo sarà disattivato oppure lo era già quando il comando lo ha raggiunto. In entrambi i casi il test deve essere in grado di verificare lo stato e procedere con l'esatta convalida (riuscito o non riuscito). Un sistema implementato all'esterno di Service Fabric per eseguire lo stesso set di errori potrebbe implicare numerosi problemi di rete, hardware e software che impedirebbero al sistema stesso di offrire le garanzie indicate in precedenza. In presenza dei problemi elencati, Service Fabric consentirà di riconfigurare lo stato del cluster per risolvere i problemi e pertanto il sottosistema Testabilità continuerà a essere in grado di fornire le garanzie corrette.

### Generazione degli eventi e degli scenari richiesti

Se è difficile iniziare a simulare un errore reale in modo coerente, essere in grado di generare errori correlati è ancora più complesso. Ad esempio, in un servizio persistente con stato si verifica una perdita di dati quando si verificano i seguenti eventi:

1. Nella replica viene rilevato solo un quorum di scrittura delle repliche. Tutte le repliche secondarie restano dietro quelle primarie.

2. Il quorum di scrittura si interrompe in quanto le repliche si arrestano a causa dell'arresto di un pacchetto di codice o di un nodo.

3. Il quorum di scrittura non può tornare attivo perché i dati per le repliche sono stati persi a causa di un danno del disco o della ricreazione dell'immagine del computer.

Questi errori correlati si verificano effettivamente nel mondo reale, anche se non con la stessa frequenza dei singoli errori. La possibilità di testare questi scenari prima che si verifichino nell'ambiente di produzione è fondamentale. Ancora più importante è la possibilità di simulare questi scenari con carichi di lavoro di produzione in circostanze controllate, ad esempio durante la giornata con tutti gli ingegneri al lavoro, il che è di gran lunga preferibile al rischio che lo scenario si verifichi per la prima volta nell'ambiente di produzione alle 2:00.

### Esperienza unificata tra ambienti diversi

Di solito, la pratica consisteva nel creare tre set di esperienze diversi, uno per l'ambiente di sviluppo, uno per i test e uno per la produzione. Il modello era quello indicato di seguito:

1. Nell'ambiente di sviluppo generare transizioni di stato che consentissero unit test dei singoli metodi.

2. Nell'ambiente di test riprodurre errori per consentire ai test end-to-end di fare pratica con scenari di errore diversi.

3. Mantenere l'ambiente di produzione originario per impedire qualsiasi errore non naturale e garantire una risposta umana estremamente rapida all'errore.

In Service Fabric, attraverso il modulo e il sottosistema Testabilità, si propone di capovolgere tutto questo e di usare la stessa metodologia dall'ambiente di sviluppo alla produzione. A questo scopo è possibile procedere in due modi:

1. Per indurre errori controllati, usare le API di Testabilità da un ambiente di piccole dimensioni fino ai cluster di produzione.

2. Per fare in modo che il cluster induca errori automaticamente, usare il sottosistema Testabilità per generare errori automatici. Il controllo della frequenza degli errori tramite la configurazione consente al servizio stesso di essere sottoposto a test in modo diverso in ambienti differenti.

Con Service Fabric, sebbene la scala degli errori sarà diversa in ambienti differenti, il meccanismo effettivo sarà identico. Ciò consente l'uso di un codice molto più rapido per la pipeline di distribuzione e la possibilità di testare i servizi con carichi di lavoro reali.

## Utilizzo di Testabilità

### Utilizzo di Testabilità in C#

Le funzionalità di Testabilità sono presenti in System.Fabric.Testability.dll. Questa dll è disponibile nel pacchetto NuGet Microsoft.ServiceFabric.Testability.nupack. Per usare le funzionalità di Testabilità, includere il pacchetto NuGet come riferimento nel progetto.

### Utilizzo di Testabilità in PowerShell

Per usare il modulo PowerShell di Testabilità, è necessario installare il file con estensione msi di runtime. Dopo aver installato il file con estensione msi, il modulo ServiceFabricTestability di PowerShell viene caricato automaticamente per essere usato dagli sviluppatori.

## Conclusioni

Per creare servizi effettivamente a livello di cloud, è fondamentale assicurarsi, prima e dopo la distribuzione, che i servizi siano in grado di resistere agli errori reali. Con i servizi attuali, la possibilità di innovare rapidamente e spostare velocemente il codice nella produzione è molto importante. Testabilità di Service Fabric consente agli sviluppatori del servizio di ottenere esattamente questo risultato.

## Passaggi successivi

- [Azioni di Testabilità](service-fabric-testability-actions.md)
- [Scenari di testabilità](service-fabric-testability-actions.md)
- Come eseguire il test del servizio
  - [Simulare gli errori durante i carichi di lavoro del servizio](service-fabric-testability-workload-tests.md)
  - [Errori di comunicazione da servizio a servizio](service-fabric-testability-scenarios-service-communication.md)

<!---HONumber=AcomDC_0204_2016-->