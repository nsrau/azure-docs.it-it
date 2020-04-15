---
title: Inoltrare i dati dei report di Configurazione stato di automazione di Azure ai log di Monitoraggio di AzureForward Azure Automation State Configuration reporting data to Azure Monitor logs
description: Questo articolo illustra come inviare i dati dei report DSC (Desired State Configuration) dalla configurazione dello stato di automazione di Azure ai log di Monitoraggio di Azure per fornire ulteriori informazioni e gestione.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a75b71d43b072d366ef2fcb15bf4c901680d48fb
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383224"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Inoltrare i dati dei report di Configurazione stato di automazione di Azure ai log di Monitoraggio di AzureForward Azure Automation State Configuration reporting data to Azure Monitor logs

La configurazione dello stato di automazione di Azure mantiene i dati sullo stato dei nodi per 30 giorni. È possibile inviare i dati sullo stato del nodo all'area di lavoro di Log Analytics se si preferisce conservare questi dati per un periodo più lungo. Lo stato della conformità è visibile nel portale di Azure o tramite PowerShell per i nodi e per le singole risorse DSC nelle configurazioni dei nodi. 

I log di Monitoraggio di Azure offrono una maggiore visibilità operativa ai dati di configurazione dello stato di automazione e consentono di risolvere più rapidamente gli incidenti. Con i log di Monitoraggio di Azure è possibile:With Azure Monitor logs you can:

- Ottenere informazioni sulla conformità per i nodi gestiti e le singole risorse.
- Attivare un messaggio di posta elettronica o un avviso in base allo stato di conformità.
- Scrivere query avanzate tra i nodi gestiti.
- Correlare lo stato di conformità tra gli account di automazione.
- Usa visualizzazioni personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato del processo del runbook e altri indicatori o metriche chiave correlati.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a inviare i report di configurazione dello stato di automazione ai log di Monitoraggio di Azure, è necessario:To start sending your Automation State Configuration reports to Azure Monitor logs, you need:

- La versione di novembre 2016 o versioni successive di [Azure PowerShell](/powershell/azure/overview) (v 2.3.0).
- Un account di automazione di Azure. Per altre informazioni, vedere [Introduzione all'automazione](automation-intro.md)di Azure.For more information, see An introduction to Azure Automation.
- Un'area di lavoro di Log Analytics con un'offerta di servizio controllo di automazione &. Per altre informazioni, vedere [Introduzione a Log Analytics in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- Almeno un nodo di configurazione dello stato di Automazione di Azure. Per altre informazioni, vedere [Onboarding di macchine per la gestione in base alla configurazione dello stato](automation-dsc-onboarding.md)di automazione di Azure.For more information, see Onboarding machines for management by Azure Automation State Configuration .
- Modulo [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) versione 2.7.0.0 o successiva. Per la procedura di installazione, vedere [Risolvere i problemi relativi alla configurazione dello stato desiderato di Automazione di Azure.For](./troubleshoot/desired-state-configuration.md)installation steps, see Troubleshoot Azure Automation Desired State Configuration .

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurare l'integrazione con i log di Monitoraggio di AzureSet up integration with Azure Monitor logs

Per iniziare a importare dati dalla configurazione dello stato di automazione di Azure nei log di Monitoraggio di Azure, completare i passaggi seguenti:To begin importing data from Azure Automation State Configuration into Azure Monitor logs, complete the following steps:

1. Accedere all'account Azure in PowerShell. Vedere [Accedere con Azure PowerShell.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. Ottenere l'ID risorsa dell'account di automazione eseguendo il cmdlet PowerShell seguente. Se si dispone di più account di automazione, scegliere l'ID risorsa per l'account che si desidera configurare.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Ottenere l'ID risorsa dell'area di lavoro di Log Analytics eseguendo il cmdlet PowerShell seguente. Se si dispone di più aree di lavoro, scegliere l'ID risorsa per l'area di lavoro che si desidera configurare.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Eseguire il cmdlet PowerShell `<AutomationResourceId>` seguente, sostituendo e `<WorkspaceResourceId>` con i `ResourceId` valori di ognuno dei passaggi precedenti.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Se si vuole interrompere l'importazione di dati da Configurazione stato di automazione di Azure nei log di Monitoraggio di Azure, eseguire il cmdlet di PowerShell seguente.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Visualizzare i log di configurazione dello stato

Dopo aver configurato l'integrazione con i log di Monitoraggio di Azure per i dati di configurazione dello stato di automazione, è possibile visualizzarli selezionando **Log** nella sezione **Monitoraggio** nel riquadro sinistro della pagina Configurazione stato (DSC).

![Log](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Viene visualizzato il riquadro Ricerca log con un'area di query con ambito alla risorsa account di automazione. È possibile cercare i log di configurazione dello stato per le operazioni DSC eseguendo una ricerca in Log di Monitoraggio di Azure.You can search the State Configuration logs for DSC operations by search in Azure Monitor logs. I record per le operazioni DSC vengono archiviati nella `AzureDiagnostics` tabella. Ad esempio, per trovare nodi non conformi, digitare la query seguente.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Dettagli filtro:

* Filtrare `DscNodeStatusData` per restituire le operazioni per ogni nodo di configurazione dello stato.
* Filtrare `DscResourceStatusData` per restituire le operazioni per ogni risorsa DSC chiamata nella configurazione del nodo applicata a tale risorsa. 
* Filtrare `DscResourceStatusData` per restituire informazioni sull'errore per tutte le risorse DSC che hanno esito negativo.

Per altre informazioni sulla creazione di query di log per trovare dati, vedere [Panoramica delle query di log in Monitoraggio di Azure.To](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)learn more about constructing log queries to find data, see Overview of log queries in Azure Monitor.

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Inviare un messaggio di posta elettronica in caso di errore di un controllo della conformità alla configurazione dello stato

Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con una configurazione DSC.

Per creare una regola di avviso, iniziare creando una ricerca nel log per i record del report di configurazione dello stato che devono richiamare l'avviso. Fare clic sul pulsante **Nuova regola** di avviso per creare e configurare la regola di avviso.

1. Nella pagina Panoramica dell'area di lavoro di Log Analytics fare clic su **Registri**.
1. Creare una query di ricerca log per l'avviso digitando la ricerca seguente nel campo della query:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione. Derivare il nome `Resource` dell'account di automazione dal campo nella ricerca dei record **DscNodeStatusData.**
1. Per aprire la schermata **Crea regola,** fare clic su **Nuova regola** di avviso nella parte superiore della pagina. 

Per ulteriori informazioni sulle opzioni per configurare l'avviso, vedere [Creare una regola](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)di avviso .

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Trovare le risorse DSC con errori in tutti i nodi

Uno dei vantaggi dell'uso dei log di Monitoraggio di Azure è la ricerca di controlli non riusciti tra i nodi. Per trovare tutte le istanze di risorse DSC non riuscite:

1. Nella pagina Panoramica dell'area di lavoro di Log Analytics fare clic su **Registri**.
1. Creare una query di ricerca dei log per l'avviso digitando i criteri di ricerca seguenti nel campo query: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Visualizzare lo stato cronologico dei nodi DSC

Per visualizzare la cronologia dello stato del nodo DSC nel tempo, è possibile utilizzare questa query:To visualize your DSC node status history over time, you can use this query:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Questa query visualizza un grafico dello stato del nodo nel tempo.

## <a name="azure-monitor-logs-records"></a>Record di log di Monitoraggio di AzureAzure Monitor logs logs records

Diagnostica di automazione di Azure creare due categorie di record nei log di Monitoraggio di Azure:Azure Automation diagnostics create two categories of records in Azure Monitor logs:

* Dati sullo stato del nodo (**DscNodeStatusData**)
* Dati sullo stato delle risorse (**DscResourceStatusData**)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora dell'esecuzione del controllo della conformità. |
| OperationName |DscNodeStatusData.DscNodeStatusData. |
| ResultType |Indica se il nodo è conforme. |
| NodeName_s |Nome del nodo gestito. |
| NodeComplianceStatus_s |Indica se il nodo è conforme. |
| DscReportStatus |Indica se il controllo della conformità è stato eseguito correttamente. |
| ConfigurationMode | Indica la modalità di applicazione della configurazione al nodo. I valori possibili sono: <ul><li>`ApplyOnly`: DSC applica la configurazione e non esegue altre operazioni a meno che non venga eseguito il push di una nuova configurazione nel nodo di destinazione o quando una nuova configurazione viene estratta da un server. Dopo l'applicazione iniziale di una nuova configurazione, DSC non verifica la deviazione da uno stato configurato in precedenza. DSC tenta di applicare la configurazione `ApplyOnly` fino a quando non ha esito positivo prima che il valore abbia effetto. </li><li>`ApplyAndMonitor`: valore predefinito. Gestione configurazione locale applica qualsiasi nuova configurazione. Dopo l'applicazione iniziale di una nuova configurazione, in caso di deviazione del nodo di destinazione rispetto allo stato desiderato, DSC segnala la discrepanza nei log. DSC tenta di applicare la configurazione `ApplyAndMonitor` fino a quando non ha esito positivo prima che il valore abbia effetto.</li><li>`ApplyAndAutoCorrect`: DSC applica tutte le nuove configurazioni. Dopo l'applicazione iniziale di una nuova configurazione, in caso di deviazione del nodo di destinazione rispetto allo stato desiderato, DSC segnala la discrepanza nei log e quindi applica di nuovo la configurazione corrente.</li></ul> |
| HostName_s | Nome del nodo gestito. |
| IPAddress | Indirizzo IPv4 del nodo gestito. |
| Category | Stato dscNodeStatus. |
| Risorsa | Nome dell'account di Automazione di Azure. |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| NodeId_g |GUID che identifica il nodo gestito. |
| DscReportId_g |GUID che identifica il report. |
| LastSeenTime_t |Data e ora dell'ultima visualizzazione del report. |
| ReportStartTime_t |Data e ora dell'avvio del report. |
| ReportEndTime_t |Data e ora del completamento del report. |
| NumberOfResources_d |Numero di risorse DSC chiamate nella configurazione applicata al nodo. |
| SourceSystem | In che modo i log di Monitoraggio di Azure hanno raccolto i dati. Sempre "Azure" per la diagnostica di Azure.Always "Azure" for Azure diagnostics. |
| ResourceId |Identificatore dell'account di Automazione di Azure.Identifier of the Azure Automation account. |
| ResultDescription | Descrizione per questa operazione. |
| SubscriptionId | ID sottoscrizione di Azure (GUID) per l'account di automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | Microsoft. Automazione. |
| ResourceType | CHE SI |
| CorrelationId |GUID che è l'identificatore di correlazione del report di conformità. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora dell'esecuzione del controllo della conformità. |
| OperationName |DscResourceStatusData.DscResourceStatusData.|
| ResultType |Indica se la risorsa è conforme. |
| NodeName_s |Nome del nodo gestito. |
| Category | Stato dscNodeStatus. |
| Risorsa | Nome dell'account di Automazione di Azure. |
| Tenant_g | GUID che identifica il tenant per il chiamante. |
| NodeId_g |GUID che identifica il nodo gestito. |
| DscReportId_g |GUID che identifica il report. |
| DscResourceId_s |Nome dell'istanza della risorsa DSC. |
| DscResourceName_s |Nome della risorsa DSC. |
| DscResourceStatus_s |Indica se la risorsa DSC è conforme. |
| DscModuleName_s |Nome del modulo di PowerShell che include la risorsa DSC. |
| DscModuleVersion_s |Versione del modulo di PowerShell che include la risorsa DSC. |
| DscConfigurationName_s |Nome della configurazione applicata al nodo. |
| ErrorCode_s | Codice errore in caso di esito negativo della risorsa. |
| ErrorMessage_s |Messaggio di errore in caso di esito negativo della risorsa. |
| DscResourceDuration_d |Durata, in secondi, dell'esecuzione della risorsa DSC. |
| SourceSystem | In che modo i log di Monitoraggio di Azure hanno raccolto i dati. Sempre `Azure` per la diagnostica di Azure.Always for Azure diagnostics. |
| ResourceId |Specifica l'account di Automazione di Azure. |
| ResultDescription | Descrizione per questa operazione. |
| SubscriptionId | ID sottoscrizione di Azure (GUID) per l'account di automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | Microsoft. Automazione. |
| ResourceType | CHE SI |
| CorrelationId |GUID che è l'ID di correlazione del report di conformità. |


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere Configurazione dello stato di [automazione](automation-dsc-overview.md)di Azure .
- Per iniziare, vedere Introduzione alla configurazione dello stato di automazione di [Azure.](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC in modo da poterle assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione dello stato](automation-dsc-compile.md)di automazione di Azure.To learn about compiling DSC configurations so that you can assign them to target nodes, see Compilazioning configurations in Azure Automation State Configuration .
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per informazioni sui prezzi, vedere Prezzi per la configurazione dello stato di automazione di Azure.For pricing [information,](https://azure.microsoft.com/pricing/details/automation/)see Azure Automation State Configuration pricing .
- Per un esempio di utilizzo della configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [Distribuzione continua tramite Configurazione dello stato](automation-dsc-cd-chocolatey.md)di automazione di Azure e Chocolatey .
- Per altre informazioni su come creare query di ricerca diverse ed esaminare i log di configurazione dello stato di automazione con i log di Monitoraggio di Azure, vedere Ricerche log nei log di Monitoraggio di Azure.To learn more about how to construct different search queries and review the Automation State Configuration logs with Azure Monitor logs, see [Log searches in Azure Monitor logs.](../log-analytics/log-analytics-log-searches.md)
- Per altre informazioni sui log di Monitoraggio di Azure e sulle origini della raccolta dati, vedere Panoramica della raccolta di dati di archiviazione di Azure in Panoramica dei log di Monitoraggio di Azure.To learn more about Azure Monitor logs and data collection sources, see [Collecting Azure storage data in Azure Monitor logs overview.](../azure-monitor/platform/collect-azure-metrics-logs.md)
