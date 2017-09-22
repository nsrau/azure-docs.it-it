---
title: Profilatura delle app Web attive in Azure con Application Insights | Microsoft Docs
description: Identificare il percorso ricorrente nel codice del server web con un profiler con footprint ridotto.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 252e1fb070bcdc11494f6f37a9a1ee03fa50509e
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="profiling-live-azure-web-apps-with-application-insights"></a>Profilatura delle app Web di Azure attive con Application Insights

*Questa funzionalità di Application Insights è GA per i servizi app e in anteprima per il calcolo.*

Determinare il tempo impiegato in ogni metodo nell'applicazione Web attiva usando lo strumento di profilatura di [Azure Application Insights](app-insights-overview.md). Consente di visualizzare profili dettagliati delle richieste attive che sono state servite dall'app ed evidenzia il 'percorso ricorrente' che usa più tempo. Seleziona automaticamente esempi con tempi di risposta diversi. Il profiler usa varie tecniche per ridurre il sovraccarico.

Il profiler è valido attualmente solo per le applicazioni Web ASP.NET in esecuzione nel Servizio app di Azure, in almeno il piano tariffario Basic.

<a id="installation"></a>
## <a name="enable-the-profiler"></a>Abilitare il profiler

[Installare Application Insights](app-insights-asp-net.md) nel codice. Se è già installato, assicurarsi che la versione in uso sia la più recente. A tale scopo, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Gestisci pacchetti NuGet. Selezionare gli aggiornamenti e aggiornare tutti i pacchetti. Distribuire nuovamente l'app.

*Uso di ASP.NET Core [Fare clic qui](#aspnetcore)*.

In [https://portal.azure.com](https://portal.azure.com) aprire la risorsa di Application Insights per l'App Web. Aprire **Prestazioni** e fare clic su **Abilita Application Insights Profiler...**.

![Fare clic sul banner Abilita profiler][enable-profiler-banner]

In alternativa, è sempre possibile fare clic su **Configura** per visualizzare lo stato, abilitare o disabilitare il profiler.

![Nel pannello Prestazioni fare clic su Configura.][performance-blade]

Le app Web configurate con Application Insights sono elencate nel pannello Configura. Seguire le istruzioni per installare l'agente Profiler, se necessario. Se nessuna app Web è stata ancora configurata con Application Insights, fare clic su *Add Linked Apps* (Aggiungere app collegate).

Usare i pulsanti *Enable Profiler* (Abilita profiler) o *Disable Profiler* (Disabilita profiler) nel pannello Configura per controllare il profiler in tutte le app Web collegate.



![Pannello Configura][linked app services]

Per arrestare o riavviare il profiler per una singola istanza del servizio app, è possibile trovarlo nella **risorsa del servizio app** in **Processi Web**. Per eliminarlo, cercare in **Estensioni**.

![Disabilitare il profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare il profiler su tutte le app Web per individuare eventuali problemi di prestazioni il prima possibile.

Se si usa WebDeploy per distribuire le modifiche all'Applicazione Web, assicurarsi di escludere la cartella **App_Data** dall'eliminazione durante la distribuzione. In caso contrario, verranno eliminati i file dell'estensione profiler alla successiva distribuzione dell'applicazione Web in Azure.

### <a name="using-profiler-with-azure-vms-and-compute-resources-preview"></a>Uso del profiler con le macchine virtuali di Azure e le risorse di calcolo (anteprima)

Quando si [abilita Application Insights per i servizi app di Azure in fase di esecuzione](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights), il profiler è automaticamente disponibile. Se Application Insights è già abilitato per la risorsa, potrebbe essere necessario aggiornarlo alla versione più recente tramite la procedura guidata **Configura**.

Esiste una [versione di anteprima del profiler per le risorse di calcolo di Azure](https://go.microsoft.com/fwlink/?linkid=848155).


## <a name="limitations"></a>Limitazioni

La conservazione predefinita dei dati è di 5 giorni. Massimo 10 GB inseriti al giorno.

Non è previsto alcun addebito per il servizio profiler. L'app Web deve essere ospitata almeno al livello di base dei servizi app.

## <a name="overhead-and-sampling-algorithm"></a>Overhead e algoritmo di campionamento

Il profiler viene eseguito in modo casuale per 2 minuti ogni ora in ogni macchina virtuale che ospita l'applicazione con il profiler abilitato per acquisire le tracce. Quando il profiler è in esecuzione, aggiunge al server tra il 5 e il 15% di overhead della CPU.
Maggiore è il numero di server disponibili per ospitare l'applicazione, minore l'impatto del profiler sulle prestazioni complessive dell'applicazione. Questo accade perché l'algoritmo di campionamento fa sì che il profiler venga eseguito in qualsiasi momento solo sul 5% dei server e un maggior numero di server sarà disponibile per elaborare le richieste Web di scartare i server con overhead dal profiler.


## <a name="viewing-profiler-data"></a>Visualizzazione dei dati del profiler

Aprire il pannello Prestazioni e scorrere verso il basso l'elenco di operazioni.




![Colonna di esempi del pannello delle prestazioni di Application Insights][performance-blade-examples]

Le colonne nella tabella sono:

* **Conteggio**: il numero di queste richieste nell'intervallo di tempo del pannello.
* **Mediana**: il tempo tipico che l'app impiega per rispondere a una richiesta. La metà di tutte le risposte è più veloce rispetto a questo valore.
* **95° percentile**: il 95% delle risposte ha una velocità superiore rispetto a questo valore. Se questo valore è molto diverso da quello mediano, potrebbe essersi verificato un problema intermittente con l'app. In alternativa la causa potrebbe essere una funzione di progettazione, ad esempio la memorizzazione nella cache.
* **Esempi**: un'icona indica che il profiler ha acquisito le analisi dello stack per questa operazione.

Fare clic sull'icona degli esempi per aprire l'Explorer di analisi, che mostra alcuni esempi acquisiti dal profiler, classificati in base al tempo di risposta.

Selezionare un esempio per mostrare i dettagli a livello di codice del tempo impiegato per l'esecuzione della richiesta.

![Explorer di analisi Application Insights][trace-explorer]

**Mostra percorso ricorrente** apre il nodo foglia di dimensioni maggiore o almeno un elemento simile. Nella maggior parte dei casi questo nodo sarà adiacente a un collo di bottiglia delle prestazioni.



* **Etichetta**: nome della funzione o dell'evento. La struttura mostra una combinazione di codice e di eventi che si è verificata, ad esempio eventi SQL e http. L'evento principale rappresenta la durata complessiva della richiesta.
* **Tempo trascorso**: l'intervallo di tempo tra l'inizio e la fine dell'operazione.
* **Quando**: indica quando la funzione o l'evento è stato eseguito in relazione ad altre funzioni.

## <a name="how-to-read-performance-data"></a>Procedura: leggere i dati sulle prestazioni

Il profiler del servizio Microsoft usa una combinazione della strumentazione e del metodo di campionamento per analizzare le prestazioni dell'applicazione.
Quando la raccolta dettagliata è in corso, il profiler del servizio campiona il puntatore all'istruzione di ciascuna delle CPU del computer ogni millisecondo.
Ogni esempio acquisisce lo stack di chiamate completo del thread attualmente in esecuzione, fornendo informazioni utili e dettagliate sulle operazioni che il thread stava eseguendo a livelli di astrazione alti e bassi. Il profiler del servizio raccoglie anche altri eventi, ad esempio gli eventi di commutazione di contesto, TPL e del pool di thread per tenere traccia della causalità e della correlazione delle attività.

Lo stack di chiamate mostrato nella visualizzazione della sequenza temporale è il risultato del campionamento e della strumentazione precedente. Poiché ogni esempio acquisisce lo stack di chiamate completo del thread, include il codice da .NET Framework, nonché altri framework a cui che si fa riferimento.

### <a id="jitnewobj"></a>Allocazione oggetti (`clr!JIT\_New or clr!JIT\_Newarr1`)
`clr!JIT\_New and clr!JIT\_Newarr1` sono le funzioni di supporto all'interno di .NET Framework che consentono di allocare memoria dall'heap gestito. `clr!JIT\_New` viene richiamato quando si alloca un oggetto. `clr!JIT\_Newarr1` viene richiamato quando si alloca una matrice di oggetti. Queste due funzioni sono in genere molto veloci e richiedono un intervallo temporale relativamente ridotto. Se `clr!JIT\_New` o `clr!JIT\_Newarr1` impiegano una notevole quantità di tempo nell'indicatore cronologico, è possibile che il codice stia allocando numerosi oggetti e usando una quantità notevole di memoria.

### <a id="theprestub"></a>Caricamento di codice (`clr!ThePreStub`)
`clr!ThePreStub` è una funzione di supporto all'interno di .NET Framework che prepara il codice da eseguire per la prima volta. Include ad esempio la compilazione JIT (Just In Time). Per ogni metodo in C#, `clr!ThePreStub` deve essere richiamato più volte nel corso della durata di un processo.

Se `clr!ThePreStub` richiede molto tempo per una richiesta, ciò indica che questa è la prima richiesta che esegue questo metodo e il tempo di caricamento del metodo da parte del runtime di .NET Framework è elevato. È possibile valutare l'ipotesi di eseguire un processo di riscaldamento che esegua tale parte del codice prima che gli utenti accedano ad esso o di eseguire NGen negli assembly.

### <a id="lockcontention"></a>Conflitto di blocchi (`clr!JITutil\_MonContention` o `clr!JITutil\_MonEnterWorker`)
`clr!JITutil\_MonContention` o `clr!JITutil\_MonEnterWorker` indicano che il thread corrente è in attesa del rilascio di un blocco. In genere viene visualizzato durante l'esecuzione di un'istruzione di blocco C#, richiamando il metodo Monitor.Enter o richiamando un metodo con l'attributo MethodImplOptions.Synchronized. Il conflitto di blocchi si verifica in genere quando il thread A acquisisce un blocco e il thread B tenta di acquisire lo stesso blocco prima che il thread A lo rilasci.

### <a id="ngencold"></a>Caricamento di codice (`[COLD]`)
Se il nome del metodo contiene `[COLD]`, ad esempio `mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined`, significa che il runtime di .NET Framework sta eseguendo il codice non ottimizzato da <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">ottimizzazione PGO</a> per la prima volta. Per ogni metodo verrà visualizzato al massimo una volta nel corso del processo.

Un tempo elevato di caricamento del codice per una richiesta indica che si tratta della prima richiesta che esegue la parte non ottimizzata del metodo. È possibile considerare l'ipotesi di eseguire un processo di riscaldamento che esegua tale parte del codice prima che gli utenti accedano ad esso.

### <a id="httpclientsend"></a>Inviare una richiesta HTTP
I metodi, ad esempio `HttpClient.Send`, indicano che il codice è in attesa che una richiesta HTTP venga completata.

### <a id="sqlcommand"></a>Operazione di database
Il metodo, ad esempio SqlCommand.Execute, indica che il codice è in attesa che un'operazione di database venga completata.

### <a id="await"></a>In attesa (`AWAIT\_TIME`)
`AWAIT\_TIME` indica che il codice è in attesa del completamento di un'altra attività. Questo accade solitamente con l'istruzione C# 'await'. Quando il codice esegue C# 'await', il thread viene rimosso e restituisce il controllo al pool di thread e non esiste un thread bloccato in attesa del completamento di 'await'. Tuttavia, in modo logico il thread che ha eseguito await viene 'bloccato' in attesa che venga completata l'operazione. `AWAIT\_TIME` indica il tempo di blocco in attesa del completamento dell'attività.

### <a id="block"></a>Tempo blocco
`BLOCKED_TIME` indica che il codice è in attesa che un'altra risorsa sia disponibile, ad esempio è in attesa di un oggetto di sincronizzazione, che un thread diventi disponibile o che venga completata una richiesta.

### <a id="cpu"></a>Tempo CPU
La CPU è occupata nell'esecuzione di istruzioni.

### <a id="disk"></a>Tempo disco
L'applicazione sta eseguendo operazioni su disco.

### <a id="network"></a>Tempo rete
L'applicazione sta eseguendo operazioni sulla rete.

### <a id="when"></a>Colonna Quando
Si tratta di una visualizzazione della variazione nel tempo degli esempi INCLUSIVI raccolti per un nodo nel tempo. L'intervallo totale della richiesta è diviso in 32 bucket orari ed esempi inclusivi per questo nodo vengono accumulati in questi 32 bucket. Ogni bucket è rappresentato come una barra, la cui altezza rappresenta un valore in scala. Per i nodi contrassegnati `CPU_TIME` o `BLOCKED_TIME` oppure in cui è presente una relazione ovvia di utilizzo di una risorsa (CPU, disco, thread), la barra rappresenta l'utilizzo di una di queste risorse per il periodo di tempo di questo bucket. Per queste metriche è possibile ottenere più del 100% utilizzando più risorse. Ad esempio, se in media si usano due CPU in un intervallo si ottiene il 200%.


## <a id="troubleshooting"></a>Risoluzione dei problemi

### <a name="how-can-i-know-whether-application-insights-profiler-is-running"></a>Come è possibile verificare se il profiler di Application Insights è in esecuzione?

Il profiler viene eseguito come processo Web continuo in App Web. È possibile aprire la risorsa App Web in https://portal.azure.com e controllare lo stato di "ApplicationInsightsProfiler" nel pannello dei processi Web. Se non è in esecuzione, aprire **Log** per altre informazioni.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Perché non è possibile trovare esempi di stack anche se il profiler è in esecuzione?

Ecco alcuni aspetti da controllare:

1. Assicurarsi che il piano di servizio app sia di livello Basic e superiore.
2. Assicurarsi che l'app Web disponga di Application Insights SDK 2.2 Beta e superiore abilitato.
3. Assicurarsi che nell'App Web l'impostazione APPINSIGHTS_INSTRUMENTATIONKEY abbia la stessa chiave di strumentazione usata da Application Insights SDK.
4. Assicurarsi che l'App Web sia in esecuzione su .Net Framework 4.6.
5. Se si tratta di un'applicazione ASP.NET Core, verificare anche le [dipendenze delle richieste](#aspnetcore).

Una volta avviato il profiler, è previsto un breve periodo di riscaldamento durante il quale il profiler raccoglie attivamente diverse analisi di prestazioni. Successivamente, il profiler raccoglie le analisi di prestazioni per due minuti ogni ora.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Stavo usando il profiler del servizio Azure. Che cosa è successo?  

Quando si abilita il profiler di Application Insights, l'agente del profiler del servizio Azure viene disabilitato.

### <a id="double-counting"></a>Doppio conteggio dei thread in parallelo

In alcuni casi la metrica del tempo totale nel visualizzatore dello stack supera la durata effettiva della richiesta.

Questa situazione può verificarsi quando sono presenti due o più thread associati a una richiesta, che operano in parallelo. Il tempo totale di thread è quindi superiore al tempo trascorso. In molti casi un thread può essere in attesa dell completamento di un altro. Il visualizzatore tenta di rilevare questa situazione e omettere l'attesa non interessante, ma sbaglia mostrando l'eccesso anziché l'omissione di quelle che possono essere informazioni critiche.  

Quando sono presenti thread in parallelo nelle analisi, è necessario determinare quali thread sono in attesa in modo da poter determinare il percorso critico per la richiesta. Nella maggior parte dei casi, il thread che entra rapidamente in uno stato di attesa è semplicemente in attesa di altri thread. Concentrarsi sugli altri e ignorare il tempo nei thread in attesa.

### <a id="issue-loading-trace-in-viewer"></a>Senza dati di profilatura

1. Se i dati che si sta tentando di visualizzare sono antecedenti a un paio di settimane, è possibile limitare il filtro temporale e riprovare.

2. Verificare che un firewall o i proxy non abbiano bloccato l'accesso a https://gateway.azureserviceprofiler.net.

3. Verificare che la chiave di strumentazione di Application Insights in uso nell'app sia la stessa della risorsa di Application Insights per cui è stata abilitata la profilatura. La chiave è in genere in ApplicationInsights.config, ma può essere disponibile anche nel file web.config o app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Report di errori nel visualizzatore di profilatura

Archiviare un ticket di supporto dal portale. Includere l'ID di correlazione dal messaggio di errore.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Errore di distribuzione Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se si intende ridistribuire l'app Web a una risorsa di Servizi app con il profiler abilitato, può essere visualizzato un errore simile al seguente: Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'. Questo errore viene generato se si esegue Distribuzione Web da script o nella pipeline di distribuzione di Visual Studio Team Services.
Per risolvere questo problema aggiungere i seguenti parametri di distribuzione all'attività di Distribuzione Web:

```
-skip:skipaction='Delete',objectname='filePath',absolutepath='\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler\\.*' 
-skip:skipaction='Delete',objectname='dirPath',absolutepath='\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler\\.*'
-skip:skipaction='Delete',objectname='filePath',absolutepath='\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler2\\.*'
-skip:skipaction='Delete',objectname='dirPath',absolutepath='\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler2\\.*'
```

Ciò elimina la cartella usata da App Insights Profiler e sblocca il processo di ridistribuzione. Non ha impatto sull'istanza del profiler attualmente in esecuzione.


## <a name="manual-installation"></a>Installazione manuale

Quando si configura il profiler, gli aggiornamenti seguenti vengono eseguiti nelle impostazioni dell'App Web. È possibile eseguire queste operazioni manualmente se l'ambiente lo richiede, ad esempio se l'applicazione viene eseguita nell'ambiente del servizio app di Azure:

1. Nel pannello di controllo dell'app Web aprire Impostazioni.
2. Impostare la versione di .Net Framework su 4.6.
3. Attivare "Always On".
4. Aggiungere l'impostazione dell'app "__APPINSIGHTS_INSTRUMENTATIONKEY__" e impostare il valore sulla stessa chiave di strumentazione usata dall'SDK.
5. Aprire Strumenti avanzati.
6. Fare clic su "OK" per aprire il sito Web Kudu.
7. Nel sito Web Kudu selezionare "Site extensions" ("Estensioni del sito").
8. Installare "__Application Insights__" dalla raccolta.
9. Riavviare l'app Web .

## <a id="aspnetcore"></a>Supporto di ASP.NET Core

L'applicazione ASP.NET Core deve installare il pacchetto NuGet Microsoft.ApplicationInsights.AspNetCore versione 2.1.0-beta6 o successiva per poter interagire con il profiler. Dopo il 27 giugno 2017 non sono più supportate le versioni precedenti.

## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

