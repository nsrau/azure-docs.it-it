<properties linkid="web-sites-create-web-jobs" urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Use WebJobs to run background tasks in Microsoft Azure Websites" metaKeywords="Microsoft Azure Web Sites, Web Jobs, background tasks" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Uso di processi Web per l'esecuzione di attività in background in Siti Web di Microsoft Azure

Siti Web di Microsoft Azure consente di eseguire programmi o script nel sito Web in tre modi: su richiesta, in modo continuo o in base a una pianificazione. Non sono previsti costi aggiuntivi per l'uso di Processi Web di Microsoft Azure a meno che non si desideri abilitare la funzionalità AlwaysOn descritta più avanti in questo articolo.

## Sommario

-   [Tipi di file consentiti per gli script][Tipi di file consentiti per gli script]
-   [Creazione di un'attività su richiesta][Creazione di un'attività su richiesta]
-   [Creazione di un'attività con esecuzione continua][Creazione di un'attività con esecuzione continua]
-   [Creazione di un'attività pianificata][Creazione di un'attività pianificata]

    -   [Processi pianificati e Utilità di pianificazione di Azure][Processi pianificati e Utilità di pianificazione di Azure]
-   [Visualizzazione della cronologia processo][Visualizzazione della cronologia processo]
-   [Note][Note]
-   [Passaggi successivi][Passaggi successivi]

    -   [Maggiore produttività con Microsoft Azure WebJobs SDK][Maggiore produttività con Microsoft Azure WebJobs SDK]
    -   [Metodi di distribuzione alternativi][Metodi di distribuzione alternativi]
    -   [Risorse aggiuntive][Risorse aggiuntive]

<a name="acceptablefiles"></a>

## Tipi di file consentiti per gli script

I seguenti tipi di file sono accettati come script eseguibili:

.cmd, .bat, .exe (Prompt dei comandi di Windows)

.ps1 (PowerShell)

.sh (Bash)

.php (PHP)

.py (Python)

.js (Node)

<a name="CreateOnDemand"></a>

## Creazione di un'attività su richiesta

1.  Nella barra dei comandi della pagina **Processi Web** fare clic su **Aggiungi**. Verrà visualizzata la finestra di dialogo **Nuovo processo**.

    ![Attività su richiesta][Attività su richiesta]

2.  In **Nome** specificare un nome per l'attività. Il nome deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da "-" e "\_".

3.  Nella casella **Contenuto (file ZIP, massimo 100 megabit)** passare al file ZIP contenente lo script. Il file ZIP deve contenere il file eseguibile (con estensione .exe, .cmd, .bat, .sh, .php, .py, .js) e gli eventuali file di supporto necessari per eseguire il programma o lo script.

4.  Nella casella **Modalità di esecuzione** selezionare **Esegui su richiesta**.

5.  Selezionare il segno di spunta nella parte inferiore della finestra di dialogo per caricare lo script nel sito Web. Il nome specificato per l'attività verrà visualizzato nell'elenco:

    ![Elenco attività][Elenco attività]

6.  Per eseguire lo script, selezionare il nome corrispondente nell'elenco e fare clic su **Esegui una volta** nella barra dei comandi nella parte inferiore della pagina del portale.

    ![Opzione Esegui una volta][Opzione Esegui una volta]

<a name="CreateContinuous"></a>

## Creazione di un'attività con esecuzione continua

1.  Per creare un'attività eseguita in modo continuo, attenersi alla stessa procedura utilizzata per creare un'attività eseguita una volta sola e nella casella **Modalità di esecuzione** selezionare **Esegui in modo continuo**.

    ![Nuova attività continua][Nuova attività continua]

2.  Per avviare o arrestare un'attività con esecuzione continua, selezionare l'attività nell'elenco e fare clic su **Avvia** o **Arresta** nella barra dei comandi.

> [WACOM.NOTE] Se il sito Web è in esecuzione su più di un'istanza, l'attività con esecuzione continua verrà eseguita su tutte le istanze. Le attività su richiesta e pianificate vengono eseguite su una singola istanza selezionata per il bilanciamento del carico da Microsoft Azure.

> [WACOM.NOTE]
> Per le attività con esecuzione continua, è consigliabile abilitare l'opzione **Always On** nella pagina Configura per il sito Web. La funzionalità AlwaysOn, disponibile in modalità Basic e Standard, evita che i siti Web vengano scaricati anche se sono rimasti inattivi per qualche tempo. Se il sito Web in uso è sempre caricato, l'attività con esecuzione continua potrebbe essere eseguita in modo più affidabile.

<a name="CreateScheduled"></a>

## Creazione di un'attività pianificata

1.  Per creare un'attività pianificata, attenersi alla stessa procedura seguita in precedenza e nella casella **Modalità di esecuzione** selezionare **Esegui in base a una pianificazione**.

    ![Nuovo processo pianificato][Nuovo processo pianificato]

2.  Scegliere una voce in **Area utilità di pianificazione** per il processo e quindi fare clic sulla freccia nella parte inferiore destra della finestra di dialogo per passare alla schermata successiva.

3.  Nella finestra di dialogo **Crea processo** scegliere il tipo di ricorrenza desiderata in **Ricorrenza**: **Processo unico** o **Processo ricorrente**.

    ![Pianificare la ricorrenza][Pianificare la ricorrenza]

4.  Scegliere inoltre un'ora di inizio in **Avvio**: **Ora** o **A un orario specifico**.

    ![Pianificare l'ora di inizio][Pianificare l'ora di inizio]

5.  Se si desidera che la pianificazione venga avviata a un'ora specifica, scegliere i valori della data e dell'ora di inizio in **Data avvio**.

    ![Pianificare l'avvio a un'ora specifica][Pianificare l'avvio a un'ora specifica]

6.  Se si sceglie un processo ricorrente, è disponibile l'opzione **Ricorre ogni** per specificare la frequenza di ricorrenza e l'opzione **Data di fine** per specificare un'ora di fine.

    ![Pianificare la ricorrenza][1]

7.  Se si sceglie **Settimane**, è possibile selezionare la casella **In una pianificazione specifica** e specificare i giorni della settimana in cui verrà eseguito il processo.

    ![Pianificare i giorni della settimana][Pianificare i giorni della settimana]

8.  Se si sceglie **Mesi** e si seleziona la casella **In una pianificazione specifica** è possibile impostare l'esecuzione del processo in determinati giorni del mese in **Giorni**.

    ![Pianificare date specifiche nel mese][Pianificare date specifiche nel mese]

9.  Se si sceglie **Giorni della settimana**, è possibile selezionare uno o più giorni della settimana del mese nei quali deve essere eseguito il processo.

    ![Pianificare giorni della settimana specifici in un mese][Pianificare giorni della settimana specifici in un mese]

10. Infine, è possibile usare l'opzione **Occorrenze** per scegliere la settimana del mese (prima, seconda, terza e così via) in cui deve essere eseguito il processo nei giorni della settimana specificati.

    ![Pianificare giorni della settimana specifici per determinate settimane in un mese][Pianificare giorni della settimana specifici per determinate settimane in un mese]

11. Dopo avere creato uno o più processi, i relativi nomi verranno visualizzati nella scheda Processi Web insieme a stato, tipo di pianificazione e altre informazioni. Vengono mantenute le informazioni cronologiche delle ultime 30 attività.

    ![Elenco processi][Elenco processi]

<a name="Scheduler"></a>

### Processi pianificati e Utilità di pianificazione di Azure

I processi pianificati possono essere ulteriormente configurati mediante l'Utilità di pianificazione di Azure.

1.  Nella pagina Processi Web fare clic sul collegamento **pianificazione** del processo per passare alla pagina del portale dell'Utilità di pianificazione di Azure.

    ![Collegamento all'Utilità di pianificazione di Azure][Collegamento all'Utilità di pianificazione di Azure]

2.  Nella pagina Utilità di pianificazione fare clic sul processo.

    ![Processo nella pagina del portale dell'Utilità di pianificazione][Processo nella pagina del portale dell'Utilità di pianificazione]

3.  Verrà visualizzata la pagina **Azione processo** in cui è possibile configurare ulteriormente il processo.

    ![Pagina Azione processo nell'Utilità di pianificazione][Pagina Azione processo nell'Utilità di pianificazione]

<!-- ================ ViewJobHistory ================= -->

<a name="ViewJobHistory"></a>

## Visualizzazione della cronologia processo

1.  Per visualizzare la cronologia di esecuzione di un processo, inclusi i processi creati con WebJobs SDK, fare clic sul collegamento corrispondente nella colonna **Log**. È possibile utilizzare l'icona degli Appunti per copiare l'URL della pagina del file di log negli Appunti, se lo si desidera.

    ![Collegamento Log][Collegamento Log]

2.  Se si fa clic sul collegamento viene visualizzata la pagina dei dettagli dei processi Web per l'attività. In questa pagina è indicato il nome del comando eseguito, le ore delle ultime esecuzioni e il relativo esito positivo o negativo. In **Recent job runs** fare clic su un'ora per visualizzare ulteriori dettagli.

    ![Dettagli processo Web][Dettagli processo Web]

3.  Verrà visualizzata la pagina **WebJob Run Details**. Fare clic su **Toggle Output** per visualizzare il testo dei contenuti del log. Il log di output è in formato testo.

    ![Dettagli dell'esecuzione del processo Web][Dettagli dell'esecuzione del processo Web]

4.  Per visualizzare il testo dell'output in un'altra finestra del browser, fare clic sul collegamento **download**. Per scaricare il testo stesso, fare clic con il pulsante destro del mouse sul collegamento e utilizzare le opzioni del browser in uso per salvare il contenuto del file.

    ![Download dell'output del log][Download dell'output del log]

5.  Il collegamento **Processi Web** nella parte superiore della pagina offre un modo pratico per ottenere un elenco di processi Web nel dashboard della cronologia.

    ![Collegamento all'elenco dei processi Web][Collegamento all'elenco dei processi Web]

    ![Elenco dei processi nel dashboard della cronologia][Elenco dei processi nel dashboard della cronologia]

    Quando si fa clic su questi collegamenti, si viene reindirizzati alla pagina WebJob Details per il processo selezionato.

<a name="WHPNotes"></a>

## Note

-   A partire da marzo 2014, i siti Web in modalità gratuita possono scadere dopo 20 minuti se non vengono inviate richieste al sito scm (distribuzione) e se il portale del sito Web non è aperto in Azure. Le richieste al sito effettivo non comportano la reimpostazione del timeout.

-   Il codice per un processo continuo deve essere scritto per l'esecuzione in un ciclo infinito.

-   I processi continui vengono eseguiti in modo continuo solo quando il sito è attivo.

-   Le modalità Basic e Standard offrono la funzionalità AlwaysOn che, se abilitata, impedisce ai siti di diventare inattivi.

<a name="NextSteps"></a>

## Passaggi successivi

<!-- ======= Do More with the Microsoft Azure WebJobs SDK ======== -->

<a name="WebJobsSDK"></a>

### Maggiore produttività con Microsoft Azure WebJobs SDK

Microsoft Azure WebJobs SDK semplifica l'attività di programmazione di un processo Web che interagisce con le code, i BLOB o le tabelle di Archiviazione di Azure o con le code del bus di servizio di Azure. Il dashboard, ora integrato nel portale di Azure, offre funzionalità di monitoraggio e diagnostica avanzate per i programmi scritti mediante l'SDK. Le funzionalità di monitoraggio e diagnostica sono incorporate nell'SDK e non richiedono l'aggiunta di codice speciale nel programma da parte dell'utente.

Per ulteriori informazioni, vedere [Introduzione a Microsoft Azure WebJobs SDK][Introduzione a Microsoft Azure WebJobs SDK].

<!-- =========== Alternative Methods of Deployment ============= -->

<a name="AlternateDeployments"></a>

### Metodi di distribuzione alternativi

Visual Studio 2013 include funzionalità che consentono di automatizzare la distribuzione di progetti di applicazione console come processi Web. Per altre informazioni, vedere [Come distribuire i processi Web di Azure ai siti Web di Azure][Come distribuire i processi Web di Azure ai siti Web di Azure].

Per distribuire i processi Web è anche possibile usare FTP, Git o Web Deploy. Per altre informazioni, vedere l'articolo di blog relativo alla [distribuzione GIT di un'app console .NET in Azure mediante Processi Web][distribuzione GIT di un'app console .NET in Azure mediante Processi Web] e l'argomento [Come distribuire i processi Web di Windows Azure][Come distribuire i processi Web di Windows Azure].

<a name="AdditionalResources"></a>

### Risorse aggiuntive

-   Per un elenco dettagliato di collegamenti sulla funzionalità Processi Web, vedere l'articolo relativo alle [risorse consigliate per i processi Web di Azure][risorse consigliate per i processi Web di Azure].

-   Video relativi a Processi Web:

    [Processi Web di Azure 101 - Processi Web di base con Jamie Espinosa][Processi Web di Azure 101 - Processi Web di base con Jamie Espinosa]

    [Processi Web di Azure 102 - Processi Web pianificati e dashboard Processi Web con Jamie Espinosa][Processi Web di Azure 102 - Processi Web pianificati e dashboard Processi Web con Jamie Espinosa]

    [Utilità di pianificazione di Azure 101 - Kevin Lam spiega come pianificare attività][Utilità di pianificazione di Azure 101 - Kevin Lam spiega come pianificare attività]

### Attività iniziali

Per iniziare a usare Azure, vedere la pagina relativa alla [versione di valutazione gratuita di Microsoft Azure][versione di valutazione gratuita di Microsoft Azure].

<!-- LINKS --> <!-- IMAGES -->

  [Tipi di file consentiti per gli script]: #acceptablefiles
  [Creazione di un'attività su richiesta]: #CreateOnDemand
  [Creazione di un'attività con esecuzione continua]: #CreateContinuous
  [Creazione di un'attività pianificata]: #CreateScheduled
  [Processi pianificati e Utilità di pianificazione di Azure]: #Scheduler
  [Visualizzazione della cronologia processo]: #ViewJobHistory
  [Note]: #WHPNotes
  [Passaggi successivi]: #NextSteps
  [Maggiore produttività con Microsoft Azure WebJobs SDK]: #WebJobsSDK
  [Metodi di distribuzione alternativi]: #AlternateDeployments
  [Risorse aggiuntive]: #AdditionalResources
  [Attività su richiesta]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
  [Elenco attività]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
  [Opzione Esegui una volta]: ./media/web-sites-create-web-jobs/13RunOnce.png
  [Nuova attività continua]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
  [Nuovo processo pianificato]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
  [Pianificare la ricorrenza]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
  [Pianificare l'ora di inizio]: ./media/web-sites-create-web-jobs/06SchdStart.png
  [Pianificare l'avvio a un'ora specifica]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
  [1]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
  [Pianificare i giorni della settimana]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
  [Pianificare date specifiche nel mese]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
  [Pianificare giorni della settimana specifici in un mese]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
  [Pianificare giorni della settimana specifici per determinate settimane in un mese]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
  [Elenco processi]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
  [Collegamento all'Utilità di pianificazione di Azure]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
  [Processo nella pagina del portale dell'Utilità di pianificazione]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
  [Pagina Azione processo nell'Utilità di pianificazione]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
  [Collegamento Log]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
  [Dettagli processo Web]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
  [Dettagli dell'esecuzione del processo Web]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
  [Download dell'output del log]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
  [Collegamento all'elenco dei processi Web]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
  [Elenco dei processi nel dashboard della cronologia]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
  [Introduzione a Microsoft Azure WebJobs SDK]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Come distribuire i processi Web di Azure ai siti Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/websites-dotnet-deploy-webjobs
  [distribuzione GIT di un'app console .NET in Azure mediante Processi Web]: http://blog.amitapple.com/post/73574681678/git-deploy-console-app
  [Come distribuire i processi Web di Windows Azure]: http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs
  [risorse consigliate per i processi Web di Azure]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Processi Web di Azure 101 - Processi Web di base con Jamie Espinosa]: http://www.windowsazure.com/it-it/documentation/videos/azure-webjobs-basics/
  [Processi Web di Azure 102 - Processi Web pianificati e dashboard Processi Web con Jamie Espinosa]: http://www.windowsazure.com/it-it/documentation/videos/azure-webjobs-schedule-and-dashboard/
  [Utilità di pianificazione di Azure 101 - Kevin Lam spiega come pianificare attività]: http://www.windowsazure.com/it-it/documentation/videos/azure-scheduler-how-to/
  [versione di valutazione gratuita di Microsoft Azure]: http://azure.microsoft.com/it-it/pricing/free-trial/
