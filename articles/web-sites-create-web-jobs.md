<properties 
	pageTitle="Esecuzione di attività in background con Processi Web" 
	description="Informazioni su come eseguire attività in background nelle app Web di Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Esecuzione di attività in background con Processi Web

## Informazioni generali

È possibile eseguire programmi o script in Processi Web nell'app Web del [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714) in tre modi: su richiesta, sempre, in base a una pianificazione. Non sono previsti costi aggiuntivi per l'uso di Processi Web.

Nell'articolo viene descritto come distribuire Processi Web utilizzando il [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Per informazioni sulla distribuzione mediante Visual Studio o un processo di distribuzione continua, vedere [Come distribuire Processi Web di Azure nelle app Web](websites-dotnet-deploy-webjobs.md).

Azure WebJobs SDK semplifica molte attività di programmazione dei processi Web. Per ulteriori informazioni, vedere [Definizione dell'SDK di Processi Web](websites-dotnet-webjobs-sdk.md).

## <a name="acceptablefiles"></a>Tipi di file consentiti per script e programmi

Sono consentiti i tipi di file seguenti:

* .cmd, .bat, .exe (Prompt dei comandi di Windows)
* .ps1 (PowerShell)
* .sh (Bash)
* .php (PHP)
* .py (Python)
* .js (Node)

## <a name="CreateOnDemand"></a>Creare un processo Web su richiesta nel portale

1. Nel pannello **App Web** del [portale di Azure](http://portal.azure.com), fare clic su **Tutte le impostazioni > Processi Web** per visualizzare il pannello **Processi Web**.
	
	![WebJob blade](./media/web-sites-create-web-jobs/wjblade.png)
	
5. Fare clic su **Aggiungi**. Viene visualizzata la finestra di dialogo **Aggiungi processo Web**.
	
	![Add WebJob blade](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. In **Nome**, indicare un nome per il processo Web. Il nome deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da "-" e "_".
	
4. Nella casella **Modalità di esecuzione**, scegliere **Esegui su richiesta**.
	
3. Nella casella **Caricamento file**, fare clic sull'icona della cartella e accedere al file zip che contiene lo script. Il file ZIP deve contenere il file eseguibile (con estensione .exe, .cmd, .bat, .sh, .php, .py, .js) e gli eventuali file di supporto necessari per eseguire il programma o lo script.
	
5. Selezionare **Crea** per caricare lo script nell'app Web. 
	
	Il nome indicato per il processo Web viene visualizzato nell'elenco del pannello **Processi Web**.
	
6. Per eseguire il processo Web, fare clic con il pulsante destro del mouse sul nome visualizzato nell'elenco e scegliere **Esegui**.
	
	![Run WebJob](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>Creare un processo Web con esecuzione continua

1. Per creare un processo Web eseguito in modo continuo, attenersi alla stessa procedura utilizzata per creare un processo Web eseguita una volta sola e nella casella **Modalità di esecuzione**, seleziona **Continua**.

2. Per avviare o interrompere un processo Web continuo, selezionarlo con il pulsante destro destro del mouse dall'elenco, quindi fare clic su **Avvia** o **Interrompi**.
	
> [AZURE.NOTE] Se l'app Web è in esecuzione su più di un'istanza, il processo Web con esecuzione continua verrà eseguito su tutte le istanze. I processi Web su richiesta e pianificati vengono eseguiti su una singola istanza selezionata per il bilanciamento del carico da Microsoft Azure.
	
> [AZURE.NOTE] Nel caso dei processi Web con esecuzione continua, si consiglia di abilitare l'opzione **Sempre attivata** per l'app Web. La funzionalità Sempre attivata, disponibile in modalità di base e standard, impedisce alle app Web di essere scaricate anche se sono rimaste inattive per qualche tempo. Se l'app Web in uso è sempre caricata, il processo Web con esecuzione continua potrebbe essere eseguito con maggiore affidabilità. 

## <a name="CreateScheduled"></a>Creare un processo Web pianificato

Il portale di gestione di Azure non permette ancora di creare un processo Web pianificato; nel frattempo, è possibile eseguire questa operazione utilizzando il [portale precedente](http://manage.windowsazure.com).

1. Nel [portale precedente](http://manage.windowsazure.com), accedere alla pagina del processo Web e fare clic su **Aggiungi**.

1. Nella casella **Modalità di esecuzione**, scegliere **Esegui in base a una pianificazione**.
	
	![New Scheduled Job][NewScheduledJob]
	
2. Scegliere l'**Area utilità di pianificazione** per il processo, quindi fare clic sulla freccia nella parte inferiore destra della finestra di dialogo per passare alla schermata successiva.

3. Nella finestra di dialogo **Crea processo**, scegliere il tipo di **ricorrenza** desiderata: **Processo unico** o **Processo ricorrente**.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. Inoltre, scegliere un'ora di **inizio**: **Ora** o **A un orario specifico**.
	
	![Schedule Start Time][SchdStart]
	
5. Se si desidera che la pianificazione venga avviata a un'ora specifica, scegliere i valori della data e dell'ora di inizio in **Data avvio**.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. Se si sceglie un processo ricorrente, è disponibile l'opzione **Ricorre ogni** per specificare la frequenza di ricorrenza e l'opzione **Data di fine** per specificare un'ora di fine.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. Se si sceglie **Settimane**, è possibile selezionare la casella **In una pianificazione specifica** e specificare i giorni della settimana in cui verrà eseguito il processo.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. Se si sceglie **Mesi** e si seleziona la casella **In una pianificazione specifica**, è possibile impostare l'esecuzione del processo in determinati giorni del mese in **Giorni**. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. Se si sceglie **Giorni della settimana**, è possibile selezionare uno o più giorni della settimana del mese nei quali deve essere eseguito il processo.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. Infine, è possibile usare l'opzione **Occorrenze** per scegliere la settimana del mese (prima, seconda, terza e così via) in cui deve essere eseguito il processo nei giorni della settimana specificati.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. Dopo avere creato uno o più processi, i relativi nomi verranno visualizzati nella scheda Processi Web insieme a stato, tipo di pianificazione e altre informazioni. Vengono mantenute le informazioni cronologiche sugli ultimi 30 processi Web.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Processi pianificati e Utilità di pianificazione di Azure

I processi pianificati possono essere ulteriormente configurati mediante l'Utilità di pianificazione di Azure del [portale precedente](http://manage.windowsazure.com).

1.	Nella pagina Processi Web fare clic sul collegamento **Pianificazione** del processo per passare alla pagina del portale dell'Utilità di pianificazione di Azure. 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. Nella pagina Utilità di pianificazione fare clic sul processo.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. Verrà visualizzata la pagina **Azione processo** in cui è possibile configurare ulteriormente il processo. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Visualizzare la cronologia del processo

1. Per visualizzare la cronologia di esecuzione di un processo, inclusi i processi creati con l'SDK di processi Web, fare clic sul collegamento corrispondente nella colonna **Log** nel pannello dei processi Web. È possibile usare l'icona degli Appunti per copiare l'URL della pagina del file di log negli Appunti, se lo si desidera.
	
	![Collegamento log](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. Se si fa clic sul collegamento, viene visualizzata la pagina dei dettagli per il processo Web. In questa pagina è indicato il nome del comando eseguito, le ore delle ultime esecuzioni e il relativo esito positivo o negativo. In **Processo recente eseguito**, fare clic su un orario per visualizzare ulteriori dettagli.
	
	![WebJobDetails][WebJobDetails]
	
3. Viene visualizzata la pagina **Dettagli esecuzione processo Web**. Fare clic su **Attiva/Disattiva output** per visualizzare il testo dei contenuti del log. Il log di output è in formato testo. 
	
	![Web job run details][WebJobRunDetails]
	
4. Per visualizzare il testo dell'output in un'altra finestra del browser, fare clic sul collegamento **Download**. Per scaricare il testo stesso, fare clic con il pulsante destro del mouse sul collegamento e usare le opzioni del browser in uso per salvare il contenuto del file.
	
	![Download log output][DownloadLogOutput]
	
5. Il collegamento **Processi Web** nella parte superiore della pagina offre un modo pratico per ottenere un elenco di processi Web nel dashboard della cronologia.
	
	![Link to WebJobs list][WebJobsLinkToDashboardList]
	
	![List of WebJobs in history dashboard][WebJobsListInJobsDashboard]
	
	Quando si fa clic su questi collegamenti, si viene reindirizzati alla pagina WebJob Details per il processo selezionato.


## <a name="WHPNotes"></a>Note
	
- A partire da marzo 2014, le app Web in modalità gratuita possono scadere dopo 20 minuti, se non vengono inviate richieste al sito scm (distribuzione) e se il portale dell'app Web non è aperto in Azure. Le richieste al sito effettivo non comportano la reimpostazione del timeout.
- Il codice per un processo continuo deve essere scritto per l'esecuzione in un ciclo infinito.
- I processi continui vengono eseguiti in modo continuo solo quando l'app è attiva.
- Le modalità di base e standard offrono la funzionalità Sempre attivata che, se abilitata, impedisce alle app Web di diventare inattive.

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non é necessario fornire una carta di credito né impegnarsi in alcun modo.

## <a name="NextSteps"></a>Passaggi successivi
 
Per ulteriori informazioni, vedere [Risorse consigliate per i processi Web di Azure][WebJobsRecommendedResources].

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

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

<!--HONumber=49-->