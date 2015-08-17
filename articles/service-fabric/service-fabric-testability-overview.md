<properties
   pageTitle="Panoramica di Testabilità"
   description="In questo articolo viene descritta la funzionalità di testabilità in Infrastruttura di servizi di Microsoft Azure."
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
   ms.date="07/13/2015"
   ms.author="rsinha"/>

# Panoramica di Testabilità

Testabilità è una suite di strumenti progettati specificamente per il test di servizi basati su Infrastruttura di servizi di Microsoft Azure. Gli strumenti consentono allo sviluppatore di causare, in modo semplice, errori significativi ed eseguire scenari di test per verificare e convalidare i numerosi stati diversi e le transizioni sperimentate da un servizio nel corso della durata, il tutto in modo controllato e sicuro.

Testabilità fornisce azioni e scenari che consentono il verificarsi di quanto illustrato in precedenza. Le azioni sono i singoli errori destinati a un servizio a scopo di test. Uno sviluppatore di servizi può utilizzarle come blocchi predefiniti per scrivere scenari complicati. Ad esempio:

  + Riavviare un nodo per simulare un qualsiasi numero di situazioni in cui un computer o una macchina virtuale viene riavviata.
  + Spostare una replica del servizio con stato per simulare il bilanciamento del carico, il failover o l’aggiornamento dell'applicazione.
  + Richiamare la perdita del quorum in un servizio con stato per creare una situazione in cui le operazioni di scrittura non possono continuare perché il numero delle repliche di "backup" o "secondarie" non è sufficiente per accettare nuovi dati.
  + Richiamare la perdita di dati in un servizio con stato per creare una situazione in cui tutto lo stato in memoria sia completamente cancellato.

Gli scenari sono test completi sono costituiti da una o più azioni e poiché le azioni sono semplicemente comandi di PowerShell e funzioni di C#, possono assumere qualsiasi forma o: servizi a esecuzione prolungata, comandi di PowerShell, applicazioni da riga di comando e così via. In Testabilità vengono forniti due scenari predefiniti:

  + Test chaos
  + Test di failover

Testabilità espone PowerShell e le API C#. In questo modo lo sviluppatore del servizio dispone di una maggiore agilità con lo script di PowerShell e un maggiore controllo delle API C#, a seconda delle esigenze.

## Importanza di Testabilità

Infrastruttura di servizi rende molto più semplice il processo di scrittura e gestione delle applicazioni scalabili distribuite. Allo stesso modo, il componente Testabilità in Infrastruttura di servizi ha lo scopo di semplificare il test di un'applicazione distribuita. Esistono tre problemi principali che devono essere risolti durante il test:

1. La simulazione o la generazione di errori che potrebbero verificarsi negli scenari reali: uno degli aspetti importanti di Infrastruttura di servizi è che consente alle applicazioni distribuite di correggere vari errori. Tuttavia, per verificare che l'applicazione sia in grado di correggere questi errori, è necessario un meccanismo per simulare/generare questi errori reali in un ambiente di test controllato.
2. La capacità di generare errori correlati: sebbene gli errori di base nel sistema come gli errori di rete e gli errori dei computer siano facili da produrre singolarmente, generare il numero significativo di scenari che possono verificarsi nel mondo reale a seguito delle interazioni di questi singoli errori non è semplice.
3. L’esperienza unificata tra vari livelli di sviluppo e distribuzione: esistono molti sistemi di fault injection che consentono di generare diversi tipi di errori. Tuttavia, quando si passa da uno scenario di sviluppo in un ambiente di piccole dimensioni all’esecuzione degli stessi test in ambienti di test di grandi dimensioni per utilizzarli nella produzione l’esperienza è più complessa.

Sebbene esistano numerosi meccanismi per risolvere i problemi elencati in precedenza, manca ancora un sistema in grado di eseguire le stesse operazioni con le garanzie richieste, direttamente da un ambiente di sviluppo di piccole dimensioni ai test nei cluster di produzione. Il componente Testabilità consente agli sviluppatori di applicazioni di concentrarsi sul test della logica di business. La funzionalità di testabilità offre tutte le funzionalità necessarie per testare l'interazione del servizio con il sistema distribuito sottostante.

### Simulazione/generazione di scenari di errore reali
Per verificare l'affidabilità di un sistema distribuito in caso di errori, è necessario un meccanismo per generare errori. Sebbene dal punto di vista teorico generare un errore come un nodo inattivo sembri semplice, comincia a comportare gli stessi problemi di coerenza che si tenta di risolvere con Infrastruttura di servizi. Ad esempio, se si desidera arrestare un nodo, il flusso di lavoro richiesto è il seguente:

1. Eseguire dal client una richiesta di arresto del nodo.
2. Inviare la richiesta al nodo giusto.
	1. Se il nodo non viene trovato, la richiesta deve avere esito negativo.
	2. Se il nodo viene trovato deve essere restituita solo se il nodo viene arrestato.

Dalla prospettiva del test, è necessario sapere che quando l'errore viene provocato si verifichi effettivamente per verificare l’errore. La garanzia offerta da Windows Fabric consiste nel fatto che il nodo sarà disattivato oppure lo è già quando il comando lo ha raggiunto. In entrambi i casi il test deve essere in grado di verificare lo stato e procedere con l’esatta convalida (riuscito o non riuscito). Un sistema implementato all'esterno di Infrastruttura di servizi per eseguire lo stesso set di errori potrebbe implicare un numero enorme di problemi di rete, hardware e software che impedirebbero al sistema stesso di offrire le garanzie indicate in precedenza. In presenza dei problemi elencati, Infrastruttura di servizi consentirà di riconfigurare lo stato del cluster per risolvere i problemi e pertanto Testabilità continuerà a essere in grado di fornire le garanzie corrette.

### Generazione degli eventi e dello scenario richiesti
Se è difficile iniziare a simulare un errore reale in modo coerente, essere in grado di generare errori correlati è ancora più complesso. Ad esempio, in un servizio persistente con stato si verifica una perdita di dati quando si verificano i seguenti eventi:

1. Nella replica viene rilevato solo un quorum di scrittura delle repliche. Tutte le repliche secondarie restano dietro quelle primarie.
2. Il quorum di scrittura si interrompe in quanto le repliche si arrestano (a causa dell’arresto del pacchetto di codice o del nodo).
3. Il quorum di scrittura non è in grado di tornare attivo perché i dati per le repliche sono persi (a causa di un danno del disco o della ricreazione dell’immagine del computer).

Questi errori correlati si verificano effettivamente (anche se non con la stessa frequenza dei singoli errori) nel mondo reale. La possibilità di testare questi scenari prima che si verifichino nell'ambiente di produzione è fondamentale. Ancora di più, la possibilità di simularli nel carico di lavoro di produzione in circostanze controllate (di giorno con tutti gli ingegneri al lavoro) è di gran lunga preferibile rispetto quando si verificano per la prima volta in produzione alle due del mattino.

### Esperienza unificata tra ambienti diversi
Di solito, la pratica consisteva nel creare tre set di esperienze diversi, uno per l'ambiente di sviluppo, uno per i test e uno per la produzione. Il modello era quello indicato di seguito:

1. Nell'ambiente di sviluppo generare transizioni di stato che consentissero unit test dei singoli metodi.
2. Nell’ambiente di test generare errori per consentire ai test end-to-end di fare pratica con scenari di errore diversi.
3. Mantenere l'ambiente di produzione originario impedendo qualsiasi errore non naturale e garantire una risposta umana estremamente rapida all’errore.

In Infrastruttura di servizi, attraverso il modulo e il servizio Testabilità proponiamo di capovolgere tutto questo e di utilizzare la stessa metodologia dall’ambiente di sviluppo alla produzione. A questo scopo è possibile procedere in due modi: 1. Per indurre errori controllati, utilizzare le API testabilità da un ambiente piccolo fino ai cluster di produzione. 2. Per fare in modo che il cluster induca errori automaticamente, utilizzare il servizio Testabilità per generare errori automatici. Il controllo della frequenza degli errori tramite la configurazione consente al servizio stesso di essere sottoposto a test in modo diverso in ambienti differenti.

Con Infrastruttura di servizi, sebbene la scala degli errori sarà diversa in ambienti differenti, il meccanismo effettivo sarà identico. Ciò consente un codice molto più rapido per la pipeline di distribuzione e la possibilità di testare i servizi, conservando il carico di lavoro reale.

## Utilizzo di Testabilità
### Utilizzo di Testabilità in C# 
Le diverse funzionalità di testabilità sono presenti in System.Fabric.Testability.dll. Questa dll è disponibile nel pacchetto nuget Microsoft.ServiceFabric.Testability.nupack. Per utilizzare le funzionalità di Testabilità, includere il pacchetto nuget come riferimento nel progetto.

## Utilizzo di Testabilità in PowerShell
Per utilizzare Testabilità in PowerShell è necessaria un’installazione di MSI runtime. Una volta installato MSI, il modulo ServiceFabricTestability di PowerShell viene caricato automaticamente per essere utilizzato dagli sviluppatori.

## Conclusioni
Per creare servizi effettivamente di scala cloud, è fondamentale la capacità di garantire che i servizi siano in grado di sopportare gli errori del mondo reale prima di essere distribuiti (e anche in fase di distribuzione nell’ambiente di produzione). Anche nel mondo dei servizi di oggi, la possibilità di innovare rapidamente e spostare il codice nella produzione è molto importante. La funzionalità Testabilità in Infrastruttura di servizi consente agli sviluppatori di servizi di eseguire proprio le operazioni illustrate in precedenza.

## Passaggi successivi

- [Azioni di Testabilità](service-fabric-testability-actions.md)
- [Scenari di testabilità](service-fabric-testability-actions.md)
- Come eseguire il test del servizio
	- [Simulare gli errori durante i carichi di lavoro del servizio](service-fabric-testability-workload-tests.md)
   - [Errori di comunicazione da servizio a servizio](service-fabric-testability-scenarios-service-communication.md)

 

<!---HONumber=August15_HO6-->