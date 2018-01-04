---
title: Profilare le app Web attive in Azure con Application Insights Profiler | Microsoft Docs
description: Identificare il percorso ricorrente nel codice del server web con un profiler con footprint ridotto.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: f8ba1a6308dfe234fff700d363fb9252b94570e2
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilare le app Web di Azure attive con Application Insights

*Questa funzionalità di Application Insights è disponibile a livello generale per Servizio app di Azure e in anteprima per le risorse di calcolo di Azure.*

Per determinare il tempo impiegato in ogni metodo nell'applicazione Web attiva, usare [Application Insights Profiler](app-insights-overview.md). Lo strumento di profilatura Application Insights consente di visualizzare profili dettagliati delle richieste attive che sono state servite dall'app ed evidenzia il *percorso critico* che usa più tempo. Il profiler seleziona automaticamente esempi con tempi di risposta diversi, quindi usa varie tecniche per ridurre al minimo il sovraccarico.

Il profiler è valido attualmente solo per le app Web ASP.NET in esecuzione in Servizio app di Azure, almeno nel livello di servizio Basic.

## <a id="installation"></a> Abilitare il profiler

[Installare Application Insights](app-insights-asp-net.md) nel codice. Se è già installato, assicurarsi che la versione in uso sia la più recente. Per controllare la versione più recente, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet** > **Aggiornamenti** > **Aggiorna tutti i pacchetti**. Ridistribuire l'app.

*Uso di ASP.NET Core - [Altre informazioni](#aspnetcore)*

Nel [portale di Azure](https://portal.azure.com) aprire la risorsa Application Insights per l'app Web. Selezionare **Prestazioni** > **Abilita Application Insights Profiler**.

![Selezionare il banner Abilita profiler][enable-profiler-banner]

In alternativa, è possibile selezionare **Configura** per visualizzare lo stato e abilitare o disabilitare il profiler.

![In Prestazioni selezionare Configura][performance-blade]

Le app Web configurate con Application Insights sono elencate in **Configura**. Seguire le istruzioni per installare l'agente profiler, se necessario. Se nessuna app Web è stata ancora configurata con Application Insights, selezionare **Aggiungi app collegate**.

Per controllare il profiler in tutte le app Web collegate, nel riquadro **Configura** selezionare **Abilita profiler** o **Disabilita profiler**.

![Opzioni del riquadro Configura][linked app services]

A differenza delle app Web ospitate attraverso i piani di servizio app, le applicazioni ospitate nelle risorse di calcolo di Azure (ad esempio, Macchine virtuali di Azure, set di scalabilità di macchine virtuali, Azure Service Fabric o Servizi cloud di Azure) non sono gestite direttamente da Azure. In questo caso non sono disponibili app Web a cui collegarsi. Anziché eseguire il collegamento a un'app, selezionare il pulsante **Abilita profiler**.

## <a name="disable-the-profiler"></a>Disabilitare Profiler
Per arrestare o riavviare il profiler per una singola istanza di Servizio app, in **Processi Web** accedere alla risorsa di Servizio app. Per eliminare il profiler, passare a **Estensioni**.

![Disabilitare il profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare il profiler su tutte le app Web per individuare eventuali problemi di prestazioni il prima possibile.

Se si usa Distribuzione Web per distribuire le modifiche all'applicazione Web, assicurarsi di escludere la cartella App_Data dall'eliminazione durante la distribuzione. In caso contrario, verranno eliminati i file dell'estensione profiler alla successiva distribuzione dell'applicazione Web in Azure.

### <a name="using-profiler-with-azure-vms-and-azure-compute-resources-preview"></a>Uso del profiler con le VM e le risorse di calcolo di Azure (anteprima)

Quando si [abilita Application Insights per Servizio app di Azure in fase di esecuzione](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights), Application Insights Profiler è automaticamente disponibile. Se Application Insights è già abilitato per la risorsa, potrebbe essere necessario aggiornarlo alla versione più recente tramite la procedura guidata Configura.

Sono disponibili informazioni su una [versione di anteprima del profiler per le risorse di calcolo di Azure](https://go.microsoft.com/fwlink/?linkid=848155).


## <a name="limitations"></a>Limitazioni

La conservazione predefinita dei dati è di cinque giorni. La quantità massima di dati inseriti al giorno è 10 GB.

Non sono previsti costi per l'uso del servizio profiler. Per usare il servizio profiler, l'app Web deve essere ospitata almeno al livello di base di Servizio app.

## <a name="overhead-and-sampling-algorithm"></a>Overhead e algoritmo di campionamento

Il profiler viene eseguito in modo casuale per due minuti ogni ora in ogni macchina virtuale che ospita l'applicazione con il profiler abilitato per acquisire le tracce. Quando il profiler è in esecuzione, aggiunge al server tra il 5 e il 15% di sovraccarico della CPU.
Maggiore è il numero di server disponibili per ospitare l'applicazione, minore l'impatto del profiler sulle prestazioni complessive dell'applicazione. Questo è dovuto al fatto che l'algoritmo di campionamento determina l'esecuzione del profiler solo nel 5% dei server in qualsiasi momento. Sono disponibili più server per elaborare le richieste Web ed eseguire l'offset del sovraccarico del server causato dall'esecuzione del profiler.


## <a name="view-profiler-data"></a>Visualizzare i dati del profiler

Passare al riquadro **Prestazioni** e quindi scorrere fino all'elenco delle operazioni.

![Colonna di esempi del riquadro delle prestazioni di Application Insights][performance-blade-examples]

La tabella delle operazioni contiene queste colonne:

* **CONTEGGIO**: il numero di queste richieste nell'intervallo di tempo del riquadro **CONTEGGIO**.
* **MEDIANA**: il tempo tipico che l'app impiega per rispondere a una richiesta. La metà di tutte le risposte è più veloce rispetto a questo valore.
* **95° PERCENTILE**: il 95% delle risposte ha una velocità superiore rispetto a questo valore. Se questo valore è molto diverso da quello mediano, potrebbe essersi verificato un problema intermittente con l'app. In alternativa la causa potrebbe essere una funzione di progettazione, ad esempio la memorizzazione nella cache.
* **TRACCE PROFILER**: un'icona indica che il profiler ha acquisito le analisi dello stack per questa operazione.

Selezionare **Visualizza** per aprire l'Explorer di analisi, che mostra alcuni esempi acquisiti dal profiler, classificati in base al tempo di risposta.

Se si usa il riquadro **Preview Performance** (Anteprima prestazioni), passare alla sezione **Take Actions** (Esegui azioni) della pagina per visualizzare le tracce del profiler. Selezionare il pulsante **Tracce Profiler**.

![Riquadro di anteprima delle prestazioni per Tracce Profiler in Application Insights][performance-blade-v2-examples]

Selezionare un esempio per mostrare i dettagli a livello di codice del tempo impiegato per l'esecuzione della richiesta.

![Explorer di analisi Application Insights][trace-explorer]

Explorer di analisi visualizza le informazioni seguenti:

* **Mostra percorso critico**: apre il nodo foglia di dimensioni maggiori o almeno un elemento simile. Nella maggior parte dei casi questo nodo è adiacente a un collo di bottiglia delle prestazioni.
* **Etichetta**: nome della funzione o dell'evento. La struttura mostra una combinazione di codice e di eventi verificatisi, ad esempio eventi SQL e HTTP. L'evento principale rappresenta la durata complessiva della richiesta.
* **Tempo trascorso**: l'intervallo di tempo tra l'inizio e la fine dell'operazione.
* **Quando**: indica quando la funzione o l'evento è stato eseguito in relazione ad altre funzioni.

## <a name="how-to-read-performance-data"></a>Procedura: leggere i dati sulle prestazioni

Il profiler del servizio Microsoft usa una combinazione della strumentazione e dei metodi di campionamento per analizzare le prestazioni dell'applicazione. Quando la raccolta dettagliata è in corso, il profiler del servizio campiona il puntatore all'istruzione di ciascuna delle CPU del computer ogni millisecondo. Ogni esempio acquisisce lo stack di chiamate completo del thread attualmente in esecuzione. Fornisce informazioni utili e dettagliate sull'attività del thread a un livello generale e specifico di astrazione. Il profiler del servizio raccoglie anche altri eventi per tenere traccia della causalità e della correlazione delle attività, ad esempio gli eventi di commutazione di contesto, Task Parallel Library (TPL) e del pool di thread.

Lo stack di chiamate mostrato nella visualizzazione della sequenza temporale è il risultato del campionamento e della strumentazione. Poiché ogni esempio acquisisce lo stack di chiamate completo del thread, include il codice di Microsoft .NET Framework, nonché di altri framework a cui si fa riferimento.

### <a id="jitnewobj"></a>Allocazione di oggetti (clr!JIT\_New o clr!JIT\_Newarr1)
**clr!JIT\_New** e **clr!JIT\_Newarr1** sono funzioni di supporto in .NET Framework che allocano la memoria da un heap gestito. **clr!JIT\_New** viene richiamato quando si alloca un oggetto. **clr!JIT\_Newarr1** viene richiamato quando si alloca una matrice di oggetti. Queste due funzioni sono in genere molto veloci e richiedono un intervallo temporale relativamente ridotto. Se **clr!JIT\_New** o **clr!JIT\_Newarr1** impiega una notevole quantità di tempo nella sequenza temporale, è possibile che il codice stia allocando numerosi oggetti e usando una quantità notevole di memoria.

### <a id="theprestub"></a>Caricamento di codice (clr!ThePreStub)
**clr!ThePreStub** è una funzione di supporto all'interno di .NET Framework che prepara il codice da eseguire per la prima volta. Include ad esempio la compilazione JIT. Per ogni metodo C#, **clr!ThePreStub** deve essere richiamato al massimo una volta nel corso della durata di un processo.

Se **clr!ThePreStub** richiede una notevole quantità di tempo per una richiesta, indica che la richiesta è la prima a eseguire questo metodo. Il tempo di caricamento del metodo da parte del runtime di .NET Framework è significativo. È possibile valutare un processo di riscaldamento che esegua tale parte del codice prima che gli utenti accedano a esso oppure l'esecuzione del generatore di immagini native (ngen.exe) negli assembly.

### <a id="lockcontention"></a>Conflitto di blocchi (clr!JITutil\_MonContention o clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** o **clr!JITutil\_MonEnterWorker** indica che il thread corrente è in attesa di un blocco da rilasciare. In genere viene visualizzato durante l'esecuzione di un'istruzione **LOCK** C#, richiamando il metodo **Monitor.Enter** o richiamando un metodo con l'attributo **MethodImplOptions.Synchronized**. Il conflitto di blocchi si verifica in genere quando il thread A acquisisce un blocco e il thread B tenta di acquisire lo stesso blocco prima che il thread A lo rilasci.

### <a id="ngencold"></a>Caricamento di codice ([COLD])
Se il nome del metodo contiene **[COLD]**, ad esempio **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, il runtime di .NET Framework esegue per la prima volta il codice non ottimizzato dall'<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">ottimizzazione PGO</a>. Per ogni metodo verrà visualizzato al massimo una volta nel corso del processo.

Un tempo sostanziale di caricamento del codice per una richiesta indica che si tratta della prima richiesta che esegue la parte non ottimizzata del metodo. Considerare un processo di riscaldamento che esegua tale parte del codice prima che gli utenti accedano a esso.

### <a id="httpclientsend"></a>Inviare una richiesta HTTP
Metodi come **HttpClient.Send** indicano che il codice è in attesa del completamento di una richiesta HTTP.

### <a id="sqlcommand"></a>Operazione di database
Metodi come **SqlCommand.Execute** indicano che il codice è in attesa del completamento di un'operazione di database.

### <a id="await"></a>Attesa (AWAIT\_TIME)
**AWAIT\_TIME** indica che il codice è in attesa del completamento di un'altra attività. Questo accade solitamente con l'istruzione C# **AWAIT**. Quando il codice esegue C# **AWAIT**, il thread viene rimosso e restituisce il controllo al pool di thread e non esiste un thread bloccato in attesa del completamento di **AWAIT**. Tuttavia, in modo logico il thread che ha eseguito **AWAIT** viene "bloccato" in attesa che venga completata l'operazione. L'istruzione **AWAIT\_TIME** indica il tempo di blocco in attesa del completamento dell'attività.

### <a id="block"></a>Tempo di blocco
**BLOCKED_TIME** indica che il codice è in attesa di un'altra risorsa disponibile. Ad esempio, potrebbe essere in attesa di un oggetto di sincronizzazione, di un thread o del completamento di una richiesta.

### <a id="cpu"></a>Tempo di CPU
La CPU è occupata nell'esecuzione di istruzioni.

### <a id="disk"></a>Tempo del disco
L'applicazione sta eseguendo operazioni su disco.

### <a id="network"></a>Tempo di rete
L'applicazione sta eseguendo operazioni sulla rete.

### <a id="when"></a>Colonna Quando
La colonna **Quando** è una visualizzazione della variazione nel tempo degli esempi INCLUSIVI raccolti per un nodo nel tempo. L'intervallo totale della richiesta è suddiviso in 32 intervalli di tempo. Gli esempi inclusivi di tale nodo vengono accumulati in questi 32 intervalli. Ogni intervallo è rappresentato come una barra. L'altezza della barra rappresenta un valore ridimensionato. Per i nodi contrassegnati con **CPU_TIME** o **BLOCKED_TIME** oppure in cui è presente una relazione ovvia di uso di una risorsa (CPU, disco, thread), la barra rappresenta l'uso di una di queste risorse per il periodo di tempo di questo intervallo. Per queste metriche è possibile ottenere un valore maggiore del 100% usando più risorse. Ad esempio, se in media si usano due CPU in un intervallo si ottiene il 200%.


## <a id="troubleshooting"></a>Risoluzione dei problemi

### <a name="too-many-active-profiling-sessions"></a>Troppe sessioni di profilatura attive

È attualmente possibile abilitare il profiler su un massimo di quattro app Web di Azure e di slot di distribuzione in esecuzione nello stesso piano di servizio. Se il processo Web del profiler segnala un numero eccessivo di sessioni di profilatura attive, spostare alcune app Web in un piano di servizio diverso.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Come è possibile verificare se il Application Insights Profiler è in esecuzione?

Il profiler viene eseguito come processo Web continuo nell'app Web. È possibile aprire la risorsa dell'app Web nel [portale di Azure](https://portal.azure.com). Nel riquadro **WebJobs** controllare lo stato di **ApplicationInsightsProfiler**. Se non è in esecuzione, aprire **Log** per altre informazioni.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Perché non è possibile trovare esempi di stack anche se il profiler è in esecuzione?

Ecco alcuni aspetti da controllare:

* Assicurarsi che il piano di servizio dell'app Web sia di livello Basic o superiore.
* Assicurarsi che nell'app Web sia abilitato Application Insights SDK 2.2 Beta o versioni successive.
* Assicurarsi che nell'app Web l'impostazione **APPINSIGHTS_INSTRUMENTATIONKEY** abbia la stessa chiave di strumentazione usata da Application Insights SDK.
* Assicurarsi che l'app Web sia in esecuzione su .NET Framework 4.6.
* Se l'app Web è un'applicazione ASP.NET Core, verificare le [dipendenze richieste](#aspnetcore).

Dopo aver avviato il profiler, sarà previsto un breve periodo di riscaldamento durante il quale il profiler raccoglierà attivamente diverse analisi di prestazioni. Successivamente, il profiler raccoglie le analisi di prestazioni per due minuti ogni ora.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Stavo usando il profiler del servizio Azure. Che cosa è successo?  

Quando si abilita Application Insights Profiler, l'agente del profiler di servizi Azure viene disabilitato.

### <a id="double-counting"></a>Doppio conteggio dei thread in parallelo

In alcuni casi la metrica del tempo totale nel visualizzatore dello stack supera la durata della richiesta.

Questa situazione può verificarsi quando sono presenti due o più thread associati a una richiesta, che operano in parallelo. In questo caso, il tempo totale di thread è superiore al tempo trascorso. Un thread può essere in attesa del completamento dell'altro. Il visualizzatore tenta di rilevare questa situazione e omette l'attesa non interessante, ma sbaglia mostrando l'eccesso anziché l'omissione di quelle che possono essere informazioni critiche.  

Quando sono presenti thread in parallelo nelle analisi, determinare quali thread sono in attesa in modo da poter determinare il percorso critico per la richiesta. Nella maggior parte dei casi, il thread che entra rapidamente in uno stato di attesa è semplicemente in attesa di altri thread. Concentrarsi sugli altri thread e ignorare il tempo nei thread in attesa.

### <a id="issue-loading-trace-in-viewer"></a>Senza dati di profilatura

Ecco alcuni aspetti da controllare:

* Se i dati che si sta tentando di visualizzare sono antecedenti a un paio di settimane, è possibile limitare il filtro temporale e riprovare.
* Verificare che un firewall o i proxy non abbiano bloccato l'accesso a https://gateway.azureserviceprofiler.net.
* Verificare che la chiave di strumentazione di Application Insights in uso nell'app sia la stessa della risorsa di Application Insights usata per abilitare la profilatura. La chiave è in genere in ApplicationInsights.config, ma potrebbe anche essere disponibile nei file web.config o app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Report di errori nel visualizzatore di profilatura

Inviare un ticket di supporto nel portale. Verificare di includere l'ID di correlazione dal messaggio di errore.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Errore di distribuzione: Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se si intende ridistribuire l'app Web in una risorsa di Servizio app con il profiler abilitato, potrebbe essere visualizzato un messaggio simile al seguente:

Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Questo errore si verifica se si esegue Distribuzione Web da script o dalla pipeline di distribuzione di Visual Studio Team Services. Per risolvere questo problema, aggiungere i parametri di distribuzione seguenti all'attività Distribuzione Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Questi parametri eliminano la cartella usata da Application Insights Profiler e sbloccano il processo di ridistribuzione. Non influiscono sull'istanza del profiler attualmente in esecuzione.


## <a name="manual-installation"></a>Installazione manuale

Quando si configura il profiler, vengono eseguiti aggiornamenti nelle impostazioni dell'app Web. È possibile applicare gli aggiornamenti manualmente, se l'ambiente lo richiede (ad esempio, se l'applicazione viene eseguita nell'ambiente di Servizio app per PowerApps).

1. Nel pannello di controllo dell'app Web aprire **Impostazioni**.
2. Impostare la **versione di .NET Framework** su **v4.6**.
3. Attivare **Always On**.
4. Aggiungere l'impostazione dell'app __APPINSIGHTS_INSTRUMENTATIONKEY__ e impostare il valore sulla stessa chiave di strumentazione usata dall'SDK.
5. Aprire **Strumenti avanzati**.
6. Selezionare **Vai** per aprire il sito Web Kudu.
7. Nel sito Web Kudu selezionare **Site extensions** (Estensioni del sito).
8. Installare __Application Insights__ dalla raccolta delle app Web di Azure.
9. Riavviare l'app Web .

## <a id="profileondemand"></a>Attivare manualmente profiler
Quando è stato sviluppato il profiler è aggiunto un'interfaccia della riga di comando in modo che sia possibile testare il profiler in servizi di app. Utilizzando questo stesso utenti interfaccia inoltre possibile personalizzare la modalità di avvio il profiler. A un livello elevato il profiler utilizza sistema Kudu del servizio App per gestire l'analisi in background. Quando si installa l'estensione Application Insights, creare un processo web continuo che ospita il profiler. Si utilizzerà la stessa tecnologia, per creare un nuovo processo web che è possibile personalizzare in base alle esigenze.

In questa sezione viene illustrato come:

1.  Creare un processo web che consente di avviare il profiler per due minuti con la stampa di un pulsante.
2.  Creare un processo web che è possibile pianificare il profiler per l'esecuzione.
3.  Impostare gli argomenti per il profiler.


### <a name="set-up"></a>Configurare
Primo opportuno acquisire familiarità con i dashboard del processo web. In impostazioni fare clic sulla scheda processi Web.

![Pannello processi Web](./media/app-insights-profiler/webjobs-blade.png)

Come è possibile visualizzare che questo dashboard Mostra tutti i processi web attualmente installati nel sito. È possibile visualizzare il processo web ApplicationInsightsProfiler2 che ha il compito di profiler in esecuzione. Si tratta di dove si finirà creare i nuovi processi web per manuale e pianificato profilatura.

Primo consente di ottenere i file binari è necessario.

1.  Innanzitutto, andare al sito kudu. Nella fase di sviluppo scheda strumenti fare clic sulla scheda "Strumenti avanzati" con il logo Kudu. Fare clic su "Go". Verrà visualizzata in un nuovo sito e accedere automaticamente.
2.  Successivamente è necessario scaricare i file binari del profiler. Passare a Esplora File mediante la Console di Debug -> CMD nella parte superiore della pagina.
3.  Fare clic sul sito -> wwwroot -> App_Data -> processi -> continua. Si dovrebbe essere una cartella "ApplicationInsightsProfiler2". Fare clic sull'icona a sinistra della cartella di download. Questo verrà scaricato un file "ApplicationInsightsProfiler2.zip".
4.  Si scaricherà tutti i file sarà necessario spostare in avanti. Consiglia di creare una directory vuota per spostare questo archivio di zip in prima di proseguire.

### <a name="setting-up-the-web-job-archive"></a>L'installazione dell'archivio del processo web
Quando si aggiunge un nuovo processo web al sito Web di azure sostanzialmente si crea un archivio zip con un run.cmd all'interno. Il run.cmd indica al sistema di processo web operazioni da eseguire quando si esegue il processo web. Sono disponibili altre opzioni che è possibile leggere la documentazione di processo web, ma ai fini di non è necessario altro.

1.  Per iniziare creare una nuova cartella, denominato miei "RunProfiler2Minutes".
2.  Copiare i file dalla cartella ApplicationInsightProfiler2 estratta nella nuova cartella.
3.  Creare un nuovo file run.cmd. (Aperta la cartella di lavoro nel codice di Visual Studio prima di avviare per praticità)
4.  Aggiungere il comando `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`e salvare il file.
a.  Il `start` comando indica al profiler di avvio.
b.  `--engine-mode immediate`indica il profiler che si desidera avviare immediatamente la profilatura.
c.  `--single`mezzo per eseguire e quindi arrestare automaticamente d.  `--immediate-profiling-duration 120`significa che il profiler eseguito per 120 secondi o 2 minuti.
5.  Salvare il file.
6.  Questa cartella di archiviazione, è possibile fare clic con il pulsante destro la cartella e scegliere Invia a -> Compressed cartella compressa. Si creerà un file con estensione zip utilizzando il nome della cartella.

![avviare il comando del profiler](./media/app-insights-profiler/start-profiler-command.png)

È ora disponibile un file ZIP processo web che è possibile utilizzare per configurare i processi web nel sito.

### <a name="add-a-new-web-job"></a>Aggiungere un nuovo processo web
Successivamente sarà necessario aggiungere un nuovo processo web nel sito. In questo esempio mostra come aggiungere un processo manuale web attivati. Dopo che è possibile eseguire questa operazione il processo è quasi esattamente lo stesso per pianificato. È possibile leggere informazioni sui trigger i processi pianificati nel proprio.

1.  Andare nel dashboard di processi web.
2.  Fare clic sul comando Aggiungi dalla barra degli strumenti.
3.  Assegnare un nome di processo web si è scelto per corrispondere al nome del mio archivio per maggiore chiarezza e per aprirlo fino a diverse versioni di run.cmd.
4.  Nel file di caricare una parte del modulo, fare clic sull'icona di file aperti e trovare il file con estensione zip apportate in precedenza.
5.  Per il tipo, scegliere avviata.
6.  Per i trigger scegliere manuale.
7.  Fare clic su OK per salvare.

![avviare il comando del profiler](./media/app-insights-profiler/create-webjob.png)

### <a name="run-the-profiler"></a>Eseguire il profiler

Dopo aver creato un nuovo processo web che è possibile attivare manualmente è possibile provare a eseguirlo.

1.  Per impostazione predefinita è possibile avere solo un processo ApplicationInsightsProfiler.exe in esecuzione in un computer in qualsiasi momento. Per iniziare con assicurarsi di disabilitare il processo web continuo da questo dashboard. Fare clic sulla riga e premere "Stop". Aggiorna sulla barra degli strumenti e verificare che lo stato di conferma che il processo viene interrotto.
2.  Fare clic sulla riga con il nuovo processo web aggiunti e premere Esegui.
3.  Con la riga ancora selezionata fare clic sul comando registri nella barra degli strumenti, verrà visualizzato è a un dashboard di processi web per il processo web che è stato avviato. Verranno visualizzate le esecuzioni più recenti e i relativi risultati.
4.  Fare clic sull'esecuzione di nuovi.
5.  Se l'assenza di errori visualizzati alcuni log di diagnostica provenienti dal profiler conferma che è stato avviato il profiling di.

### <a name="things-to-consider"></a>Aspetti da considerare

Se questo metodo è relativamente semplice, esistono alcuni aspetti da considerare.

1.  Perché non è gestita dal servizio si avranno alcuna possibilità di aggiornare i file binari dell'agente per il processo web. Non attualmente abbiamo una pagina di download stabile per i file binari è l'unico modo per ottenere la versione più recente aggiornando l'estensione e selezionandola dalla cartella continua come abbiamo fatto in precedenza.
2.  Come si utilizzano gli argomenti della riga di comando che erano originariamente concepiti con sviluppatore usare anziché utilizzare per l'utente finale, questi argomenti modifica in futuro, pertanto solo possibile tenere presente che quando l'aggiornamento. Non deve essere gran parte di un problema in quanto è possibile aggiungere un processo web, l'esecuzione e test del corretto funzionamento. Infine, verrà creata l'interfaccia utente per eseguire questa operazione senza il processo manuale, ma è opportuno prendere in considerazione.
3.  La funzionalità processi Web per i servizi di App è univoca in quanto durante l'esecuzione del processo web assicura che il processo presenta le stesse variabili di ambiente e le impostazioni di app che finirà con il sito web. Ciò significa che non è necessario passare la chiave di strumentazione tramite la riga di comando per il profiler, deve solo selezionare la chiave di strumentazione dall'ambiente di. Tuttavia se si desidera eseguire il profiler, nella casella di sviluppo o in un computer all'esterno di servizi App è necessario specificare una chiave di strumentazione. È possibile farlo tramite il passaggio di un argomento `--ikey <instrumentation-key>`. Si noti che questo valore deve corrispondere alla chiave di strumentazione utilizzato dall'applicazione. Nell'output del log dal profiler indicherà quali ikey introduttiva il profiler e se è stata rilevata attività da tale chiave di strumentazione durante è sottoposto a profilatura.
4.  I processi web manualmente trigger possono essere attivati effettivamente tramite Web Hook. È possibile ottenere questo url da facendo clic sul processo web dal dashboard e visualizzare le proprietà o scegliendo proprietà nella barra degli strumenti dopo aver selezionato il processo web dalla tabella. Sono disponibili numerosi articoli disponibili online su questo, in modo non entra in dettagli su di esso, ma verrà visualizzata la possibilità di attivare il profiler tramite la pipeline CI/CD (ad esempio VSTS) o simile a Microsoft Flow (https://flow.microsoft.com/en-us/). A seconda di come decorativo che si desidera rendere il run.cmd, che può essere un run.ps1, i valori possibili sono numerose.  




## <a id="aspnetcore"></a>Supporto di ASP.NET Core

Un'applicazione ASP.NET Core deve installare il pacchetto NuGet Microsoft.ApplicationInsights.AspNetCore 2.1.0-beta6 o versioni successive per poter interagire con il profiler. A partire da 27 giugno 2017 non sono supportate le versioni precedenti.

## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
