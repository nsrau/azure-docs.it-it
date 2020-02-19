---
title: Inviare i dati dei report di configurazione stato di automazione di Azure ai log di monitoraggio di Azure
description: Questo articolo illustra come inviare i dati di Reporting DSC (Desired state Configuration) dalla configurazione dello stato di automazione di Azure ai log di monitoraggio di Azure per offrire informazioni e gestione aggiuntive.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 578fcf4cd03a2d4fc8400b9e84f53206750a588c
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430721"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Inviare i dati dei report di configurazione stato di automazione di Azure ai log di monitoraggio di Azure

La configurazione dello stato di automazione di Azure mantiene i dati sullo stato dei nodi per 30 giorni.
È possibile inviare i dati sullo stato del nodo all'area di lavoro Log Analytics se si preferisce conservare questi dati per un periodo di tempo più lungo.
Lo stato della conformità è visibile nel portale di Azure o tramite PowerShell per i nodi e per le singole risorse DSC nelle configurazioni dei nodi.
Con i log di monitoraggio di Azure è possibile:

- Ottenere le informazioni sulla conformità per i nodi gestiti e le singole risorse
- Attivare un messaggio di posta elettronica o un avviso in base allo stato della conformità
- Scrivere query avanzate nei nodi gestiti
- Correlare lo stato della conformità negli account di Automazione
- Visualizzare la cronologia della conformità dei nodi nel tempo

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a inviare i report di configurazione dello stato di automazione ai log di monitoraggio di Azure, è necessario:

- La versione di novembre 2016 o versioni successive di [Azure PowerShell](/powershell/azure/overview) (v 2.3.0).
- Un account di automazione di Azure. Per altre informazioni, vedere [Introduzione ad automazione di Azure](automation-intro.md).
- Un'area di lavoro Log Analytics con un'offerta di servizio di controllo & automazione. Per altre informazioni, vedere [Introduzione a Log Analytics in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- Almeno un nodo di configurazione dello stato di Automazione di Azure. Per altre informazioni, vedere Caricamento [di computer per la gestione mediante la configurazione dello stato di automazione di Azure](automation-dsc-onboarding.md).
- Il modulo [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) , versione 2.7.0.0 o successiva. Per la procedura di installazione, vedere [risolvere i problemi di automazione di Azure desired state Configuration](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurare l'integrazione con i log di monitoraggio di Azure

Per iniziare a importare i dati da Azure Automation DSC nei log di monitoraggio di Azure, completare i passaggi seguenti:

1. Accedere all'account Azure in PowerShell. Vedere [accedere con Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Ottenere l'ID risorsa dell'account di automazione eseguendo il cmdlet di PowerShell seguente. Se si dispone di più di un account di automazione, scegliere l'ID risorsa per l'account che si desidera configurare.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Ottenere l'ID risorsa dell'area di lavoro di Log Analytics eseguendo il cmdlet di PowerShell seguente. Se si dispone di più di un'area di lavoro, scegliere l'ID risorsa per l'area di lavoro che si desidera configurare.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Eseguire il cmdlet di PowerShell seguente, sostituendo `<AutomationResourceId>` e `<WorkspaceResourceId>` con i valori *resourceId* di ognuno dei passaggi precedenti.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Se si vuole interrompere l'importazione dei dati dalla configurazione dello stato di automazione di Azure nei log di monitoraggio di Azure, eseguire il cmdlet di PowerShell seguente.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Visualizzare i log di configurazione dello stato

Dopo aver configurato l'integrazione con i log di monitoraggio di Azure per i dati di configurazione dello stato di automazione, è possibile visualizzarli selezionando i **log** nella sezione **monitoraggio** nel riquadro sinistro della pagina configurazione stato (DSC).

![Registri](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Il riquadro **Ricerca log** si apre con un'area di query con ambito alla risorsa dell'account di automazione. È possibile cercare i log di configurazione dello stato per le operazioni DSC eseguendo una ricerca nei log di monitoraggio di Azure. I record per le operazioni DSC vengono archiviati nella tabella AzureDiagnostics. Per individuare i nodi non conformi, ad esempio, digitare la query seguente.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Dettagli filtro:

* Filtrare in *DscNodeStatusData* per restituire le operazioni per ogni nodo di configurazione dello stato.
* Filtrare in *DscResourceStatusData* per restituire le operazioni per ogni risorsa DSC chiamata nella configurazione del nodo applicata a tale risorsa. 
* Filtrare in *DscResourceStatusData* per restituire informazioni sugli errori per qualsiasi risorsa DSC che abbia esito negativo.

Per altre informazioni sulla creazione di query di log per trovare i dati, vedere [Panoramica delle query di log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Inviare un messaggio di posta elettronica in caso di errore di un controllo della conformità alla configurazione dello stato

Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con una configurazione DSC.

Per creare una regola di avviso, iniziare creando una ricerca log per i record del report di configurazione dello stato che devono richiamare l'avviso. Fare clic su pulsante **+ Nuova regola di avviso** per creare e configurare la regola di avviso.

1. Nella pagina Panoramica dell'area di lavoro Log Analytics fare clic su **log**.
1. Creare una query di ricerca dei log per l'avviso digitando i criteri di ricerca seguenti nel campo query: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione. Derivare il nome dell'account di automazione dal campo risorsa nella ricerca di DscNodeStatusData.
1. Per aprire la schermata **Crea regola** fare clic su **+ Nuova regola di avviso** nella parte superiore della pagina. 

Per ulteriori informazioni sulle opzioni per la configurazione dell'avviso, vedere [creare una regola di avviso](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Trovare le risorse DSC con errori in tutti i nodi

Un vantaggio dell'uso dei log di monitoraggio di Azure è che è possibile cercare i controlli non riusciti tra i nodi.
Per trovare tutte le istanze delle risorse DSC non riuscite:

1. Nella pagina Panoramica dell'area di lavoro Log Analytics fare clic su **log**.
1. Creare una query di ricerca dei log per l'avviso digitando i criteri di ricerca seguenti nel campo query: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Visualizzare lo stato cronologico dei nodi DSC

Per visualizzare la cronologia dello stato del nodo DSC nel tempo, è possibile usare questa query:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Questa query consente di visualizzare un grafico dello stato del nodo nel tempo.

## <a name="azure-monitor-logs-records"></a>Record di log di monitoraggio di Azure

Diagnostica di automazione di Azure crea due categorie di record nei log di monitoraggio di Azure:

* Dati sullo stato del nodo (DscNodeStatusData)
* Dati sullo stato delle risorse (DscResourceStatusData)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora dell'esecuzione del controllo della conformità. |
| OperationName |DscNodeStatusData. |
| ResultType |Indica se il nodo è conforme. |
| NodeName_s |Nome del nodo gestito. |
| NodeComplianceStatus_s |Indica se il nodo è conforme. |
| DscReportStatus |Indica se il controllo della conformità è stato eseguito correttamente. |
| ConfigurationMode | Indica la modalità di applicazione della configurazione al nodo. I valori possibili sono: <ul><li>*ApplyOnly*: DSC applica la configurazione e non esegue altre operazioni, a meno che non venga eseguito il push di una nuova configurazione nel nodo di destinazione o in caso di pull di una nuova configurazione da un server. Dopo l'applicazione iniziale di una nuova configurazione, DSC non verifica l'eventuale desincronizzazione da uno stato configurato in precedenza. DSC tenta di applicare la configurazione fino a quando non ha esito positivo prima che il valore *ApplyOnly* abbia effetto. </li><li>*ApplyAndMonitor*: valore predefinito. Gestione configurazione locale applica tutte le nuove configurazioni. Dopo l'applicazione iniziale di una nuova configurazione, in caso di deviazione del nodo di destinazione rispetto allo stato desiderato, DSC segnala la discrepanza nei log. DSC tenta di applicare la configurazione fino a quando non ha esito positivo prima che il valore *ApplyAndMonitor* abbia effetto.</li><li>*ApplyAndAutoCorrect*: DSC applica qualsiasi nuova configurazione. Dopo l'applicazione iniziale di una nuova configurazione, in caso di deviazione del nodo di destinazione rispetto allo stato desiderato, DSC segnala la discrepanza nei log e quindi applica di nuovo la configurazione corrente.</li></ul> |
| HostName_s | Nome del nodo gestito. |
| IPAddress | Indirizzo IPv4 del nodo gestito. |
| Category | DscNodeStatus. |
| Resource | Nome dell'account di Automazione di Azure. |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| NodeId_g |GUID che identifica il nodo gestito. |
| DscReportId_g |GUID che identifica il report. |
| LastSeenTime_t |Data e ora dell'ultima visualizzazione del report. |
| ReportStartTime_t |Data e ora dell'avvio del report. |
| ReportEndTime_t |Data e ora del completamento del report. |
| NumberOfResources_d |Numero di risorse DSC chiamate nella configurazione applicata al nodo. |
| SourceSystem | Modalità di registrazione dei dati raccolti da monitoraggio di Azure. Sempre "Azure" per diagnostica di Azure. |
| ResourceId |Identificatore dell'account di automazione di Azure. |
| ResultDescription | Descrizione dell'operazione. |
| SubscriptionId | ID sottoscrizione di Azure (GUID) per l'account di automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | Microsoft. Automazione. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID che rappresenta l'identificatore di correlazione del report di conformità. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora dell'esecuzione del controllo della conformità. |
| OperationName |DscResourceStatusData|
| ResultType |Indica se la risorsa è conforme. |
| NodeName_s |Nome del nodo gestito. |
| Category | DscNodeStatus. |
| Resource | Nome dell'account di Automazione di Azure. |
| Tenant_g | GUID che identifica il tenant del chiamante. |
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
| SourceSystem | Modalità di registrazione dei dati raccolti da monitoraggio di Azure. È sempre *Azure* per la diagnostica di Azure. |
| ResourceId |Specifica l'account di Automazione di Azure. |
| ResultDescription | Descrizione dell'operazione. |
| SubscriptionId | ID sottoscrizione di Azure (GUID) per l'account di automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | Microsoft. Automazione. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID che rappresenta l'ID di correlazione del report di conformità. |

## <a name="summary"></a>Riepilogo

Inviando i dati di configurazione dello stato di automazione ai log di monitoraggio di Azure, è possibile ottenere informazioni più dettagliate sullo stato dei nodi di configurazione dello stato di automazione per:

- Configurando avvisi che segnalino la presenza di un problema
- Usando viste personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato dei processi del runbook e altri indicatori chiave o metriche correlati.

Log di monitoraggio di Azure offre una maggiore visibilità operativa per i dati di configurazione dello stato di automazione e consente di risolvere più rapidamente gli eventi imprevisti.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Configurazione dello stato di Automazione di Azure ](automation-dsc-overview.md)
- Per iniziare, vedere [Introduzione a Configurazione stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
- Per altre informazioni su come creare query di ricerca diverse ed esaminare i log di configurazione dello stato di automazione con i log di monitoraggio di Azure, vedere [ricerche log nei log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md)
- Per altre informazioni sui log di monitoraggio di Azure e sulle origini di raccolta dati, vedere [raccolta di dati di archiviazione di Azure in log di monitoraggio di Azure Panoramica](../azure-monitor/platform/collect-azure-metrics-logs.md)
