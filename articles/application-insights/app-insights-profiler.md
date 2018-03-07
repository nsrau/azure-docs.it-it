---
title: Profilare le app Web attive in Azure con Application Insights Profiler | Microsoft Docs
description: Identificare il percorso critico nel codice del server Web con un profiler con footprint ridotto.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: c65ef9141898369b8fcadd4c52972b767aca7cfe
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilare le app Web di Azure attive con Application Insights

*Questo strumento di Azure Application Insights è disponibile a livello generale per la funzionalità App Web del servizio app di Azure e in anteprima per le risorse di calcolo di Azure.*

Questo articolo illustra il tempo impiegato in ogni metodo dell'applicazione Web attiva quando si usa [Application Insights](app-insights-overview.md). Lo strumento Application Insights Profiler visualizza i profili dettagliati delle richieste attive che sono state soddisfatte dall'app ed evidenzia il *percorso critico* che impiega più tempo. Le richieste con diversi tempi di risposta vengono profilate in base a un campionamento. Adottando varie tecniche, è possibile ridurre al minimo il sovraccarico associato all'applicazione.

Lo strumento Profiler è attualmente applicabile alle app Web ASP.NET e ASP.NET Core in esecuzione su App Web. Per usare Profiler è necessario il livello di servizio Basic o superiore.

## <a id="installation"></a> Abilitare Profiler per un'app Web in esecuzione su App Web
Se si è già pubblicata l'applicazione come app Web, ma nel codice sorgente non si è ancora configurato l'uso di Application Insights, eseguire queste operazioni:
1. Passare al riquadro **Servizi app** nel portale di Azure.
2. In **Monitoraggio** selezionare **Application Insights** e quindi seguire le istruzioni visualizzate nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app Web.

   ![Abilitare Application Insights nel portale dei servizi app][appinsights-in-appservices]

3. Se si ha accesso al codice sorgente del progetto, [installare Application Insights](app-insights-asp-net.md).  
   Se è già installato, assicurarsi che la versione in uso sia la più recente. Per controllare la versione più recente, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet** > **Aggiornamenti** > **Aggiorna tutti i pacchetti**. Distribuire quindi l'app.

Per poter interagire con Profiler, le applicazioni ASP.NET Core richiedono l'installazione del pacchetto NuGet Microsoft.ApplicationInsights.AspNetCore 2.1.0-beta6 o versioni successive. A partire dal 27 giugno 2017 le versioni precedenti non sono supportate.

1. Nel [portale di Azure](https://portal.azure.com) aprire la risorsa Application Insights per l'app Web. 
2. Selezionare **Prestazioni** > **Abilita Application Insights Profiler**.

   ![Selezionare il banner Abilita profiler][enable-profiler-banner]

3. In alternativa è possibile selezionare la configurazione di **Profiler** per visualizzare lo stato e abilitare o disabilitare Profiler.

   ![Selezionare la configurazione di Profiler][performance-blade]

   Le app Web configurate con Application Insights sono elencate nel riquadro di configurazione **Profiler**. Se si sono eseguiti i passaggi precedenti, l'agente Profiler dovrebbe essere installato. 

4. Nel riquadro di configurazione **Profiler** selezionare **Abilita profiler**.

5. Se necessario, seguire le istruzioni per installare l'agente Profiler. Se nessuna app Web è stata ancora configurata con Application Insights, selezionare **Aggiungi app collegate**.

   ![Opzioni del riquadro di configurazione][linked app services]

A differenza delle app Web ospitate in base ai piani di App Web, le applicazioni ospitate nelle risorse di calcolo di Azure (ad esempio, Macchine virtuali di Azure, set di scalabilità di macchine virtuali, Azure Service Fabric o Servizi cloud di Azure) non sono gestite direttamente da Azure. In questo caso non sono disponibili app Web a cui collegarsi. Anziché eseguire il collegamento a un'app, selezionare il pulsante **Abilita profiler**.

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Abilitare Profiler per le risorse di calcolo di Azure (anteprima)

Per altre informazioni, vedere la [versione di anteprima di Profiler per le risorse di calcolo di Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Visualizzare i dati del profiler

Verificare che l'applicazione riceva traffico. Se si sta eseguendo un esperimento, è possibile generare le richieste da indirizzare all'app Web usando il [test delle prestazioni di Application Insights](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test). Se Profiler è stato appena abilitato, è possibile eseguire per circa 15 minuti un breve test di carico, che genererà le analisi del profiler. Se invece Profiler è stato abilitato già da qualche tempo, tenere presente che viene eseguito in modo casuale due volte all'ora e che ogni esecuzione dura due minuti. È consigliabile eseguire prima il test di carico per un'ora per ottenere analisi di esempio del profiler.

Dopo che l'applicazione ha iniziato a ricevere traffico, passare al riquadro **Prestazioni**, selezionare **Take Actions** (Esegui azioni) per visualizzare le analisi del profiler e quindi fare clic sul pulsante **Analisi Profiler**.

![Anteprima delle analisi di Profiler nel riquadro delle prestazioni di Application Insights][performance-blade-v2-examples]

Selezionare un esempio per visualizzare i dettagli a livello di codice del tempo impiegato per l'esecuzione della richiesta.

![Finestra di esplorazione delle analisi di Application Insights][trace-explorer]

La finestra di esplorazione delle analisi visualizza le informazioni seguenti:

* **Mostra percorso critico**: apre il nodo foglia di dimensioni maggiori o almeno un elemento simile. Nella maggior parte dei casi questo nodo è adiacente a un collo di bottiglia delle prestazioni.
* **Etichetta**: nome della funzione o dell'evento. La struttura mostra una combinazione di codice e di eventi che si sono verificati, ad esempio eventi SQL e HTTP. L'evento principale rappresenta la durata complessiva della richiesta.
* **Tempo trascorso**: intervallo di tempo tra l'inizio e la fine dell'operazione.
* **Quando**: tempo in cui la funzione o l'evento è stato eseguito in relazione ad altre funzioni.

## <a name="how-to-read-performance-data"></a>Come leggere i dati sulle prestazioni

Il profiler di servizi Microsoft usa una combinazione della strumentazione e dei metodi di campionamento per analizzare le prestazioni dell'applicazione. Quando la raccolta dettagliata è in corso, il profiler di servizi campiona il puntatore all'istruzione di ogni CPU di computer ogni millisecondo. Ogni esempio acquisisce lo stack di chiamate completo del thread attualmente in esecuzione. Fornisce informazioni dettagliate sull'attività del thread a livello sia generale che specifico di astrazione. Il profiler di servizi raccoglie anche altri eventi per tenere traccia della causalità e della correlazione delle attività, ad esempio gli eventi di commutazione di contesto, Task Parallel Library (TPL) e del pool di thread.

Lo stack di chiamate riportato nella visualizzazione della sequenza temporale è il risultato del campionamento e della strumentazione. Poiché ogni esempio acquisisce lo stack di chiamate completo del thread, include il codice di Microsoft .NET Framework e di eventuali altri framework a cui si fa riferimento.

### <a id="jitnewobj"></a>Allocazione di oggetti (clr!JIT\_New o clr!JIT\_Newarr1)
**clr!JIT\_New** e **clr!JIT\_Newarr1** sono funzioni di supporto in .NET Framework che allocano la memoria da un heap gestito. **clr!JIT\_New** viene richiamato quando si alloca un oggetto. **clr!JIT\_Newarr1** viene richiamato quando si alloca una matrice di oggetti. Queste due funzioni sono in genere veloci e richiedono un intervallo temporale relativamente ridotto. Se **clr!JIT\_New** o **clr!JIT\_Newarr1** impiega molto tempo nella sequenza temporale, è possibile che il codice stia allocando numerosi oggetti e utilizzando una notevole quantità di memoria.

### <a id="theprestub"></a>Caricamento di codice (clr!ThePreStub)
**clr!ThePreStub** è una funzione di supporto all'interno di .NET Framework che prepara il codice da eseguire per la prima volta. Ad esempio, include in genere la compilazione JIT. Per ogni metodo C#, **clr!ThePreStub** deve essere richiamato al massimo una volta nel corso della durata di un processo.

Se **clr!ThePreStub** richiede una notevole quantità di tempo per una richiesta, indica che la richiesta è la prima a eseguire questo metodo. Il tempo di caricamento del primo metodo da parte del runtime di .NET Framework è significativo. È possibile valutare un processo di riscaldamento che esegua tale parte del codice prima che gli utenti accedano a esso oppure l'esecuzione del generatore di immagini native (ngen.exe) negli assembly.

### <a id="lockcontention"></a>Conflitto di blocchi (clr!JITutil\_MonContention o clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** o **clr!JITutil\_MonEnterWorker** indica che il thread corrente è in attesa di un blocco da rilasciare. Questo testo viene in genere visualizzato durante l'esecuzione di un'istruzione C# **LOCK**, quando viene richiamato il metodo **Monitor.Enter** o un metodo con l'attributo **MethodImplOptions.Synchronized**. Il conflitto di blocchi si verifica in genere quando il thread _A_ acquisisce un blocco e il thread _B_ prova ad acquisire lo stesso blocco prima che il thread _A_ lo rilasci.

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

## <a name="overhead-and-sampling-algorithm"></a>Sovraccarico e algoritmo di campionamento

Profiler viene eseguito in modo casuale per due minuti ogni ora in ogni macchina virtuale che ospita l'applicazione con Profiler abilitato per acquisire le analisi. Quando è in esecuzione, Profiler comporta un sovraccarico della CPU del server compreso tra il 5% e il 15%.

Maggiore è il numero di server disponibili per l'hosting dell'applicazione, minore è l'impatto di Profiler sulle prestazioni complessive dell'applicazione. Ciò dipende dall'algoritmo di campionamento, in base al quale Profiler viene eseguito in qualsiasi momento solo nel 5% dei server. Sono disponibili più server per elaborare le richieste Web e compensare il sovraccarico del server causato dall'esecuzione di Profiler.

## <a name="disable-profiler"></a>Disabilitare Profiler
Per arrestare o riavviare Profiler per una singola istanza di App Web, in **Processi Web** accedere alla risorsa di App Web. Per eliminare Profiler, passare a **Estensioni**.

![Disabilitare Profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare Profiler su tutte le app Web per individuare il prima possibile eventuali problemi di prestazioni.

Se si usa Distribuzione Web per distribuire le modifiche all'applicazione Web, assicurarsi di escludere la cartella App_Data dall'eliminazione durante la distribuzione. In caso contrario, i file dell'estensione Profiler verranno eliminati alla successiva distribuzione dell'applicazione Web in Azure.


## <a id="troubleshooting"></a>Risoluzione dei problemi

### <a name="too-many-active-profiling-sessions"></a>Troppe sessioni di profilatura attive

È attualmente possibile abilitare Profiler su un massimo di quattro app Web e slot di distribuzione di Azure in esecuzione nello stesso piano di servizio. Se il processo Web di Profiler segnala un numero eccessivo di sessioni di profilatura attive, spostare alcune app Web in un altro piano di servizio.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Come è possibile verificare se il Application Insights Profiler è in esecuzione?

Profiler viene eseguito come processo Web continuo nell'app Web. È possibile aprire la risorsa dell'app Web nel [portale di Azure](https://portal.azure.com). Nel riquadro **WebJobs** controllare lo stato di **ApplicationInsightsProfiler**. Se non è in esecuzione, aprire **Log** per altre informazioni.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Perché non è possibile trovare esempi di stack anche se Profiler è in esecuzione?

Ecco alcuni aspetti da controllare:

* Verificare che il piano di servizio dell'app Web sia di livello Basic o superiore.
* Assicurarsi che nell'app Web sia abilitato Application Insights SDK 2.2 Beta o versioni successive.
* Assicurarsi che nell'app Web l'impostazione **APPINSIGHTS_INSTRUMENTATIONKEY** abbia la stessa chiave di strumentazione usata da Application Insights SDK.
* Assicurarsi che l'app Web sia in esecuzione su .NET Framework 4.6.
* Se l'app Web è un'applicazione ASP.NET Core, verificare le [dipendenze richieste](#aspnetcore).

Dopo l'avvio di Profiler, è previsto un breve periodo di riscaldamento durante il quale Profiler raccoglie attivamente diverse analisi delle prestazioni. Al termine di questo periodo, Profiler raccoglie le analisi delle prestazioni per due minuti ogni ora.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Prima era attivo il profiler di servizi Azure. Che cosa è successo?

Quando si abilita Application Insights Profiler, l'agente del profiler di servizi Azure viene disabilitato.

### <a id="double-counting"></a>Doppio conteggio dei thread in parallelo

In alcuni casi la metrica del tempo totale nel visualizzatore dello stack supera la durata della richiesta.

Questa situazione può verificarsi quando due o più thread sono associati a una richiesta e operano in parallelo. In questo caso, il tempo totale di thread è superiore al tempo trascorso. Un thread può essere in attesa del completamento dell'altro. Il visualizzatore prova a rilevare questa situazione e omette l'attesa non interessante, ma sbaglia mostrando troppe informazioni anziché omettere le informazioni potenzialmente critiche.

Quando sono presenti thread in parallelo nelle analisi, determinare quali thread sono in attesa per poter verificare il percorso critico per la richiesta. Nella maggior parte dei casi, il thread che entra rapidamente in uno stato di attesa è semplicemente in attesa di altri thread. Concentrarsi sugli altri thread e ignorare il tempo nei thread in attesa.

### <a id="issue-loading-trace-in-viewer"></a>Senza dati di profilatura

Ecco alcuni aspetti da controllare:

* Se i dati che si sta tentando di visualizzare sono antecedenti a un paio di settimane, è possibile limitare il filtro temporale e riprovare.
* Verificare che un firewall o i proxy non abbiano bloccato l'accesso a https://gateway.azureserviceprofiler.net.
* Verificare che la chiave di strumentazione di Application Insights in uso nell'app sia la stessa della risorsa di Application Insights usata per abilitare la profilatura. La chiave si trova in genere nel file ApplicationInsights.config, ma può anche essere definita nel file web.config o app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Report di errori nel visualizzatore di profilatura

Inviare un ticket di supporto nel portale. Verificare di includere l'ID di correlazione dal messaggio di errore.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Errore di distribuzione: Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se si intende ridistribuire l'app Web in una risorsa di App Web con Profiler abilitato, è possibile che venga visualizzato un messaggio simile al seguente:

*Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

Questo errore si verifica se si esegue Distribuzione Web da script o dalla pipeline di distribuzione di Visual Studio Team Services. Per risolvere questo problema, aggiungere i parametri di distribuzione seguenti all'attività Distribuzione Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Questi parametri eliminano la cartella usata da Application Insights Profiler e sbloccano il processo di ridistribuzione. Non influiscono sull'istanza di Profiler attualmente in esecuzione.


## <a name="manual-installation"></a>Installazione manuale

Quando si configura Profiler, vengono apportati alcuni aggiornamenti alle impostazioni dell'app Web. È possibile applicare gli aggiornamenti manualmente, se l'ambiente lo richiede. Ad esempio nel caso in cui l'applicazione sia in esecuzione in un ambiente di App Web per PowerApps.

1. Nel riquadro **Web App Control** (Controllo app Web) aprire **Impostazioni**.
2. Impostare la **versione di .NET Framework** su **v4.6**.
3. Attivare **Always On**.
4. Aggiungere l'impostazione dell'app **APPINSIGHTS_INSTRUMENTATIONKEY** e impostare il valore sulla stessa chiave di strumentazione usata dall'SDK.
5. Aprire **Strumenti avanzati**.
6. Selezionare **Vai** per aprire il sito Web Kudu.
7. Nel sito Web Kudu selezionare **Site extensions** (Estensioni del sito).
8. Installare **Application Insights** dalla raccolta delle app Web di Azure.
9. Riavviare l'app Web.

## <a id="profileondemand"></a> Attivare Profiler manualmente
Durante lo sviluppo di Profiler è stata aggiunta un'interfaccia della riga di comando per testare Profiler sui servizi app. Usando questa interfaccia, gli utenti possono anche personalizzare la modalità di avvio di Profiler. A livello generale, Profiler usa il sistema Kudu di App Web per gestire la profilatura in background. Quando si installa l'estensione Application Insights, viene creato un processo Web continuo che ospita Profiler. Questa stessa tecnologia viene usata per creare un nuovo processo Web che può essere personalizzato in base alle esigenze.

Questa sezione illustra come eseguire queste operazioni:

* Creare un processo Web che consente di avviare Profiler per due minuti con un semplice clic.
* Creare un processo Web che consente di pianificare l'esecuzione di Profiler.
* Impostare gli argomenti per Profiler.


### <a name="set-up"></a>Configurare
Prima di tutto è opportuno acquisire familiarità con il dashboard del processo Web. In **Impostazioni** fare clic sulla scheda **Processi Web**.

![Pannello Processi Web](./media/app-insights-profiler/webjobs-blade.png)

Come si può notare, questo dashboard visualizza tutti i processi Web attualmente installati nel sito. È possibile vedere il processo Web ApplicationInsightsProfiler2 su cui è in esecuzione il processo di Profiler. È qui che si creano nuovi processi Web per la profilatura manuale e pianificata.

Per ottenere i file binari necessari, eseguire queste operazioni:

1.  Sul sito Kudu, nella scheda **Development tools** (Strumenti di sviluppo), selezionare la scheda **Advanced Tools** (Strumenti avanzati) con il logo Kudu e quindi scegliere **Go** (Vai).  
   Verrà aperto un nuovo sito a cui si accede automaticamente.
2.  Per scaricare i file binari di Profiler, accedere a Esplora file tramite **Console di debug** > **CMD** nella parte superiore della pagina.
3.  Selezionare **Site** > **wwwroot** > **App_Data** > **Jobs** > **Continuous**.  
   Verrà visualizzata una cartella denominata *ApplicationInsightsProfiler2*. 
4. A sinistra della cartella fare clic sull'icona **Download**.  
   Verrà scaricato il file *ApplicationInsightsProfiler2.zip*. È consigliabile creare una directory vuota in cui spostare questo archivio con estensione zip.

### <a name="setting-up-the-web-job-archive"></a>Configurazione di un archivio per processi Web
Quando si aggiunge un nuovo processo Web nel sito Web di Azure, sostanzialmente si crea un archivio con estensione zip che contiene un file *run.cmd*. Il file *run.cmd* indica le operazioni da svolgere durante l'esecuzione del processo Web.

1.  Creare una nuova cartella, ad esempio *RunProfiler2Minutes*.
2.  Copiare i file dalla cartella *ApplicationInsightProfiler2* estratta alla nuova cartella.
3.  Creare un nuovo file *run.cmd*.  
    Per praticità, è possibile aprire la cartella di lavoro in Visual Studio Code prima di iniziare.
4.  Nel file aggiungere il comando `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`. Di seguito sono riportate le descrizioni dei comandi:

    * `start`: avvia Profiler.  
    * `--engine-mode immediate`: indica a Profiler di iniziare subito la profilatura.  
    * `--single` indica a Profiler di eseguire la profilatura e quindi arrestarsi automaticamente.  
    * `--immediate-profiling-duration 120`: indica a Profiler di eseguire la profilatura per 120 secondi o 2 minuti.

5.  Salvare le modifiche.
6.  Archiviare la cartella facendo clic con il pulsante destro del mouse e quindi scegliendo **Invia a** > **Cartella compressa**.  
   Verrà creato un file con l'estensione zip e il nome della cartella.

![Comando di avvio di Profiler](./media/app-insights-profiler/start-profiler-command.png)

A questo punto si è creato un file con estensione zip che è possibile usare per configurare i processi Web nel sito.

### <a name="add-a-new-web-job"></a>Aggiungere un nuovo processo Web
In questa sezione si aggiunge un nuovo processo Web nel sito. L'esempio seguente mostra come aggiungere un processo Web attivato manualmente. La procedura per eseguire questa operazione è quasi identica a quella relativa a un processo Web pianificato.

1.  Passare al dashboard **Processi Web**.
2.  Sulla barra degli strumenti fare clic su **Aggiungi**.
3.  Assegnare un nome al processo Web.  
    Per maggiore chiarezza, può essere utile usare lo stesso nome dell'archivio ed estenderlo per diverse versioni del file *run.cmd*.
4.  Nell'area **Caricamento file** del modulo selezionare l'icona **Apri file** e quindi cercare il file con estensione zip creato nella sezione precedente.

    a.  Nella casella **Tipo** selezionare **Attivato**.  
    b.  Nella casella **Trigger** selezionare **Manuale**.

5.  Selezionare **OK**.

![Comando di avvio di Profiler](./media/app-insights-profiler/create-webjob.png)

### <a name="run-profiler"></a>Eseguire Profiler

Ora che si è creato un nuovo processo Web attivabile manualmente, è possibile provare a eseguirlo seguendo le istruzioni riportate in questa sezione.

Per impostazione predefinita, è possibile eseguire un solo processo *ApplicationInsightsProfiler.exe* alla volta su un computer in qualsiasi momento. Per iniziare, assicurarsi quindi di disabilitare il processo Web *Continuous* da questo dashboard. 
1. Selezionare la riga con il nuovo processo Web e quindi scegliere **Arresta**. 
2. Sulla barra degli strumenti fare clic su **Aggiorna** e verificare che il processo risulti arrestato.
3. Selezionare la riga con il nuovo processo Web e quindi scegliere **Esegui**.
4. Con la riga ancora selezionata, sulla barra degli strumenti fare clic sul comando **Log**.  
    Questa operazione consente di aprire un dashboard per il nuovo processo Web ed elenca le esecuzioni più recenti e i relativi risultati.
5. Selezionare l'istanza dell'esecuzione appena avviata.  
    Se il nuovo processo Web è stato avviato correttamente, è possibile visualizzare alcuni log di diagnostica provenienti da Profiler che confermano l'avvio della profilatura.

### <a name="things-to-consider"></a>Aspetti da considerare

Questo metodo è relativamente semplice, ma è opportuno considerare gli aspetti seguenti:

* Poiché il processo Web non è gestito dal servizio Microsoft, non è possibile aggiornare i file binari dell'agente per il processo Web. Non è attualmente disponibile una pagina di download stabile per i file binari, quindi l'unico modo per ottenere la versione più recente di tali file è aggiornare l'estensione e recuperarla dalla cartella *Continuous*, come nei passaggi precedenti.

* Poiché questo processo prevede l'utilizzo di argomenti della riga di comando originariamente concepiti per gli sviluppatori anziché per gli utenti finali, gli argomenti potrebbero cambiare in futuro. Prestare attenzione alle possibili modifiche quando si esegue l'aggiornamento. Questo non dovrebbe comunque causare particolari problemi perché è possibile aggiungere un processo Web, eseguirlo e testarlo per verificare che funzioni. In futuro verrà sviluppata un'interfaccia utente per gestire questa operazione evitando la procedura manuale.

* Processi Web di App Web è un'ottima funzionalità. Quando esegue il processo Web, verifica che il processo abbia le stesse variabili di ambiente e le stesse impostazioni di app previste per il sito Web. Non è quindi necessario passare la chiave di strumentazione a Profiler tramite la riga di comando. Profiler dovrebbe selezionare automaticamente la chiave di strumentazione dall'ambiente. Se tuttavia si vuole eseguire Profiler nella casella di sviluppo o in un computer all'esterno di App Web, è necessario specificare una chiave di strumentazione. È possibile eseguire questa operazione passando un argomento, `--ikey <instrumentation-key>`. Questo valore deve corrispondere alla chiave di strumentazione usata dall'applicazione. Nell'output del log di Profiler è indicata la chiave di strumentazione iniziale di Profiler ed è specificato se sono state rilevate attività da tale chiave di strumentazione durante la profilatura.

* I processi Web con attivazione manuale possono essere attivati tramite webhook. È possibile ottenere questo URL facendo clic con il pulsante destro del mouse sul processo Web nel dashboard e visualizzando le proprietà. In alternativa, è possibile selezionare **Proprietà** sulla barra degli strumenti dopo aver selezionato il processo Web nella tabella. Questo approccio offre infinite possibilità, dall'attivazione di Profiler dalla pipeline di integrazione continua/recapito continuo (come VSTS) all'uso di un servizio come Microsoft Flow (https://flow.microsoft.com/it-it/). In ultima analisi, la scelta dipende dal livello di complessità desiderato per il file *run.cmd* (che può essere anche un file *run.ps1*), ma la flessibilità è garantita.

## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
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
