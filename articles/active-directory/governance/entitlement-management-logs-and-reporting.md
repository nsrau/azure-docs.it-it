---
title: Report di & archiviazione con Monitoraggio di Azure - Gestione dei diritti di Azure ADArchive & report with Azure Monitor - Azure AD entitlement management
description: Informazioni su come archiviare i log e creare report con Monitoraggio di Azure nella gestione dei diritti di Azure Active Directory.Learn how to archive logs and create reports with Azure Monitor in Azure Active Directory entitlement management.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380178"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archiviare i log e la creazione di report sulla gestione dei diritti di Azure AD in Monitoraggio di AzureArchive logs and reporting on Azure AD entitlement management in Azure Monitor

Azure AD archivia gli eventi di controllo per un massimo di 30 giorni nel log di controllo. Tuttavia, è possibile mantenere i dati di controllo per un periodo di conservazione superiore a quello predefinito, descritto in Per quanto tempo Azure AD archivia i dati di report? , instradandoli a un account di archiviazione di Azure o tramite Monitoraggio di Azure.However, you can keep the audit data for longer than the default retention period, outlined in [How long Azure AD store reporting data? ,](../reports-monitoring/reference-reports-data-retention.md)by routing it to an Azure Storage account or using Azure Monitor. È quindi possibile utilizzare cartelle di lavoro e query e report personalizzati su questi dati.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Configurare Azure AD per l'uso di Monitoraggio di AzureConfigure Azure AD to use Azure Monitor
Prima di usare le cartelle di lavoro di Monitoraggio di Azure, è necessario configurare Azure AD per inviare una copia dei log di controllo a Monitoraggio di Azure.Before using the Azure Monitor workbooks, you must configure Azure AD to send a copy of its audit logs to Azure Monitor.

L'archiviazione dei log di controllo di Azure AD richiede la creazione di Monitoraggio di Azure in una sottoscrizione di Azure.Archiving Azure AD audit logs requires you to have Azure Monitor in an Azure subscription. Per altre informazioni sui prerequisiti e sui costi stimati dell'uso di Monitoraggio di Azure [In Azure AD, vedere Monitoraggio di Azure.You](../reports-monitoring/concept-activity-logs-azure-monitor.md)can read more about the prerequisites and estimated costs of using Azure Monitor in Azure AD activity logs in Azure Monitor .

**Ruolo prerequisito**: Amministratore globale

1. Accedere al portale di Azure come utente amministratore globale. Assicurarsi di avere accesso al gruppo di risorse contenente l'area di lavoro di Monitoraggio di Azure.Make sure you have access to the resource group containing the Azure Monitor workspace.
 
1. Selezionare **Azure Active Directory,** quindi fare clic su **Impostazioni di diagnostica** in Monitoraggio nel menu di spostamento sinistro. Verificare se è già presente un'impostazione per inviare i log di controllo a tale area di lavoro.

1. Se non è già presente un'impostazione, fare clic su **Aggiungi impostazione diagnostica**. Usare le istruzioni nell'articolo [Integrare i log](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) di Azure AD con i log di Monitoraggio di Azure per inviare il log di controllo di Azure AD all'area di lavoro di Monitoraggio di Azure.Use the instructions in the article Integrate Azure AD logs with Azure Monitor logs to send the Azure AD audit log to the Azure Monitor workspace.

    ![Riquadro Impostazioni di diagnostica](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Dopo aver inviato il log a Monitoraggio di Azure, selezionare **Aree**di lavoro di Log Analytics e selezionare l'area di lavoro contenente i log di controllo di Azure AD.

1. Selezionare **Utilizzo e costi stimati** e fare clic su **Conservazione dati**. Modificare il dispositivo di scorrimento sul numero di giorni in cui si desidera mantenere i dati per soddisfare i requisiti di controllo.

    ![Riquadro Aree di lavoro di Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Successivamente, per visualizzare l'intervallo di date conservate nell'area di lavoro, è possibile utilizzare la cartella di lavoro Intervallo di date *log archiviati:*  
    
    1. Selezionare **Azure Active Directory,** quindi fare clic su **Cartelle di lavoro**. 
    
    1. Espandere la sezione **Risoluzione dei**problemi di Azure Active Directory e fare clic su Intervallo di date **log archiviati**. 


## <a name="view-events-for-an-access-package"></a>Visualizzare gli eventi per un pacchetto di accessoView events for an access package  

Per visualizzare gli eventi per un pacchetto di accesso, è necessario avere accesso all'area di lavoro di monitoraggio di Azure sottostante (vedere [Gestire l'accesso ai dati di log e alle aree di lavoro in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) per informazioni) e in uno dei ruoli seguenti:To view events for an access package, you must have access to the underlying Azure monitor workspace (see Manage access to log data and workspaces in Azure Monitor for information) and in one of the following roles: 

- Amministratore globale  
- Amministratore della sicurezza  
- Ruolo con autorizzazioni di lettura per la sicurezza  
- Lettore di report  
- Amministratore di applicazioni  

Utilizzare la procedura seguente per visualizzare gli eventi: 

1. Nel portale di Azure selezionare **Azure Active Directory,** quindi fare clic su **Cartelle di lavoro**. Se si dispone di una sola sottoscrizione, andare al passaggio 3.If you have only one subscription, move on to step 3. 

1. Se si dispone di più sottoscrizioni, selezionare la sottoscrizione che contiene l'area di lavoro.  

1. Selezionare la cartella di lavoro denominata *Access Package Activity*. 

1. Nella cartella di lavoro selezionare un intervallo di tempo (modificare in **Tutti** se non è sicuro) e selezionare un ID pacchetto di accesso dall'elenco a discesa di tutti i pacchetti di accesso che hanno avuto attività durante tale intervallo di tempo. Verranno visualizzati gli eventi correlati al pacchetto di accesso che si è verificato durante l'intervallo di tempo selezionato.  

    ![Visualizzare gli eventi del pacchetto di accessoView access package events](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Ogni riga include l'ora, l'ID del pacchetto di accesso, il nome dell'operazione, l'ID oggetto, l'UPN e il nome visualizzato dell'utente che ha avviato l'operazione.  Dettagli aggiuntivi sono inclusi in JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Creare query personalizzate di Monitoraggio di Azure usando il portale di AzureCreate custom Azure Monitor queries using the Azure portal
È possibile creare query personalizzate sugli eventi di controllo di Azure AD, inclusi gli eventi di gestione dei diritti.  

1. In Azure Active Directory del portale di Azure fare clic su **Log** nella sezione Monitoraggio nel menu di spostamento a sinistra per creare una nuova pagina di query.

1. L'area di lavoro deve essere visualizzata nell'angolo superiore sinistro della pagina di query. Se si dispone di più aree di lavoro di Monitoraggio di Azure e l'area di lavoro usata per archiviare gli eventi di controllo di Azure AD non viene visualizzata, fare clic su **Seleziona ambito**. Selezionare quindi la sottoscrizione e l'area di lavoro corrette.

1. Successivamente, nell'area di testo della query, eliminare la stringa "ricerca " e sostituirla con la query seguente:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Successivamente, fare clic su **Esegui**. 

    ![Fare clic su Esegui per avviare la query](./media/entitlement-management-logs-and-reporting/run-query.png)

Nella tabella verranno visualizzati gli eventi del registro di controllo per la gestione dei diritti dell'ultima ora per impostazione predefinita. È possibile modificare l'impostazione "Intervallo di tempo" per visualizzare gli eventi meno recenti. Tuttavia, la modifica di questa impostazione mostrerà solo gli eventi che si sono verificati dopo che Azure AD è stato configurato per inviare eventi a Monitoraggio di Azure.However, changing this setting will only show events that occurred after Azure AD was configured to send events to Azure Monitor.

Se si desidera conoscere gli eventi di controllo meno recenti e più recenti contenuti in Monitoraggio di Azure, usare la query seguente:If you would to know the ldest ldest and newest audit events held in Azure Monitor, use the following query:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Per altre informazioni sulle colonne archiviate per gli eventi di controllo in Monitoraggio di Azure, vedere Interpretare lo schema dei log di controllo di Azure AD in Monitoraggio di Azure.For more information on the columns that are stored for audit events in Azure Monitor, see [Interpret the Azure AD audit logs schema in Azure Monitor.](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Creare query personalizzate di Monitoraggio di Azure usando Azure PowerShellCreate custom Azure Monitor queries using Azure PowerShell

È possibile accedere ai log tramite PowerShell dopo aver configurato Azure AD per l'invio di log a Monitoraggio di Azure.You can access logs through PowerShell after you've configured Azure AD to send logs to Azure Monitor. Quindi, inviare query dagli script o dalla riga di comando di PowerShell, senza che sia un amministratore globale nel tenant. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Verificare che l'utente o l'entità servizio disponga dell'assegnazione di ruolo correttaEnsure the user or service principal has the correct role assignment

Assicurarsi che l'utente o l'entità servizio che eseguirà l'autenticazione in Azure AD si trovi nel ruolo di Azure appropriato nell'area di lavoro di Log Analytics.Sure sure you, the user or service principal that will authenticate to Azure AD, are in the appropriate Azure role in the Log Analytics workspace. The role options are either Log Analytics Reader or the Log Analytics Contributor. Se si è già in uno di questi ruoli, passare a Recuperare log analytics ID con una sottoscrizione di Azure.If you're already in one of those roles, then skip to [Retrieve Log Analytics ID with one Azure subscription](#retrieve-log-analytics-id-with-one-azure-subscription).

Per impostare l'assegnazione di ruolo e creare una query, eseguire la procedura seguente:

1. Nel portale di Azure individuare l'area di [lavoro Log Analytics.](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)

1. Selezionare **Controllo di accesso (IAM)**.

1. Fare quindi clic su **Aggiungi** per aggiungere un'assegnazione di ruolo.

    ![Aggiungi un'assegnazione di ruolo](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Installare il modulo Azure PowerShell

Dopo aver a che fare con l'assegnazione di ruolo appropriata, avviare PowerShell e [installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (se non è già stato fatto), digitando:Once you have the appropriate role assignment, launch PowerShell, and install the Azure PowerShell module (if you haven't already), by typing:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
A questo punto si è pronti per eseguire l'autenticazione in Azure AD e recuperare l'ID dell'area di lavoro di Log Analytics su cui si sta effettuando query.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Recuperare Log Analytics ID con una sottoscrizione di AzureRetrieve Log Analytics ID with one Azure subscription
Se si dispone di una sola sottoscrizione di Azure e di una singola area di lavoro di Log Analytics, digitare quanto segue per eseguire l'autenticazione in Azure AD, connettersi a tale sottoscrizione e recuperare tale area di lavoro:If you have only a single Azure subscription, and a single Log Analytics workspace, then type the following to authenticate to Azure AD, connect to that subscription, and retrieve that workspace:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Recuperare Log Analytics ID con più sottoscrizioni di AzureRetrieve Log Analytics ID with multiple Azure subscriptions

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) opera in una sottoscrizione alla volta. Pertanto, se si dispone di più sottoscrizioni di Azure, è consigliabile assicurarsi di connettersi a quella con l'area di lavoro Log Analytics con i log di Azure AD. 
 
 I cmdlet seguenti visualizzano un elenco di sottoscrizioni e individuano l'ID della sottoscrizione con l'area di lavoro Di Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
È possibile rieseguire l'autenticazione e associare la `Connect-AzAccount –Subscription $subs[0].id`sessione di PowerShell a tale sottoscrizione utilizzando un comando, ad esempio . Per altre informazioni su come eseguire l'autenticazione in Azure da PowerShell, incluso in modo non interattivo, vedere Accedere con Azure PowerShell.To learn more about how to authenticate to Azure from PowerShell, including non-interactively, see [Sign in with Azure PowerShell.](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)

Se nella sottoscrizione sono presenti più aree di lavoro di Log Analytics, il cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) restituisce l'elenco delle aree di lavoro. È quindi possibile trovare quello con i log di Azure AD. Il `CustomerId` campo restituito da questo cmdlet è uguale al valore dell'"ID area di lavoro" visualizzato nel portale di Azure nella panoramica dell'area di lavoro di Log Analytics.The field returned by this cmdlet is the same as the value of the "Workspace Id" displayed in the Azure portal in the Log Analytics workspace overview.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Inviare la query all'area di lavoro di Log Analytics
Infine, dopo aver identificato un'area di lavoro, è possibile usare [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) per inviare una query Kusto a tale area di lavoro. Queste query sono scritte in [Kusto query language](https://docs.microsoft.com/azure/kusto/query/).
 
Ad esempio, è possibile recuperare l'intervallo di date dei record degli eventi di controllo dall'area di lavoro Log Analytics, con i cmdlet di PowerShell per inviare una query come:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

È inoltre possibile recuperare gli eventi di gestione dei diritti usando una query come:You can also retrieve entitlement management events using a query like:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Passaggi successivi:
- [Creare report interattivi con cartelle di lavoro di Monitoraggio di Azure](../../azure-monitor/app/usage-workbooks.md) 

