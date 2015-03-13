<properties 
	pageTitle="Usare processi Web per eseguire attività in background in Siti Web di Microsoft Azure" 
	description="Informazioni su come eseguire attività in background in Siti Web di Microsoft Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="timamm" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2014" 
	ms.author="cephalin"/>

#Usare processi Web per eseguire attività in background in Siti Web di Azure

Siti Web di Azure consente di eseguire programmi o script nel sito Web in tre modi: su richiesta, in modo continuo o in base a una pianificazione. Non sono previsti costi aggiuntivi per l'uso di Processi Web di Microsoft Azure.

Questo articolo descrive come distribuire Processi Web tramite il portale di gestione di Azure. Per informazioni su come eseguire la distribuzione usando Visual Studio o un processo di recapito continuo, vedere [Come distribuire i processi Web di Azure in Siti Web di Azure](http://azure.microsoft.com/documentation/articles/websites-dotnet-deploy-webjobs).

Azure WebJobs SDK semplifica molte attività di programmazione dei processi Web. Per altre informazioni, vedere [Cos'è Azure WebJobs SDK](../websites-dotnet-webjobs-sdk).

## Sommario ##
- [Tipi di file consentiti per gli script](#acceptablefiles)
- [Creare un'attività su richiesta](#CreateOnDemand)
- [Creare un'attività con esecuzione continua](#CreateContinuous)
- [Creare un'attività pianificata](#CreateScheduled)
	- [Processi pianificati e Utilità di pianificazione di Azure](#Scheduler)
- [Visualizzare la cronologia processo](#ViewJobHistory)
- [Note](#WHPNotes)
- [Passaggi successivi](#NextSteps)

## <a name="acceptablefiles"></a>Tipi di file consentiti per gli script o i programmi

Sono consentiti i seguenti tipi di file:

* .cmd, .bat, .exe (Prompt dei comandi di Windows)
* .ps1 (PowerShell)
* .sh (Bash)
* .php (PHP)
* .py (Python)
* .js (Node)

## <a name="CreateOnDemand"></a>Creare un'attività su richiesta

1. Nella barra dei comandi della pagina **Processi Web** fare clic su **Aggiungi**. Verrà visualizzata la finestra di dialogo **Nuovo processo**.
	
	![Attività su richiesta][OnDemandWebJob]
	
2. In **Nome** specificare un nome per l'attività. Il nome deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da "-" e "_".
	
3. Nella casella **Contenuto (file ZIP, massimo 100 megabit)** passare al file ZIP contenente lo script. Il file ZIP deve contenere il file eseguibile (con estensione .exe, .cmd, .bat, .sh, .php, .py, .js) e gli eventuali file di supporto necessari per eseguire il programma o lo script.
	
4. Nella casella **Modalità di esecuzione** selezionare **Esegui su richiesta**.
	
5. Selezionare il segno di spunta nella parte inferiore della finestra di dialogo per caricare lo script nel sito Web. Il nome specificato per l'attività verrà visualizzato nell'elenco:
	
	![Elenco attività][WebJobsList]
	
6. Per eseguire lo script, selezionare il nome corrispondente nell'elenco e fare clic su **Esegui una volta** nella barra dei comandi nella parte inferiore della pagina del portale.
	
	![Esegui una volta][RunOnce]

## <a name="CreateContinuous"></a>Creare un'attività con esecuzione continua

1. Per creare un'attività da eseguire continuamente, attenersi alla stessa procedura usata per creare un'attività eseguita una volta sola, ma nella casella **Modalità di esecuzione** selezionare **Esegui in modo continuo**.
	
	![Nuova attività continua][NewContinuousJob]
	
2. Per avviare o arrestare un'attività con esecuzione continua, selezionare l'attività nell'elenco e fare clic su **Avvia** o **Arresta** nella barra dei comandi.

> [AZURE.NOTE] Se il sito Web è in esecuzione in più di un'istanza, l'attività con esecuzione continua verrà eseguita in tutte le istanze. Le attività su richiesta e pianificate vengono eseguite in una singola istanza selezionata per il bilanciamento del carico da Microsoft Azure.

> [AZURE.NOTE]
> Per le attività con esecuzione continua, è consigliabile abilitare l'opzione **Always On** nella pagina Configura per il sito Web. La funzionalità AlwaysOn, disponibile in modalità Basic e Standard, evita che i siti Web vengano scaricati anche se sono rimasti inattivi per qualche tempo. Se il sito Web in uso è sempre caricato, l'attività con esecuzione continua potrebbe essere eseguita in modo più affidabile. 

## <a name="CreateScheduled"></a>Creare un'attività pianificata

1. Per creare un'attività pianificata, attenersi alla stessa procedura seguita in precedenza, ma nella casella **Modalità di esecuzione** selezionare **Esegui in base a una pianificazione**.
	
	![Nuovo processo pianificato][NewScheduledJob]
	
2. Selezionare una voce in **Area utilità di pianificazione** per il processo, quindi fare clic sulla freccia nella parte inferiore destra della finestra di dialogo per passare alla schermata successiva.

3. Nella finestra di dialogo **Crea processo** selezionare il tipo di ricorrenza desiderata in **Ricorrenza**: **Processo unico** o **Processo ricorrente**.
	
	![Pianificazione della ricorrenza][SchdRecurrence]
	
4. Scegliere inoltre un'ora di inizio in **Avvio**: **Ora** o **A un orario specifico**.
	
	![Pianificazione dell'ora di inizio][SchdStart]
	
5. Se si desidera che la pianificazione venga avviata a un'ora specifica, scegliere i valori della data e dell'ora di inizio in **Data avvio**.
	
	![Pianificazione dell'avvio per una data e ora specifiche][SchdStartOn]
	
6. Se si sceglie un processo ricorrente, sono disponibili l'opzione **Ricorre ogni** per specificare la frequenza di ricorrenza e l'opzione **Data di fine** per specificare una data e ora di fine.
	
	![Pianificazione della ricorrenza][SchdRecurEvery]
	
7. Se si sceglie **Settimane**, è possibile selezionare la casella **In una pianificazione specifica** e impostare i giorni della settimana in cui verrà eseguito il processo.
	
	![Pianificazione dei giorni della settimana][SchdWeeksOnParticular]
	
8. Se si sceglie **Mesi** e si seleziona la casella **In una pianificazione specifica**, è possibile impostare l'esecuzione del processo per determinate date del mese in **Giorni**. 
	
	![Pianificazione di determinate date del mese][SchdMonthsOnPartDays]
	
9. Se si sceglie **Giorni della settimana**, è possibile selezionare uno o più giorni della settimana del mese nei quali deve essere eseguito il processo.
	
	![Pianificazione di determinati giorni della settimana in un mese][SchdMonthsOnPartWeekDays]
	
10. Infine, è possibile usare anche l'opzione **Occorrenze** per scegliere la settimana del mese (prima, seconda, terza e così via) in cui deve essere eseguito il processo nei giorni della settimana specificati.
	
	![Pianificazione di determinati giorni della settimana in particolari settimane di un mese][SchdMonthsOnPartWeekDaysOccurences]
	
11. Dopo avere creato uno o più processi, i relativi nomi verranno visualizzati nella scheda Processi Web insieme a stato, tipo di pianificazione e altre informazioni. Vengono mantenute le informazioni cronologiche delle ultime 30 attività.
	
	![Elenco dei processi][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Processi pianificati e Utilità di pianificazione di Azure

I processi pianificati possono essere ulteriormente configurati mediante l'Utilità di pianificazione di Azure.

1.	Nella pagina Processi Web fare clic sul collegamento **Pianificazione** del processo per passare alla pagina del portale dell'Utilità di pianificazione di Azure. 
	
	![Collegamento all'Utilità di pianificazione di Azure][LinkToScheduler]
	
2. Nella pagina Utilità di pianificazione fare clic sul processo.
	
	![Processo nella pagina del portale dell'Utilità di pianificazione][SchedulerPortal]
	
3. Verrà visualizzata la pagina **Azione processo**, in cui è possibile configurare ulteriormente il processo. 
	
	![Pagina Azione processo nell'Utilità di pianificazione][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Visualizzare la cronologia processo

1. Per visualizzare la cronologia di esecuzione di un processo, inclusi i processi creati con WebJobs SDK, fare clic sul collegamento corrispondente nella colonna **Log**. È possibile usare l'icona degli Appunti per copiare l'URL della pagina del file di log negli Appunti, se lo si desidera.
	
	![Collegamento ai log][WebJobLogs]
		
2. Se si fa clic sul collegamento viene visualizzata la pagina dei dettagli dei processi Web per l'attività. In questa pagina è indicato il nome del comando eseguito, le ore delle ultime esecuzioni e il relativo esito positivo o negativo. In **Recent job runs** fare clic su un intervallo di tempo per visualizzare altri dettagli.
	
	![Dettagli dei processi Web][WebJobDetails]
	
3. Verrà visualizzata la pagina **WebJob Run Details**. Fare clic su **Attiva/Disattiva output** per visualizzare il testo del contenuto del log. Il log di output è in formato testo. 
	
	![Dettagli di esecuzione dei processi Web][WebJobRunDetails]
	
4. Per visualizzare il testo dell'output in un'altra finestra del browser, fare clic sul collegamento **download**. Per scaricare il testo vero e proprio, fare clic con il pulsante destro del mouse sul collegamento e usare le opzioni del browser per salvare il contenuto del file.
	
	![Download dell'output del log][DownloadLogOutput]
	
5. Il collegamento **Processi Web** nella parte superiore della pagina offre un modo pratico per ottenere un elenco di processi Web nel dashboard della cronologia.
	
	![Collegamento all'elenco dei processi Web][WebJobsLinkToDashboardList]
	
	![Elenco dei processi nel dashboard della cronologia][WebJobsListInJobsDashboard]
	
	Quando si fa clic su questi collegamenti, si viene reindirizzati alla pagina WebJob Details per il processo selezionato.


## <a name="WHPNotes"></a>Note
	
- A partire da marzo 2014, i siti Web in modalità gratuita possono scadere dopo 20 minuti se non vengono inviate richieste al sito scm (distribuzione) e se il portale del sito Web non è aperto in Azure. Le richieste al sito effettivo non comportano la reimpostazione del timeout.
- Il codice per un processo continuo deve essere scritto per l'esecuzione in un ciclo infinito.
- I processi continui vengono eseguiti in modo continuo solo quando il sito è attivo.
- Le modalità Basic e Standard offrono la funzionalità AlwaysOn che, se abilitata, impedisce ai siti di diventare inattivi.

## <a name="NextSteps"></a>Passaggi successivi
 
Per altre informazioni, vedere [Risorse consigliate per Processi Web di Azure][WebJobsRecommendedResources].

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png


<!--HONumber=42-->
