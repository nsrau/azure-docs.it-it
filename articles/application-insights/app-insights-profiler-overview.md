---
title: Profilare le applicazioni di produzione in Azure con Application Insights Profiler | Microsoft Docs
description: Identificare il percorso ricorrente nel codice del server web con un profiler con footprint ridotto.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 7e0734790a0b33a52ace01ad961b1bc3127fa3d4
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976520"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profilare le applicazioni di produzione in Azure con Application Insights
## <a name="enable-profiler-for-your-application"></a>Abilitare Profiler per l'applicazione

Application Insights Profiler offre tracce delle prestazioni per le applicazioni in esecuzione nell'ambiente di produzione in Azure. Acquisisce automaticamente i dati su larga scala senza influire negativamente sugli utenti finali. Profiler consente di identificare il percorso di codice "ad accesso frequente" che presenta i tempi più lunghi per la gestione di una richiesta Web specifica. 

Il profiler funziona con le applicazioni .net distribuite nei servizi di Azure seguenti. Le istruzioni specifiche per abilitare il profiler per ogni tipo di servizio sono disponibili ai collegamenti seguenti.

* [Servizi app](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni di Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali e set di scalabilità delle macchine virtuali](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Se il profiler è abilitato, ma non vengono visualizzate le tracce, controllare la [Guida alla risoluzione dei problemi.](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

L'esecuzione del profiler in locale non è ufficialmente supportata, ma sono disponibili alcune [istruzioni che è possibile provare.](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)

## <a name="view-profiler-data"></a>Visualizzare i dati del profiler

L'applicazione gestisce attivamente le richieste per consentire al profiler di caricare le tracce. Se si sta eseguendo un esperimento, è possibile generare le richieste da indirizzare all'app Web usando il [test delle prestazioni di Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Se è stato appena abilitato Profiler, è possibile eseguire un breve test di carico. Durante l'esecuzione del test di carico, premere il pulsante **Profile Now** (Profila adesso) nella [**pagina delle impostazioni del profiler**](app-insights-profiler-settings.md#profiler-settings-page). Mentre è in esecuzione, il profiler eseguirà la profilatura in modo casuale circa una volta ogni ora e per una durata di due minuti. Se l'applicazione sta gestendo un flusso costante di richieste, il profiler caricherà le tracce ogni ora.

Dopo che l'applicazione riceve una parte del traffico e il profiler ha avuto tempo per caricare le tracce, le tracce dovrebbero essere disponibili per la visualizzazione. Il processo potrebbe richiedere da 5 a 10 minuti. Per visualizzare le tracce, passare al riquadro **Prestazioni**, selezionare **Take Actions** (Esegui azioni) per visualizzare le tracce del profiler e quindi fare clic sul pulsante **Tracce del profiler**.

![Anteprima delle analisi di Profiler nel riquadro delle prestazioni di Application Insights][performance-blade]

Selezionare un esempio per visualizzare i dettagli a livello di codice del tempo impiegato per l'esecuzione della richiesta.

![Explorer di analisi Application Insights][trace-explorer]

La finestra di esplorazione delle analisi visualizza le informazioni seguenti:

* **Mostra percorso critico**: apre il nodo foglia di dimensioni maggiori o almeno un elemento simile. Nella maggior parte dei casi questo nodo è vicino a un collo di bottiglia delle prestazioni.
* **Etichetta**: nome della funzione o dell'evento. La struttura mostra una combinazione di codice e di eventi che si sono verificati, ad esempio eventi SQL e HTTP. L'evento principale rappresenta la durata complessiva della richiesta.
* **Tempo trascorso**: l'intervallo di tempo tra l'inizio e la fine dell'operazione.
* **Quando**: tempo in cui la funzione o l'evento è stato eseguito in relazione ad altre funzioni.

## <a name="how-to-read-performance-data"></a>Procedura: leggere i dati sulle prestazioni

Il profiler del servizio Microsoft usa una combinazione della strumentazione e dei metodi di campionamento per analizzare le prestazioni dell'applicazione. Quando la raccolta dettagliata è in corso, il profiler di servizi campiona il puntatore all'istruzione di ogni CPU di computer ogni millisecondo. Ogni esempio acquisisce lo stack di chiamate completo del thread attualmente in esecuzione. Fornisce informazioni dettagliate sull'attività del thread a livello sia generale che specifico di astrazione. Il profiler del servizio raccoglie anche altri eventi per tenere traccia della causalità e della correlazione delle attività, ad esempio gli eventi di commutazione di contesto, Task Parallel Library (TPL) e del pool di thread.

Lo stack di chiamate riportato nella visualizzazione della sequenza temporale è il risultato del campionamento e della strumentazione. Poiché ogni esempio acquisisce lo stack di chiamate completo del thread, include il codice di Microsoft .NET Framework e di eventuali altri framework a cui si fa riferimento.

### <a id="jitnewobj"></a>Allocazione di oggetti (clr!JIT\_New o clr!JIT\_Newarr1)

**clr!JIT\_New** e **clr!JIT\_Newarr1** sono funzioni di supporto in .NET Framework che allocano la memoria da un heap gestito. **clr!JIT\_New** viene richiamato quando si alloca un oggetto. **clr!JIT\_Newarr1** viene richiamato quando si alloca una matrice di oggetti. Queste due funzioni sono in genere veloci e richiedono un intervallo temporale relativamente ridotto. Se **clr!JIT\_New** o **clr!JIT\_Newarr1** impiega molto tempo nella sequenza temporale, è possibile che il codice stia allocando numerosi oggetti e utilizzando una notevole quantità di memoria.

### <a id="theprestub"></a>Caricamento di codice (clr!ThePreStub)

**clr!ThePreStub** è una funzione di supporto all'interno di .NET Framework che prepara il codice da eseguire per la prima volta. Ad esempio, include in genere la compilazione JIT. Per ogni metodo C#, **clr!ThePreStub** deve essere richiamato al massimo una volta nel corso della durata di un processo.

Se **clr!ThePreStub** richiede una maggiore quantità di tempo per una richiesta, indica che la richiesta è la prima a eseguire questo metodo. Il tempo di caricamento del primo metodo da parte del runtime di .NET Framework è significativo. È possibile valutare un processo di riscaldamento che esegua tale parte del codice prima che gli utenti accedano a esso oppure l'esecuzione del generatore di immagini native (ngen.exe) negli assembly.

### <a id="lockcontention"></a>Conflitto di blocchi (clr!JITutil\_MonContention o clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** o **clr!JITutil\_MonEnterWorker** indica che il thread corrente è in attesa di un blocco da rilasciare. Questo testo viene spesso visualizzato durante l'esecuzione di un'istruzione C# **LOCK** o quando viene richiamato il metodo **Monitor.Enter** o un metodo con l'attributo **MethodImplOptions.Synchronized**. Il conflitto di blocchi si verifica in genere quando il thread _A_ acquisisce un blocco e il thread _B_ prova ad acquisire lo stesso blocco prima che il thread _A_ lo rilasci.

### <a id="ngencold"></a>Caricamento di codice ([COLD])

Se il nome del metodo contiene **[COLD]**, ad esempio **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, il runtime di .NET Framework esegue per la prima volta il codice non ottimizzato dall'<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">ottimizzazione PGO</a>. Per ogni metodo deve essere visualizzato al massimo una volta nel corso del processo.

Un tempo sostanziale di caricamento del codice per una richiesta indica che si tratta della prima richiesta che esegue la parte non ottimizzata del metodo. Considerare un processo di riscaldamento che esegua tale parte del codice prima che gli utenti accedano a esso.

### <a id="httpclientsend"></a>Inviare una richiesta HTTP

Metodi come **HttpClient.Send** indicano che il codice è in attesa del completamento di una richiesta HTTP.

### <a id="sqlcommand"></a>Operazione di database

Metodi come **SqlCommand.Execute** indicano che il codice è in attesa del completamento di un'operazione di database.

### <a id="await"></a>Attesa (AWAIT\_TIME)

**AWAIT\_TIME** indica che il codice è in attesa del completamento di un'altra attività. Questo in genere accade con l'istruzione C# **AWAIT**. Quando il codice esegue C# **AWAIT**, il thread viene rimosso e restituisce il controllo al pool di thread e non esiste un thread bloccato in attesa del completamento di **AWAIT**. Tuttavia, in modo logico il thread che ha eseguito **AWAIT** viene "bloccato" in attesa che venga completata l'operazione. L'istruzione **AWAIT\_TIME** indica il tempo di blocco in attesa del completamento dell'attività.

### <a id="block"></a>Tempo di blocco

**BLOCKED_TIME** indica che il codice è in attesa di un'altra risorsa disponibile. Ad esempio, potrebbe essere in attesa di un oggetto di sincronizzazione, di un thread o del completamento di una richiesta.

### <a id="cpu"></a>Tempo di CPU

La CPU è occupata nell'esecuzione di istruzioni.

### <a id="disk"></a>Tempo del disco

L'applicazione sta eseguendo operazioni su disco.

### <a id="network"></a>Tempo di rete

L'applicazione sta eseguendo operazioni sulla rete.

### <a id="when"></a>Colonna Quando

La colonna **Quando** è una visualizzazione della variazione nel tempo degli esempi INCLUSIVI raccolti per un nodo nel tempo. L'intervallo totale della richiesta è suddiviso in 32 intervalli di tempo. Gli esempi inclusivi di tale nodo vengono accumulati in questi 32 intervalli. Ogni intervallo è rappresentato come una barra. L'altezza della barra rappresenta un valore ridimensionato. Per i nodi contrassegnati con **CPU_TIME** o **BLOCKED_TIME**, oppure in cui è presente una relazione ovvia con l'utilizzo di una risorsa (ad esempio, CPU, disco o thread), la barra rappresenta l'utilizzo di una delle risorse durante tale intervallo. Se si utilizzano più risorse, per queste metriche è possibile che si ottenga un valore maggiore del 100%. Se ad esempio si usano in media due CPU durante un intervallo, si ottiene il 200%.

## <a name="limitations"></a>Limitazioni

Il periodo di conservazione dati predefinito è di cinque giorni. La quantità massima di dati inseriti giornalmente è 10 GB.

Non sono previsti costi per l'uso del servizio Profiler, ma è necessario che l'app Web sia ospitata almeno al livello Basic di App Web.

## <a name="overhead-and-sampling-algorithm"></a>Overhead e algoritmo di campionamento

Profiler viene eseguito in modo casuale per due minuti ogni ora in ogni macchina virtuale che ospita l'applicazione con Profiler abilitato per acquisire le analisi. Quando è in esecuzione, Profiler comporta un sovraccarico della CPU del server compreso tra il 5% e il 15%.

## <a name="next-steps"></a>Passaggi successivi
Abilitare Application Insights Profiler per l'applicazione di Azure
* [Servizi app](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni di Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali e set di scalabilità delle macchine virtuali](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
