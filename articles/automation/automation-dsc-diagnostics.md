---
title: Integrazione con i log di Monitoraggio di Azure
description: Questo articolo descrive come inviare i dati di report di Desired State Configuration da State Configuration di Automazione di Azure ai log di Monitoraggio di Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 425a7ff0553ddeac502c59e240f5ab152d6e0d79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015154"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integrazione con i log di Monitoraggio di Azure

State Configuration di Automazione di Azure mantiene i dati sullo stato dei nodi per 30 giorni. È possibile inviare i dati sullo stato dei nodi all'area di lavoro Log Analytics se si preferisce mantenere questi dati per un periodo di tempo più lungo. Lo stato della conformità è visibile nel portale di Azure o tramite PowerShell per i nodi e per le singole risorse DSC nelle configurazioni dei nodi. 

I log di Monitoraggio di Azure offrono una maggiore visibilità operativa dei dati di State Configuration di Automazione e possono consentire di risolvere più rapidamente gli eventi imprevisti. Con i log di Monitoraggio di Azure è possibile:

- Ottenere informazioni sulla conformità per nodi gestiti e singole risorse.
- Attivare un messaggio di posta elettronica o un avviso in base allo stato di conformità.
- Scrivere query avanzate in tutti i nodi gestiti.
- Correlare lo stato di conformità negli account di Automazione.
- Usare visualizzazioni personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato dei processi del runbook e altri indicatori chiave o metriche correlati.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a inviare i report di State Configuration di Automazione ai log di Monitoraggio di Azure, sono necessari:

- La versione di novembre 2016 o versioni successive di [Azure PowerShell](/powershell/azure/) (v 2.3.0).
- Un account di automazione di Azure. Per altre informazioni, vedere [Introduzione ad Automazione di Azure](automation-intro.md).
- Un'area di lavoro Log Analytics con un'offerta di servizio di Automazione e controllo. Per altre informazioni, vedere [Introduzione a Log Analytics in Monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md).
- Almeno un nodo di configurazione dello stato di Automazione di Azure. Per altre informazioni, vedere [Onboarding di computer per la gestione tramite State Configuration di Automazione di Azure](automation-dsc-onboarding.md).
- Il modulo [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0), versione 2.7.0.0 o successive. Per la procedura di installazione, vedere [Risolvere i problemi relativi a Desired State Configuration di Automazione di Azure](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurare l'integrazione con i log di Monitoraggio di Azure

Per iniziare a importare i dati da State Configuration di Automazione di Azure nei log di Monitoraggio di Azure, seguire questa procedura:

1. Accedere all'account Azure in PowerShell. Vedere [Accedere con Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Ottenere l'ID risorsa dell'account di Automazione eseguendo il cmdlet di PowerShell seguente. Se si hanno più account di Automazione, scegliere l'ID risorsa per l'account che si vuole configurare.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Ottenere l'ID risorsa dell'area di lavoro Log Analytics eseguendo il cmdlet di PowerShell seguente. Se si hanno più aree di lavoro, scegliere l'ID risorsa per l'area di lavoro che si vuole configurare.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Eseguire il cmdlet di PowerShell seguente, sostituendo `<AutomationResourceId>` e `<WorkspaceResourceId>` con i valori `ResourceId` ottenuti in ognuno dei passaggi precedenti.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Se si vuole interrompere l'importazione dei dati da State Configuration di Automazione di Azure nei log di Monitoraggio di Azure, eseguire il comando di PowerShell seguente.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Visualizzare i log di configurazione dello stato

Dopo aver configurato l'integrazione con i log di Monitoraggio di Azure per i dati di State Configuration di Automazione, è possibile visualizzare tali dati selezionando **Log** nella sezione **Monitoraggio** nel riquadro sinistro della pagina State Configuration (DSC).

![Log](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Il riquadro Ricerca log viene aperto con un'area di query con ambito definito in base alla risorsa dell'account di Automazione. È possibile cercare le operazioni DSC nei log di State Configuration eseguendo la ricerca nei log di Monitoraggio di Azure. I record relativi alle operazioni DSC sono archiviati nella tabella `AzureDiagnostics`. Per trovare ad esempio i nodi non conformi, digitare la query seguente.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Dettagli di filtro:

* Filtrare in base a `DscNodeStatusData` per restituire le operazioni per ogni nodo di State Configuration.
* Filtrare in base a `DscResourceStatusData` per restituire le operazioni per ogni risorsa DSC chiamata nella configurazione del nodo applicata a tale risorsa. 
* Filtrare in base a `DscResourceStatusData` per restituire le informazioni sugli errori per tutte le risorse DSC che hanno esito negativo.

Per altre informazioni sulla creazione di query di log per trovare dati, vedere [Panoramica delle query di log in Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Inviare un messaggio di posta elettronica in caso di errore di un controllo della conformità alla configurazione dello stato

Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con una configurazione DSC.

Per creare una regola di avviso, è necessario creare prima di tutto una ricerca log per i record del report di State Configuration che devono richiamare l'avviso. Fare clic sul pulsante **Nuova regola di avviso** per creare e configurare la regola di avviso.

1. Nella pagina Panoramica dell'area di lavoro Log Analytics fare clic su **Log**.
1. Creare una query di ricerca log per l'avviso digitando la ricerca seguente nel campo della query: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione. Recuperare il nome dell'account di Automazione dal campo `Resource` nella ricerca dei record di `DscNodeStatusData`.
1. Per aprire la schermata **Crea regola**, fare clic su **Nuova regola di avviso** nella parte superiore della pagina. 

Per altre informazioni sulle opzioni per la configurazione dell'avviso, vedere [Creare una regola di avviso](../azure-monitor/platform/alerts-metric.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Trovare le risorse DSC con errori in tutti i nodi

Un vantaggio dell'uso dei log di Monitoraggio di Azure consiste nella possibilità di cercare controlli non riusciti in tutti i nodi. Per trovare tutte le istanze delle risorse DSC non riuscite:

1. Nella pagina Panoramica dell'area di lavoro Log Analytics fare clic su **Log**.
1. Creare una query di ricerca dei log per l'avviso digitando i criteri di ricerca seguenti nel campo query: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Visualizzare lo stato cronologico dei nodi DSC

Per visualizzare la cronologia dello stato dei nodi DSC nel tempo, è possibile usare questa query:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Verrà visualizzato un grafico dello stato dei nodi nel tempo.

## <a name="azure-monitor-logs-records"></a>Record dei log di Monitoraggio di Azure

La diagnostica di Automazione di Azure crea due categorie di record nei log di Monitoraggio di Azure:

* Dati sullo stato del nodo (`DscNodeStatusData`)
* Dati sullo stato della risorsa (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora dell'esecuzione del controllo della conformità. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Valore che indica se il nodo è conforme. |
| NodeName_s |Nome del nodo gestito. |
| NodeComplianceStatus_s |Valore dello stato che specifica se il nodo è conforme. |
| DscReportStatus |Valore dello stato che indica se il controllo di conformità è stato eseguito correttamente. |
| ConfigurationMode | Modalità usata per applicare la configurazione al nodo. I valori possibili sono: <ul><li>`ApplyOnly`: DSC applica la configurazione e non esegue altre operazioni, a meno che non venga eseguito il push di una nuova configurazione nel nodo di destinazione o in caso di pull di una nuova configurazione da un server. Dopo l'applicazione iniziale di una nuova configurazione, DSC non verifica la deviazione da uno stato configurato in precedenza. DSC prova ad applicare la configurazione fino a quando l'operazione non riesce, prima che venga applicata la modalità `ApplyOnly`. </li><li>`ApplyAndMonitor`: Si tratta del valore predefinito. Gestione configurazione locale applica qualsiasi nuova configurazione. Dopo l'applicazione iniziale di una nuova configurazione, in caso di deviazione del nodo di destinazione rispetto allo stato desiderato, DSC segnala la discrepanza nei log. DSC prova ad applicare la configurazione fino a quando l'operazione non riesce, prima che venga applicata la modalità `ApplyAndMonitor`.</li><li>`ApplyAndAutoCorrect`: DSC applica tutte le nuove configurazioni. Dopo l'applicazione iniziale di una nuova configurazione, in caso di deviazione del nodo di destinazione rispetto allo stato desiderato, DSC segnala la discrepanza nei log e quindi applica di nuovo la configurazione corrente.</li></ul> |
| HostName_s | Nome del nodo gestito. |
| IPAddress | Indirizzo IPv4 del nodo gestito. |
| Category | `DscNodeStatus`. |
| Risorsa | Nome dell'account di Automazione di Azure. |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| NodeId_g | GUID che identifica il nodo gestito. |
| DscReportId_g | GUID che identifica il report. |
| LastSeenTime_t | Data e ora dell'ultima visualizzazione del report. |
| ReportStartTime_t | Data e ora dell'avvio del report. |
| ReportEndTime_t | Data e ora del completamento del report. |
| NumberOfResources_d | Numero di risorse DSC chiamate nella configurazione applicata al nodo. |
| SourceSystem | Sistema di origine che identifica il modo in cui i dati sono stati raccolti dai log di Monitoraggio di Azure. Il valore è sempre `Azure` per diagnostica di Azure. |
| ResourceId |Identificatore di risorsa dell'account di Automazione di Azure. |
| ResultDescription | Descrizione della risorsa per questa operazione. |
| SubscriptionId | ID della sottoscrizione di Azure (GUID) per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse per l'account di Automazione. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | GUID che rappresenta l'identificatore di correlazione del report di conformità. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora dell'esecuzione del controllo della conformità. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Indica se la risorsa è conforme. |
| NodeName_s |Nome del nodo gestito. |
| Category | DscNodeStatus. |
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
| SourceSystem | Modalità di raccolta dei dati da parte dei log di Monitoraggio di Azure. Il valore è sempre `Azure` per diagnostica di Azure. |
| ResourceId |Identificatore dell'account di Automazione di Azure. |
| ResultDescription | Descrizione per questa operazione. |
| SubscriptionId | ID della sottoscrizione di Azure (GUID) per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse per l'account di Automazione. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID che rappresenta l'ID di correlazione del report di conformità. |

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Panoramica di State Configuration di Automazione di Azure](automation-dsc-overview.md).
- Per iniziare, vedere [Introduzione a State Configuration di Automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilare configurazioni DSC in State Configuration di Automazione di Azure](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Per informazioni sui prezzi, vedere [Prezzi di State Configuration di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio dell'uso di State Configuration di Automazione di Azure in una pipeline di distribuzione continua, vedere [Configurare la distribuzione continua con Chocolatey](automation-dsc-cd-chocolatey.md).
- Per altre informazioni su come creare query di ricerca diverse e rivedere i log di State Configuration di Automazione con i log di Monitoraggio di Azure, vedere [Ricerche log nei log di Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).
- Per altre informazioni sui log di Monitoraggio di Azure e sulle origini di raccolta dati, vedere [Panoramica della raccolta di dati di archiviazione di Azure nei log di Monitoraggio di Azure](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).
