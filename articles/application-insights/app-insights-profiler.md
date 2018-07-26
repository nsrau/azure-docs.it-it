---
title: Profilare le app Web attive in Azure con Application Insights Profiler | Microsoft Docs
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
ms.date: 07/13/2018
ms.author: mbullwin
ms.openlocfilehash: e4712b94be94eb6d4cf363fc120b72c74f29f0a2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057923"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilare le app Web di Azure attive con Application Insights

Questa funzionalità di Azure Application Insights è disponibile a livello generale per la funzionalità App Web di Servizio app di Azure e in anteprima per le risorse di calcolo di Azure. Sono disponibili informazioni [sull'uso locale di Profiler](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).

Questo articolo illustra il tempo impiegato in ogni metodo dell'applicazione Web attiva quando si usa [Application Insights](app-insights-overview.md). Lo strumento Application Insights Profiler visualizza i profili dettagliati delle richieste attive che sono state soddisfatte dall'app ed evidenzia il *percorso critico* che impiega più tempo. Le richieste con diversi tempi di risposta vengono profilate in base a un campionamento. Adottando varie tecniche, è possibile ridurre al minimo il sovraccarico associato all'applicazione.

Lo strumento Profiler è attualmente applicabile alle app Web ASP.NET e ASP.NET Core in esecuzione su App Web. Per usare Profiler è necessario il livello di servizio Basic o superiore.

## <a id="installation"></a> Abilitare Profiler per App Web

Dopo aver distribuito un'app Web, indipendentemente dal fatto che sia stato incluso Application Insights SDK nel codice sorgente, eseguire queste operazioni:

1. Passare al riquadro **Servizi app** nel portale di Azure.
2. Passare al riquadro **Impostazioni > Monitoraggio**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/app-insights-profiler/AppInsights-AppServices.png)

3. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app Web. Accettare tutte le opzioni predefinite. L'opzione **Diagnostica a livello di codice** è attivata per impostazione predefinita e consente di abilitare Profiler.

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

4. Profiler è ora installato con l'estensione del sito Application Insights e viene abilitato tramite un'impostazione di app di Servizi app.

    ![Impostazione di app per Profiler][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Abilitare Profiler per le risorse di calcolo di Azure (anteprima)

Per altre informazioni, vedere la [versione di anteprima di Profiler per le risorse di calcolo di Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Visualizzare i dati del profiler

Verificare che l'applicazione riceva traffico. Se si sta eseguendo un esperimento, è possibile generare le richieste da indirizzare all'app Web usando il [test delle prestazioni di Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Se Profiler è stato appena abilitato, è possibile eseguire per circa 15 minuti un breve test di carico, che genererà le analisi del profiler. Se invece Profiler è stato abilitato già da qualche tempo, tenere presente che viene eseguito in modo casuale due volte all'ora e che ogni esecuzione dura due minuti. È consigliabile eseguire prima il test di carico per un'ora per ottenere tracce di esempio del profiler.

Dopo che l'applicazione ha iniziato a ricevere traffico, passare al riquadro **Prestazioni**, selezionare **Take Actions** (Esegui azioni) per visualizzare le analisi del profiler e quindi fare clic sul pulsante **Analisi Profiler**.

![Anteprima delle analisi di Profiler nel riquadro delle prestazioni di Application Insights][performance-blade-v2-examples]

Selezionare un esempio per visualizzare i dettagli a livello di codice del tempo impiegato per l'esecuzione della richiesta.

![Explorer di analisi Application Insights][trace-explorer]

La finestra di esplorazione delle analisi visualizza le informazioni seguenti:

* **Mostra percorso critico**: apre il nodo foglia di dimensioni maggiori o almeno un elemento simile. Nella maggior parte dei casi questo nodo è adiacente a un collo di bottiglia delle prestazioni.
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

Se **clr!ThePreStub** richiede una notevole quantità di tempo per una richiesta, indica che la richiesta è la prima a eseguire questo metodo. Il tempo di caricamento del primo metodo da parte del runtime di .NET Framework è significativo. È possibile valutare un processo di riscaldamento che esegua tale parte del codice prima che gli utenti accedano a esso oppure l'esecuzione del generatore di immagini native (ngen.exe) negli assembly.

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

Quando si configura Profiler, vengono apportati alcuni aggiornamenti alle impostazioni dell'app Web. È possibile applicare gli aggiornamenti manualmente, se l'ambiente lo richiede Ad esempio nel caso in cui l'applicazione sia in esecuzione in un ambiente di App Web per PowerApps.

1. Nel riquadro **Web App Control** (Controllo app Web) aprire **Impostazioni**.
2. Impostare la **versione di .NET Framework** su **v4.6**.
3. Attivare **Always On**.
4. Aggiungere l'impostazione dell'app **APPINSIGHTS_INSTRUMENTATIONKEY** e impostare il valore sulla stessa chiave di strumentazione usata dall'SDK.
5. Aprire **Strumenti avanzati**.
6. Selezionare **Vai** per aprire il sito Web Kudu.
7. Nel sito Web Kudu selezionare **Site extensions** (Estensioni del sito).
8. Installare **Application Insights** dalla raccolta delle app Web di Azure.
9. Riavviare l'app Web .

## <a id="profileondemand"></a> Attivare Profiler manualmente

Profiler può essere attivato manualmente con un solo clic. Si supponga di eseguire un test delle prestazioni Web. Sono necessarie tracce che consentono di comprendere le prestazioni dell'app Web in condizioni di carico. Il controllo sul momento in cui le tracce vengono acquisite è fondamentale poiché l'utente conosce il momento in cui il test di carico viene eseguito, ma l'intervallo di campionamento casuale potrebbe non rilevarlo.
I passaggi seguenti illustrano il funzionamento di questo scenario.

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Facoltativo) Passaggio 1: Generare il traffico verso l'app Web avviando un test delle prestazioni Web

Se l'app Web include già il traffico in ingresso o se si desidera solo generare manualmente il traffico, ignorare questa sezione e andare al passaggio 2.

Passare al portale di Application Insights, **Configura > Test delle prestazioni**. Fare clic sul pulsante Nuovo per avviare un nuovo test delle prestazioni.
![Creare un nuovo test delle prestazioni][create-performance-test]

Nel riquadro **Nuovo test delle prestazioni** configurare l'URL di destinazione del test. Accettare tutte le impostazioni predefinite e avviare l'esecuzione del test di carico.

![Configurare il test di carico][configure-performance-test]

Si noti che inizialmente il nuovo test viene inserito in una coda e in seguito viene visualizzato con stato in corso.

![Test di carico inviato e accodato][load-test-queued]

![Test di carico in esecuzione e in corso][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Passaggio 2: Avvio di Profiler on-demand

Quando il test è in esecuzione, è possibile avviare Profiler per acquisire tracce nell'app Web durante la ricezione del carico.
Passare al riquadro Configura Application Insights Profiler:

![Configurare una voce del riquadro di Profiler][configure-profiler-entry]

Nel riquadro **Configura Application Insights Profiler** è presente un pulsante **Profile Now** (Profila ora) per attivare Profiler su tutte le istanze delle app Web collegate. È possibile anche visualizzare i momento in cui Profiler è stato eseguito in precedenza.

![Profiler su richiesta][profiler-on-demand]

Si notino le modifiche di notifica e stato in relazione all'esecuzione di Profiler.

### <a name="step-3-view-traces"></a>Passaggio 3: Visualizzare le tracce

Al termine dell'esecuzione di Profiler, seguire le istruzioni visualizzate in base alla notifica per passare al riquadro Prestazioni e visualizzare le tracce.

### <a name="troubleshooting-on-demand-profiler"></a>Risoluzione dei problemi di Profiler su richiesta

In alcuni casi è possibile visualizzare un messaggio di errore di timeout di Profiler dopo una sessione su richiesta:

![Errore di timeout di Profiler][profiler-timeout]

L'errore può essere visualizzato per i due motivi seguenti.

1. La sessione di Profiler su richiesta ha esito positivo, ma Application Insights ha impiegato un tempo più lungo per elaborare i dati raccolti. Se l'elaborazione dei dati non è stata completata in 15 minuti, nel portale viene visualizzato un messaggio di timeout. Se dopo un certo periodo di tempo, le tracce di Profiler vengono visualizzate, ignorare il messaggio di errore. Microsoft sta lavorando per risolvere il problema.

2. L'app Web dispone di una versione precedente dell'agente di Profiler priva della funzionalità di esecuzione su richiesta. Se Application Insights Profiler è stato abilitato in precedenza, è probabile che sia necessario aggiornare l'agente di Profiler per iniziare a usare la funzionalità su richiesta.
  
Seguire questa procedura per verificare e installare la versione di Profiler più recente.

1. Passare alle impostazioni dell'app di Servizi app e verificare che siano specificate le impostazioni seguenti:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: sostituire il valore con la chiave di strumentazione appropriata per Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0. Se una di queste impostazioni non è specificata, andare al riquadro di abilitazione di Application Insights per installare l'estensione del sito più recente.

2. Andare al riquadro Application Insights nel portale di Servizi app.

    ![Abilitare Application Insights nel portale di Servizi app][enable-app-insights]

3. Se nella pagina seguente viene visualizzato un pulsante "Aggiorna", fare clic per aggiornare l'estensione del sito di Application Insights che consente di installare l'agente di Profiler più recente.
![Aggiornare l'estensione del sito][update-site-extension]

4. Fare clic su **Modifica** per garantire che Profiler venga attivato e selezionare **OK** per salvare le modifiche.

    ![Modificare e salvare Application Insights][change-and-save-appinsights]

5. Tornare alla scheda **Impostazioni app** del servizio app per verificare che siano specificate le impostazioni seguenti:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: sostituire il valore con la chiave di strumentazione appropriata per Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![Impostazioni dell'app per Profiler][app-settings-for-profiler]

6. Facoltativamente, selezionare la versione dell'estensione e verificare che non sia disponibile alcun aggiornamento.

    ![Verifica dell'aggiornamento dell'estensione][check-for-extension-update]

## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
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
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
