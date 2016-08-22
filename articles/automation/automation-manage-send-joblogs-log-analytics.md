<properties
    pageTitle="Inoltrare lo stato e i flussi del processo da Automazione a Log Analytics (OMS) | Microsoft Azure"
    description="Questo articolo illustra come inviare lo stato e i flussi del processo del runbook a Log Analytics di Microsoft Operations Management Suite per fornire informazioni e funzionalità di gestione aggiuntive."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor="tysonn" />  
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="08/08/2016"
    ms.author="magoedte" />

# Inoltrare lo stato e i flussi del processo da Automazione a Log Analytics (OMS)

Automazione può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics di Microsoft Operations Management Suite (OMS). Anche se è possibile visualizzare queste informazioni nel portale di Azure o con PowerShell in base allo stato del singolo processo o di tutti i processi per un account di Automazione specifico, qualsiasi operazione avanzata per supportare i requisiti operativi richiede la creazione di script di PowerShell personalizzati. Con Log Anaytics ora è possibile:

- Ottenere informazioni dettagliate sui processi di Automazione.
- Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso.
- Scrivere query avanzate nei flussi del processo.
- Correlare i processi tra account di Automazione.
- Visualizzare la cronologia dei processi nel tempo.

## Prerequisiti e considerazioni sulla distribuzione

Per iniziare a inviare i log di Automazione a Log Analytics, sono necessari gli elementi seguenti:

1. Una sottoscrizione di OMS. Per altre informazioni, vedere [Introduzione a Log Analytics](../log-analytics/log-analytics-get-started.md).

    >[AZURE.NOTE]Per il corretto funzionamento di questa configurazione, l'area di lavoro di OMS e l'account di Automazione devono trovarsi nella stessa sottoscrizione di Azure.
  
2. Un [account di archiviazione di Azure](../storage/storage-create-storage-account.md).
   
    >[AZURE.NOTE]L'account di archiviazione *deve* trovarsi nella stessa area dell'account di Automazione.
 
3. Azure PowerShell con versione 1.0.8 o successiva dei cmdlet di Operational Insights. Per informazioni su questa versione e come installarla, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
4. Diagnostica di Azure e PowerShell per Log Analytics. Per altre informazioni su questa versione e su come installarla, vedere [ AzureDiagnosticsAndLogAnalytics 0.1](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1).
5. Scaricare lo script di PowerShell **Enable AzureDiagnostics.ps1** da [PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript). Lo script configura le impostazioni seguenti:
 - Un account di archiviazione per contenere lo stato del processo e i dati del flusso del runbook per un account di Automazione specificato.
 - Abilitare la raccolta dei dati dall'account di Automazione per archiviarli in un account di archiviazione BLOB di Azure nel formato JSON.
 - Configurare la raccolta dei dati dall'account di archiviazione BLOB per Log Analytics di OMS.
 - Abilitare la soluzione Log Analytics di Automazione nell'area di lavoro di OMS.

Durante l'esecuzione dello script **Enable-AzureDiagnostics.ps1** sono richiesti i parametri seguenti:

- *AutomationAccountName*: nome dell'account di Automazione.
- *LogAnalyticsWorkspaceName*: nome dell'area di lavoro di OMS.

Per trovare i valori per *AutomationAccountName*, nel portale di Azure selezionare l'account di Automazione dal pannello **Account di Automazione** e selezionare **Tutte le impostazioni**. Nel pannello **Tutte le impostazioni** selezionare **Proprietà** in **Impostazioni account**. Nel pannello **Proprietà** è possibile prendere nota di questi valori.<br>![Proprietà dell'account di Automazione](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).


## Configurare l'integrazione con Log Analytics

1. Nel computer locale avviare **Windows PowerShell** dalla schermata **Start**.
2. Dalla shell della riga di comando di PowerShell passare alla cartella che contiene lo script scaricato ed eseguirlo modificando i valori per i parametri *-AutomationAccountName* e *-LogAnalyticsWorkspaceName*.

    >[AZURE.NOTE] Verrà richiesto di autenticarsi con Azure dopo aver eseguito lo script. È **necessario** accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
    
        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `

3. Dopo aver eseguito questo script, vengono visualizzati i record in Log Analytics circa 30 minuti dopo la scrittura dei nuovi dati di diagnostica nell'archivio. Se i record non sono disponibili dopo questo periodo di tempo, fare riferimento alla sezione sulla risoluzione dei problemi di configurazione in [JSON files in blob storage](../log-analytics/log-analytics-azure-storage-json.md#troubleshooting-configuration-for-azure-diagnostics-written-to-blob-in-json) (File JSON nell'archivio BLOB).

### Verificare la configurazione

Per verificare che lo script abbia configurato correttamente l'account di Automazione e l'area di lavoro di OMS, è possibile seguire questa procedura in PowerShell. Prima di procedere, per trovare i valori per il nome dell'area di lavoro di OMS e il nome del gruppo di risorse, nel portale di Azure passare a Log Analytics (OMS) e nel pannello Log Analytics (OMS) prendere nota del valore per **Nome** e **Gruppo di risorse**.<br>. ![Elenco nell'area di lavoro di Log Analytics OMS](media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png) Questi due valori saranno usati quando si verifica la configurazione nell'area di lavoro di OMS usando il cmdlet di PowerShell [Get-AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/library/mt603567.aspx).

1.  Nel portale di Azure passare ad Account di archiviazione e cercare l'account di archiviazione seguente che usa la convenzione di denominazione: *AutomationAccountNameomsstorage*. Subito dopo il completamento di un processo del runbook verranno visualizzati i due contenitori BLOB: **insights-log-joblogs** e **insights-log-jobstreams**.

2.  In PowerShell eseguire il codice di PowerShell seguente, modificando i valori per i parametri **ResourceGroupName** e **WorkspaceName** copiati o annotati in precedenza.

    Login-AzureRmAccount Get-AzureRmSubscription -SubscriptionName 'NomeSottoscrizione' | Set-AzureRmContext Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "NomeGruppoDiRisorseOMS" ` -Workspace "NomeAreaDiLavoroOMS"

    Verranno restituite le informazioni di archiviazione per l'area di lavoro di OMS specificata. Si vuole verificare che le informazioni di archiviazione per l'account di Automazione specificate in precedenza siano presenti e che l'oggetto **State** mostri un valore **OK**.<br> ![Risultati del cmdlet Get-AzureRmOperationalInsightsStorageInsights](media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png).

## Visualizzazione dei log di Automazione in Log Analytics 

Dopo avere avviato l'invio di log del processo di automazione a Log Analytics, si vedrà quali operazioni è possibile eseguire con questi log in OMS.

### Inviare un messaggio di posta elettronica quando un processo del runbook non riesce o viene sospeso 

Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con un processo del runbook.

Per creare una regola di avviso, è necessario creare prima di tutto una ricerca nei log per trovare i record del processo del runbook che dovranno richiamare l'avviso. Il pulsante **Avviso** diventerà disponibile per poter creare e configurare la regola di avviso.

1.	Dalla pagina di panoramica di OMS fare clic su **Ricerca log**.
2.	Creare una query di ricerca dei log per l'avviso digitando quanto segue nel campo query: `Category=JobLogs (ResultType=Failed || ResultType=Suspended)`. È anche possibile raggruppare per RunbookName usando: `Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`.
  
    Se son stati configurati log da più di un account di automazione o una sottoscrizione di Azure nell'area di lavoro, potrebbe essere utile raggruppare gli avvisi per sottoscrizione o account di Automazione. Il nome dell'account di automazione può essere derivato dal campo Risorsa nella ricerca di JobLogs.

3.	Fare clic su **Avviso** nella parte superiore della pagina per aprire la schermata **Aggiungi regola di avviso**. Per altre informazioni sulle opzioni per configurare l'avviso, vedere [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule).

### Trovare tutti i processi completati con errori 

Oltre agli avvisi basati sugli errori, è probabile che si voglia sapere quando si è verificato un errore non irreversibile per un processo del runbook. PowerShell genera un flusso di errore, ma gli errori non irreversibili non causano la sospensione o l'esito negativo del processo.

1. Nel portale di OMS fare clic su **Ricerca log**.
2. Nel campo delle query digitare `Category=JobStreams StreamType_s=Error | measure count() by JobId_g` e quindi fare clic su **Search**.


### Visualizzare flussi del processo per un processo  

Quando si esegue il debug di un processo, è consigliabile esaminarne anche i flussi. La query seguente illustra tutti i flussi per un singolo processo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`  

### Visualizzare lo stato cronologico del processo 

Infine, è consigliabile visualizzare la cronologia dei processi nel tempo. È possibile usare questa query per cercare lo stato dei processi nel tempo.

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day` <br> ![Grafico dello stato cronologico del processo OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## Riepilogo

Inviando lo stato del processo e i flussi del processo di Automazione a Log Analytics, è possibile ottenere informazioni più dettagliate dello stato dei processi di Automazione per configurare avvisi che notifichino all'utente quando è presente un problema, nonché dashboard personalizzati usando query avanzate per visualizzare i risultati del runbook, lo stato del processo del runbook e altri indicatori chiave o metriche correlate. Questo consente di fornire una più ampia visibilità operativa e affrontare più velocemente gli eventi imprevisti.


## Passaggi successivi

- Per alte informazioni su come creare query di ricerca diverso ed esaminare i log del processo di automazione con Log Analytics, vedere [Ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Per informazioni su come creare e recuperare l'output e i messaggi di errore da runbook, vedere [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md).
- Per maggiori informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere come tenere traccia del processo di un runbook in [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md)
- Per altre informazioni su Log Analytics di OMS e sulle origini di raccolta dati, vedere la [panoramica della raccolta dati di Archiviazione di Azure in Log Analytics](../log-analytics/log-analytics-azure-storage.md)

<!---HONumber=AcomDC_0810_2016-->