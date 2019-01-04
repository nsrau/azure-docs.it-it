---
title: Inoltrare i dati di report della configurazione dello stato di Automazione di Azure a Log Analytics
description: Questo articolo illustra come inviare i dati di report DSC (Desired State Configuration) dalla configurazione dello stato di Automazione di Azure a Log Analytics per fornire informazioni e funzionalità di gestione aggiuntive.
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88805f26869ad75830cef1aa074cd90cb947e76f
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52681739"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-log-analytics"></a>Inoltrare i dati di report della configurazione dello stato di Automazione di Azure a Log Analytics

Configurazione dello stato di Automazione di Azure può inviare i dati dello stato del nodo Desired State Configuration (DSC) nell'area di lavoro di Log Analytics. Lo stato della conformità è visibile nel portale di Azure o tramite PowerShell per i nodi e per le singole risorse DSC nelle configurazioni dei nodi. Con Log Analytics è possibile:

- Ottenere le informazioni sulla conformità per i nodi gestiti e le singole risorse
- Attivare un messaggio di posta elettronica o un avviso in base allo stato della conformità
- Scrivere query avanzate nei nodi gestiti
- Correlare lo stato della conformità negli account di Automazione
- Visualizzare la cronologia della conformità dei nodi nel tempo

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a inviare i report di configurazione dello stato di Automazione a Log Analytics, sono necessari gli elementi seguenti:

- La versione di novembre 2016 o versioni successive di [Azure PowerShell](/powershell/azure/overview) (v 2.3.0).
- Un account di automazione di Azure. Per altre informazioni, vedere [Introduzione ad Automazione di Azure](automation-offering-get-started.md)
- Un'area di lavoro di Log Analytics con un'offerta di servizio **Automazione e controllo**. Per altre informazioni, vedere [Introduzione a Log Analytics](../log-analytics/log-analytics-get-started.md).
- Almeno un nodo di configurazione dello stato di Automazione di Azure. Per altre informazioni, vedere [Caricamento di computer per la gestione della configurazione dello stato di Automazione di Azure](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-log-analytics"></a>Configurare l'integrazione con Log Analytics

Per iniziare a importare i dati da Automation DSC per Azure in Log Analytics, seguire questa procedura:

1. Accedere all'account Azure in PowerShell. Vedere [Log in with Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0) (Accedere con Azure PowerShell)
1. Ottenere il valore _ResourceId_ dell'account di Automazione eseguendo questo comando di PowerShell. Se sono presenti più account di Automazione, scegliere il valore _ResourceID_ per l'account da configurare.

  ```powershell
  # Find the ResourceId for the Automation Account
  Get-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
  ```

1. Ottenere il valore _ResourceId_ dell'area di lavoro di Log Analytics eseguendo questo comando di PowerShell. Se sono presenti più aree di lavoro, scegliere il valore _ResourceID_ per l'area di lavoro da configurare.

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Get-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
  ```

1. Eseguire questo comando di PowerShell, sostituendo `<AutomationResourceId>` e `<WorkspaceResourceId>` con i valori _ResourceId_ ottenuti nei passaggi precedenti:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
  ```

Se si vuole interrompere l'importazione dei dati dalla configurazione dello stato di Automazione di Azure per Azure in Log Analytics, eseguire questo comando di PowerShell:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Visualizzare i log di configurazione dello stato

Dopo la configurazione dell'integrazione con Log Analytics per i dati della configurazione dello stato di Automazione, un pulsante **Ricerca log** verrà visualizzato nel pannello **Nodi DSC** dell'account di Automazione. Fare clic sul pulsante **Ricerca log** per visualizzare i log per i dati dei nodi DSC.

![Pulsante Ricerca log](media/automation-dsc-diagnostics/log-search-button.png)

Viene visualizzato il pannello **Ricerca log**, che include un'operazione **DscNodeStatusData** per ogni nodo di configurazione dello stato e un'operazione **DscResourceStatusData** per ogni [risorsa DSC](/powershell/dsc/resources) chiamata nella configurazione del nodo applicata a tale nodo.

L'operazione **DscResourceStatusData** contiene informazioni sugli errori per qualsiasi risorsa DSC per cui si sono verificati errori.

Fare clic su ogni operazione nell'elenco per visualizzare i dati per tale operazione.

È anche possibile visualizzare i log [eseguendo ricerche in Log Analytics. Vedere [Trovare dati tramite ricerche nei log](../log-analytics/log-analytics-log-searches.md).
Digitare la query seguente per trovare i log della configurazione dello stato: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

È anche possibile perfezionare la query in base al nome dell'operazione. Ad esempio: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Inviare un messaggio di posta elettronica in caso di errore di un controllo della conformità alla configurazione dello stato

Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con una configurazione DSC.

Per creare una regola di avviso, è necessario creare prima di tutto una ricerca nei log per i record del report della configurazione dello stato che dovrà richiamare l'avviso. Fare clic su pulsante **+ Nuova regola di avviso** per creare e configurare la regola di avviso.

1. Dalla pagina della panoramica di Log Analytics fare clic su **Ricerca log**.
1. Creare una query di ricerca dei log per l'avviso digitando i criteri di ricerca seguenti nel campo query: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione.  
   Il nome dell'account di Automazione può essere derivato dal campo Risorsa nella ricerca di DscNodeStatusData.  
1. Per aprire la schermata **Crea regola** fare clic su **+ Nuova regola di avviso** nella parte superiore della pagina. Per altre informazioni sulle opzioni per la configurazione dell'avviso, vedere [Creare un avviso](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Trovare le risorse DSC con errori in tutti i nodi

Un vantaggio dell'uso di Log Analytics consiste nella possibilità di cercare controlli con errori nei nodi.
Per trovare tutte le istanze di risorse DSC per cui si sono verificati errori:

1. Dalla pagina della panoramica di Log Analytics fare clic su **Ricerca log**.
1. Creare una query di ricerca dei log per l'avviso digitando i criteri di ricerca seguenti nel campo query: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Visualizzare lo stato cronologico dei nodi DSC

È infine consigliabile visualizzare la cronologia dello stato dei nodi DSC nel tempo.  
È possibile usare questa query per cercare lo stato dei nodi DSC nel tempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Verrà visualizzato un grafico dello stato dei nodi nel tempo.

## <a name="log-analytics-records"></a>Record di Log Analytics

La diagnostica di Automazione di Azure crea due categorie di record in Log Analytics.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Proprietà | DESCRIZIONE |
| --- | --- |
| TimeGenerated |Data e ora dell'esecuzione del controllo della conformità. |
| OperationName |DscNodeStatusData |
| ResultType |Indica se il nodo è conforme. |
| NodeName_s |Nome del nodo gestito. |
| NodeComplianceStatus_s |Indica se il nodo è conforme. |
| DscReportStatus |Indica se il controllo della conformità è stato eseguito correttamente. |
| ConfigurationMode | Indica la modalità di applicazione della configurazione al nodo. I valori possibili sono __"ApplyOnly"__,__"ApplyandMonitior"__ e __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: DSC applica la configurazione e non esegue altre operazioni, a meno che non venga eseguito il push di una nuova configurazione nel nodo di destinazione o in caso di pull di una nuova configurazione da un server. Dopo l'applicazione iniziale di una nuova configurazione, DSC non verifica la deviazione da uno stato configurato in precedenza. DSC prova ad applicare la configurazione fino a quando non viene raggiunto un esito positivo, prima che venga applicata la modalità __ApplyOnly__. </li><li> __ApplyAndMonitor__: Si tratta del valore predefinito. Gestione configurazione locale applica qualsiasi nuova configurazione. Dopo l'applicazione iniziale di una nuova configurazione, in caso di deviazione del nodo di destinazione rispetto allo stato desiderato, DSC segnala la discrepanza nei log. DSC prova ad applicare la configurazione fino a quando non viene raggiunto un esito positivo, prima che venga applicata la modalità __ApplyAndMonitor__.</li><li>__ApplyAndAutoCorrect__: DSC applica qualsiasi nuova configurazione. Dopo l'applicazione iniziale di una nuova configurazione, in caso di deviazione del nodo di destinazione rispetto allo stato desiderato, DSC segnala la discrepanza nei log e quindi applica di nuovo la configurazione corrente.</li></ul> |
| HostName_s | Nome del nodo gestito. |
| IPAddress | Indirizzo IPv4 del nodo gestito. |
| Categoria | DscNodeStatus |
| Risorsa | Nome dell'account di Automazione di Azure. |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| NodeId_g |GUID che identifica il nodo gestito. |
| DscReportId_g |GUID che identifica il report. |
| LastSeenTime_t |Data e ora dell'ultima visualizzazione del report. |
| ReportStartTime_t |Data e ora dell'avvio del report. |
| ReportEndTime_t |Data e ora del completamento del report. |
| NumberOfResources_d |Numero di risorse DSC chiamate nella configurazione applicata al nodo. |
| SourceSystem | Modo in cui Log Analytics ha raccolto i dati. È sempre *Azure* per la diagnostica di Azure. |
| ResourceId |Specifica l'account di Automazione di Azure. |
| ResultDescription | Descrizione per questa operazione. |
| SubscriptionId | ID della sottoscrizione di Azure (GUID) per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID che rappresenta l'ID di correlazione del report di conformità. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Proprietà | DESCRIZIONE |
| --- | --- |
| TimeGenerated |Data e ora dell'esecuzione del controllo della conformità. |
| OperationName |DscResourceStatusData|
| ResultType |Indica se la risorsa è conforme. |
| NodeName_s |Nome del nodo gestito. |
| Categoria | DscNodeStatus |
| Risorsa | Nome dell'account di Automazione di Azure. |
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
| SourceSystem | Modo in cui Log Analytics ha raccolto i dati. È sempre *Azure* per la diagnostica di Azure. |
| ResourceId |Specifica l'account di Automazione di Azure. |
| ResultDescription | Descrizione per questa operazione. |
| SubscriptionId | ID della sottoscrizione di Azure (GUID) per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID che rappresenta l'ID di correlazione del report di conformità. |

## <a name="summary"></a>Summary

L'invio dei dati della configurazione dello stato di Automazione a Log Analytics consente di ottenere informazioni dettagliate migliori sullo stato dei nodi di configurazione dello stato di Automazione tramite queste operazioni:

- Configurando avvisi che segnalino la presenza di un problema
- Usando viste personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato dei processi del runbook e altri indicatori chiave o metriche correlati.  

Log Analytics offre una maggiore visibilità operativa dei dati della configurazione dello stato di Automazione e può consentire di risolvere più rapidamente gli eventi imprevisti.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Configurazione dello stato di Automazione di Azure ](automation-dsc-overview.md)
- Per iniziare, vedere [Introduzione alla configurazione dello stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per vedere un esempio dell'uso della configurazione dello stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Distribuzione continua tramite la configurazione dello stato di Automazione di Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
- Per altre informazioni su come creare query di ricerca diverse ed esaminare i log della configurazione dello stato di Automazione con Log Analytics, vedere [Ricerche log in Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Per altre informazioni su Log Analytics e sulle origini di raccolta di dati, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md)