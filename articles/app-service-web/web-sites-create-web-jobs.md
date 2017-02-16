---
title: "Eseguire attività in background con processi Web"
description: "Informazioni su come eseguire attività in background nelle app Web di Azure."
services: app-service
documentationcenter: 
author: tdykstra
manager: wpickett
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: 10320f338d902ffefd8a98fd59f3e8fb22682b00
ms.openlocfilehash: 578575877fc706076ac2fdf034cb1ac0e92b16ef


---
# <a name="run-background-tasks-with-webjobs"></a>Eseguire attività in background con processi Web
## <a name="overview"></a>Panoramica
È possibile eseguire programmi o script in Processi Web nell'app Web del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) in tre modi: su richiesta, sempre, in base a una pianificazione. Non sono previsti costi aggiuntivi per l'uso di Processi Web.

Nell'articolo viene descritto come distribuire Processi Web utilizzando il [Portale di Azure](https://portal.azure.com). Per informazioni sulla distribuzione mediante Visual Studio o un processo di distribuzione continua, vedere [Come distribuire Processi Web di Azure nelle App Web](websites-dotnet-deploy-webjobs.md).

Azure WebJobs SDK semplifica molte attività di programmazione dei processi Web. Per ulteriori informazioni, vedere [Definizione dell'SDK di Processi Web](websites-dotnet-webjobs-sdk.md).

 Funzioni di Azure offre un altro modo per eseguire programmi e script da un ambiente senza server o da un'app Servizio app. Per altre informazioni, vedere [Panoramica di Funzioni di Azure](../azure-functions/functions-overview.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="a-nameacceptablefilesaacceptable-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Tipi di file consentiti per script e programmi
Sono consentiti i tipi di file seguenti:

* .cmd, .bat, .exe (Prompt dei comandi di Windows)
* .ps1 (PowerShell)
* .sh (Bash)
* .php (PHP)
* .py (Python)
* .js (Node)
* JAR (Java)

## <a name="a-namecreateondemandacreate-an-on-demand-webjob-in-the-portal"></a><a name="CreateOnDemand"></a>Creare un processo Web su richiesta nel portale
1. Nel pannello **App Web** del [Portale di Azure](https://portal.azure.com) fare clic su **Tutte le impostazioni > Processi Web** per visualizzare il pannello **Processi Web**.
   
    ![Pannello Processi Web](./media/web-sites-create-web-jobs/wjblade.png)
2. Fare clic su **Aggiungi**. Viene visualizzata la finestra di dialogo **Aggiungi processo Web** .
   
    ![Pannello Aggiungi processo Web](./media/web-sites-create-web-jobs/addwjblade.png)
3. In **Name**indicare un nome per il processo Web. Il nome deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da "-" e "_".
4. Nella casella **Modalità di esecuzione** selezionare **Esegui su richiesta**.
5. Nella casella **Caricamento file** , fare clic sull'icona della cartella e accedere al file zip che contiene lo script. Il file ZIP deve contenere il file eseguibile (con estensione .exe, .cmd, .bat, .sh, .php, .py, .js) e gli eventuali file di supporto necessari per eseguire il programma o lo script.
6. Selezionare **Crea** per caricare lo script nell'app Web. 
   
    Il nome indicato per il processo Web viene visualizzato nell'elenco del pannello **Processi Web** .
7. Per eseguire il processo Web, fare clic con il pulsante destro del mouse sul nome visualizzato nell'elenco e scegliere **Esegui**.
   
    ![Eseguire un processo Web](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="a-namecreatecontinuousacreate-a-continuously-running-webjob"></a><a name="CreateContinuous"></a>Creare un processo Web con esecuzione continua
1. Per creare un processo Web eseguito in modo continuo, seguire la stessa procedura usata per creare un processo Web eseguito una sola volta, ma nella casella **Modalità di esecuzione** selezionare **Continuo**.
2. Per avviare o interrompere un processo Web continuo, selezionarlo con il pulsante destro del mouse nell'elenco, quindi fare clic su **Avvia** o **Interrompi**.

> [!NOTE]
> Se l'app Web è in esecuzione su più di un'istanza, il processo Web con esecuzione continua verrà eseguito su tutte le istanze. I processi Web su richiesta e pianificati vengono eseguiti su una singola istanza selezionata per il bilanciamento del carico da Microsoft Azure.
> 
> Per i processi Web continui da eseguire in modo affidabile su tutte le istanze, abilitare l'impostazione di configurazione Always On* per l'app Web, altrimenti è possibile che l'esecuzione dei processi venga interrotta quando il sito host SCM rimane inattivo per troppo tempo.
> 
> 

## <a name="a-namecreatescheduledcronacreate-a-scheduled-webjob-using-a-cron-expression"></a><a name="CreateScheduledCRON"></a>Creare un processo Web pianificato utilizzando un'espressione CRON
Questa tecnica, disponibile per le app Web in esecuzione in modalità Basic, Standard o Premium, richiede che nell'app sia abilitata l'impostazione **Always On** .

Per trasformare un processo Web On Demand in un processo Web pianificato, includere semplicemente un file `settings.job` nella directory principale del file zip del processo Web. Questo file JSON deve includere una `schedule` proprietà con un [espressione CRON](https://en.wikipedia.org/wiki/Cron), per esempio quanto riportato di seguito.

L'espressione CRON è composta da 6 campi: `{second} {minute} {hour} {day} {month} {day of the week}`.

Ad esempio, per attivare il processo Web ogni 15 minuti, `settings.job` avrebbe:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

Altri esempi di programmazione CRON:

* Ogni ora (ad esempio ogni volta che il numero di minuti è 0): `0 0 * * * *` 
* Ogni ora dalle 9 di mattina alle 5 del pomeriggio: `0 0 9-17 * * *` 
* Alle 9:30 di mattina ogni giorno: `0 30 9 * * *`
* Alle 9:30 di mattina ogni giorno della settimana: `0 30 9 * * 1-5`

**Nota**: quando si distribuisce un processo Web da Visual Studio, assicurarsi di contrassegnare le proprietà del file `settings.job` come "Copia se più recente".

## <a name="a-namecreatescheduledacreate-a-scheduled-webjob-using-the-azure-scheduler"></a><a name="CreateScheduled"></a>Creare un processo Web pianificato utilizzando l’Utilità di pianificazione di Azure
La tecnica alternativa seguente usa l'utilità di pianificazione di Azure. In questo caso, il processo Web non dispone di alcuna conoscenza diretta della pianificazione. Al contrario, l'utilità di pianificazione di Azure verrà configurata per attivare il processo Web in una pianificazione. 

Il portale di Azure non permette ancora di creare un processo Web pianificato; tuttavia, attualmente è possibile eseguire questa operazione utilizzando il [portale classico](http://manage.windowsazure.com).

1. Nel [portale classico](http://manage.windowsazure.com) , accedere alla pagina del processo Web e fare clic su **Aggiungi**.
2. Nella casella **Modalità di esecuzione** scegliere **Esegui in base a una pianificazione**.
   
    ![Nuovo processo pianificato][NewScheduledJob]
3. Scegliere una voce in **Scheduler Region** per il processo e quindi fare clic sulla freccia nella parte inferiore destra della finestra di dialogo per passare alla schermata successiva.
4. Nella finestra di dialogo **Crea processo** scegliere il tipo di **Ricorrenza** desiderata: **Processo con singola occorrenza** o **Processo ricorrente**.
   
    ![Pianificare la ricorrenza][SchdRecurrence]
5. Scegliere un'ora di **inizio**: **Ora** o **A un orario specifico**.
   
    ![Pianificare l'ora di inizio][SchdStart]
6. Se si desidera che la pianificazione venga avviata a un'ora specifica, scegliere i valori dell'ora di inizio in **Starting On**.
   
    ![Pianificare l'avvio a un'ora specifica][SchdStartOn]
7. Se si sceglie un processo ricorrente, è disponibile l'opzione **Ricorre ogni** per specificare la frequenza di ricorrenza e l'opzione **Data di fine** per specificare la data e l'ora di fine.
   
    ![Pianificare la ricorrenza][SchdRecurEvery]
8. Se si sceglie **Settimane**, è possibile selezionare la casella **In una pianificazione specifica** e specificare i giorni della settimana in cui verrà eseguito il processo.
   
    ![Pianificare i giorni della settimana][SchdWeeksOnParticular]
9. Se si sceglie **Mesi** e si seleziona la casella **In una pianificazione specifica**, è possibile impostare l'esecuzione del processo in determinati giorni del mese in **Giorni**. 
   
    ![Pianificare date specifiche nel mese][SchdMonthsOnPartDays]
10. Se si sceglie **Week Days**, è possibile selezionare il giorno o i giorni della settimana del mese nei quali deve essere eseguito il processo.
    
     ![Pianificare giorni della settimana specifici in un mese][SchdMonthsOnPartWeekDays]
11. Infine, è possibile usare l'opzione **Occorrenze** per scegliere la settimana del mese (prima, seconda, terza e così via) in cui deve essere eseguito il processo nei giorni della settimana specificati.
    
    ![Pianificare giorni della settimana specifici per determinate settimane in un mese][SchdMonthsOnPartWeekDaysOccurences]
12. Dopo avere creato uno o più processi, i relativi nomi verranno visualizzati nella scheda WebJobs insieme a stato, tipo di pianificazione e altre informazioni. Vengono mantenute le informazioni cronologiche sugli ultimi 30 processi Web.
    
    ![Elenco processi][WebJobsListWithSeveralJobs]

### <a name="a-nameschedulerascheduled-jobs-and-azure-scheduler"></a><a name="Scheduler"></a>Processi pianificati e Utilità di pianificazione di Azure
I processi pianificati possono essere ulteriormente configurati nell'Utilità di pianificazione di Azure del [portale classico](http://manage.windowsazure.com).

1. Nella pagina WebJobs fare clic sul collegamento **schedule** del processo per passare alla pagina del portale dell'Utilità di pianificazione di Azure. 
   
   ![Collegamento all'Utilità di pianificazione di Azure][LinkToScheduler]
2. Nella pagina Scheduler fare clic sul processo.
   
    ![Processo nella pagina del portale dell'Utilità di pianificazione][SchedulerPortal]
3. Verrà visualizzata la pagina **Job Action** in cui è possibile configurare ulteriormente il processo. 
   
    ![Pagina Job Action nell'Utilità di pianificazione][JobActionPageInScheduler]

## <a name="a-nameviewjobhistoryaview-the-job-history"></a><a name="ViewJobHistory"></a>Visualizzare la cronologia processo
1. Per visualizzare la cronologia di esecuzione di un processo, inclusi i processi creati con WebJobs SDK, fare clic sul collegamento corrispondente nella colonna **Log** nel pannello dei processi Web. È possibile utilizzare l'icona degli Appunti per copiare l'URL della pagina del file di log negli Appunti, se lo si desidera.
   
    ![Collegamento Log](./media/web-sites-create-web-jobs/wjbladelogslink.png)
2. Se si fa clic sul collegamento, viene visualizzata la pagina dei dettagli per il processo Web. In questa pagina è indicato il nome del comando eseguito, le ore delle ultime esecuzioni e il relativo esito positivo o negativo. In **Recent job runs**fare clic su un'ora per visualizzare ulteriori dettagli.
   
    ![WebJobDetails][WebJobDetails]
3. Verrà visualizzata la pagina **WebJob Run Details** . Fare clic su **Toggle Output** per visualizzare il testo dei contenuti del log. Il log di output è in formato testo. 
   
    ![Dettagli dell'esecuzione del processo Web][WebJobRunDetails]
4. Per visualizzare il testo dell'output in un'altra finestra del browser, fare clic sul collegamento **download** . Per scaricare il testo stesso, fare clic con il pulsante destro del mouse sul collegamento e utilizzare le opzioni del browser in uso per salvare il contenuto del file.
   
    ![Download dell'output del log][DownloadLogOutput]
5. Il collegamento **Processi Web** nella parte superiore della pagina offre un modo pratico per ottenere un elenco di processi Web nel dashboard della cronologia.
   
    ![Collegamento all'elenco dei processi Web][WebJobsLinkToDashboardList]
   
    ![Elenco dei processi nel dashboard della cronologia][WebJobsListInJobsDashboard]
   
    Quando si fa clic su questi collegamenti, si viene reindirizzati alla pagina WebJob Details per il processo selezionato.

## <a name="a-namewhpnotesanotes"></a><a name="WHPNotes"></a>Note
* Le app Web in modalità gratuita possono scadere dopo 20 minuti, se non vengono inviate richieste al sito scm (distribuzione) e se il portale dell'app Web non è aperto in Azure. Le richieste al sito effettivo non comportano la reimpostazione del timeout.
* Il codice per un processo continuo deve essere scritto per l'esecuzione in un ciclo infinito.
* I processi continui vengono eseguiti in modo continuo solo quando l'app è attiva.
* Le modalità di base e standard offrono la funzionalità Sempre attivata che, se abilitata, impedisce alle app Web di diventare inattive.
* È possibile solo eseguire il debug di processi Web con esecuzione continua. Il debug di processi Web pianificati o su richiesta non è supportato.

## <a name="a-namenextstepsanext-steps"></a><a name="NextSteps"></a>Passaggi successivi
Per altre informazioni, vedere [Risorse consigliate per i processi Web di Azure][WebJobsRecommendedResources].

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




<!--HONumber=Nov16_HO4-->


