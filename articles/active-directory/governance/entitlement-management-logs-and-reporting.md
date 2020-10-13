---
title: Archiviare e segnalare con Monitoraggio di Azure - Gestione entitlement di Azure AD
description: Informazioni su come archiviare i log e creare report con Monitoraggio di Azure in gestione entitlement di Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89d6379f3fa41036836288ed5c75fbdaad0031da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88783823"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Log di archiviazione e creazione di report su gestione entitlement di Azure AD in Monitoraggio di Azure

Azure AD archivia gli eventi di controllo per un massimo di 30 giorni nel log di controllo. Tuttavia, è possibile mantenere i dati relativi alle attività di controllo per più tempo rispetto al periodo di conservazione predefinito descritto in [Per quanto tempo Azure AD archivia i dati dei report?](../reports-monitoring/reference-reports-data-retention.md) instradandoli a un account di archiviazione di Azure con Monitoraggio di Azure. È quindi possibile usare le cartelle di lavoro e le query e i report personalizzati in questi dati.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Configurare Azure AD per l'uso di Monitoraggio di Azure
Prima di usare le cartelle di lavoro di Monitoraggio di Azure, è necessario configurare Azure AD per inviare una copia dei log di controllo a Monitoraggio di Azure.

Per archiviare i log di controllo di Azure AD è necessario disporre di Monitoraggio di Azure in una sottoscrizione di Azure. Per altre informazioni sui prerequisiti e sui costi stimati per l'uso di Monitoraggio di Azure, leggere [Log attività di Azure AD in Monitoraggio di Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Ruolo prerequisito**: Amministratore globale

1. Accedere al portale di Azure come utente con ruolo di amministratore globale. Assicurarsi di avere accesso al gruppo di risorse contenente l'area di lavoro Monitoraggio di Azure.
 
1. Selezionare **Azure Active Directory** quindi fare clic su **Impostazioni di diagnostica** in Monitoraggio nel menu di spostamento a sinistra. Verificare se è già presente un'impostazione per inviare i log di controllo all'area di lavoro.

1. Se non è già presente un'impostazione, fare clic su **Aggiungi impostazione di diagnostica**. Usare le istruzioni riportate nell'articolo [Integrare log di Azure AD con i log di monitoraggio di Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) per inviare il log di controllo di Azure AD all'area di lavoro Monitoraggio di Azure.

    ![Riquadro Impostazioni di diagnostica](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Dopo l'invio del log a Monitoraggio di Azure, selezionare **Aree di lavoro Log Analytics**e selezionare l'area di lavoro che contiene i log di controllo di Azure AD.

1. Selezionare **Utilizzo e costi stimati** e fare clic su **Conservazione dei dati**. Modificare il dispositivo di scorrimento in base al numero di giorni per cui si vuole mantenere i dati per soddisfare i requisiti di controllo.

    ![Riquadro Aree di lavoro di Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. In seguito, per visualizzare l'intervallo di date contenute nell'area di lavoro, è possibile usare la cartella di lavoro *Intervallo di date dei log archiviati*:  
    
    1. Selezionare **Azure Active Directory**, quindi fare clic su **Cartelle di lavoro**. 
    
    1. Espandere la sezione **Risoluzione dei problemi di Azure Active Directory** e fare clic su **Intervallo di date del log archiviati**. 


## <a name="view-events-for-an-access-package"></a>Visualizzare gli eventi per un pacchetto di accesso  

Per visualizzare gli eventi per un pacchetto di accesso, è necessario disporre dell'accesso all'area di lavoro Monitoraggio di Azure sottostante (vedere [Gestire l'accesso ai dati di log e alle aree di lavoro in Monitoraggio di Azure](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) per informazioni) e in uno dei ruoli seguenti: 

- Amministratore globale  
- Amministratore della sicurezza  
- Ruolo con autorizzazioni di lettura per la sicurezza  
- Lettore di report  
- Amministratore di applicazioni  

Usare la procedura seguente per visualizzare gli eventi: 

1. Nel portale di Azure, selezionare **Azure Active Directory** e quindi **Cartella di lavoro**. Se è disponibile solo una sottoscrizione, passare al passaggio 3. 

1. Se sono disponibili più sottoscrizioni, selezionare la sottoscrizione che contiene l'area di lavoro.  

1. Selezionare la cartella di lavoro denominata *Attività del pacchetto di accesso*. 

1. Nella cartella di lavoro, selezionare un intervallo di tempo (modificare in **Tutti** se non si è sicuri) e selezionare un ID del pacchetto di accesso nell'elenco a discesa di tutti i pacchetti di accesso con attività durante tale intervallo di tempo. Verranno visualizzati gli eventi correlati al pacchetto di accesso che si sono verificati durante l'intervallo di tempo selezionato.  

    ![Visualizzare gli eventi del pacchetto di accesso](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Ogni riga include l'ora, l'ID del pacchetto di accesso, il nome dell'operazione, l'ID dell'oggetto, il nome dell'entità utente e il nome visualizzato dell'utente che ha avviato l'operazione.  In JSON sono inclusi dettagli aggiuntivi.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Creare query di Monitoraggio di Azure personalizzate usando il portale di Azure
È possibile creare query personalizzate sugli eventi di controllo di Azure AD, inclusi gli eventi di gestione entitlement.  

1. In Azure Active Directory del portale di Azure, fare clic su **Log** nella sezione Monitoraggio del menu di spostamento a sinistra per creare una nuova pagina di query.

1. L'area di lavoro deve essere visualizzata in alto a sinistra della pagina query. Se sono presenti più aree di lavoro di Monitoraggio di Azure e l'area di lavoro usata per archiviare gli eventi di controllo di Azure AD non è visualizzata, fare clic su **Seleziona ambito**. Selezionare quindi la sottoscrizione e l'area di lavoro corrette.

1. Successivamente, nell'area di testo della query, eliminare la stringa "Cerca*" e sostituirla con la query seguente:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Fare quindi clic su **Esegui**. 

    ![Fare clic su Esegui per avviare la query](./media/entitlement-management-logs-and-reporting/run-query.png)

La tabella visualizzerà gli eventi del log di controllo per la gestione entitlement dell'ultima ora per impostazione predefinita. È possibile modificare l'impostazione "Intervallo di tempo" per visualizzare gli eventi meno recenti. Tuttavia, modificando questa impostazione verranno visualizzati solo gli eventi che si sono verificati dopo la configurazione di Azure AD per l'invio di eventi a Monitoraggio di Azure.

Per informazioni sugli eventi di controllo meno recenti e più recenti contenuti in Monitoraggio di Azure, usare la query seguente:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Per altre informazioni sulle colonne archiviate per gli eventi di controllo in Monitoraggio di Azure, vedere [Interpretare lo schema dei log di controllo di Azure AD in Monitoraggio di Azure](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Creare query di Monitoraggio di Azure personalizzate usando Azure PowerShell

È possibile accedere ai log tramite PowerShell dopo aver configurato Azure AD per inviare i log a Monitoraggio di Azure. Inviare quindi le query dagli script o dalla riga di comando di PowerShell, senza che sia necessario un amministratore globale nel tenant. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Verificare che l'assegnazione di ruolo dell'utente o dell'entità servizio sia corretta

Assicurarsi che l'utente o l'entità servizio che eseguirà l'autenticazione a Azure AD abbia il ruolo di Azure appropriato nell'area di lavoro Log Analytics. Le opzioni dei ruoli sono Lettore di Log Analytics o Collaboratore di Log Analytics. Se si ha già uno di questi ruoli, passare a [Recuperare l'ID di Log Analytics con una sottoscrizione di Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Per impostare l'assegnazione di ruolo e creare una query, eseguire i passaggi seguenti:

1. Nel portale di Azure individuare l'[area di lavoro Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selezionare **Controllo di accesso (IAM)** .

1. Fare quindi clic su **Aggiungi** per aggiungere un'assegnazione di ruolo.

    ![Aggiungi un'assegnazione di ruolo](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Installare il modulo Azure PowerShell

Una volta eseguita l'assegnazione di ruolo appropriata, avviare PowerShell e [installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (se non è già stato fatto), digitando:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
A questo punto è possibile eseguire l'autenticazione ad Azure AD e recuperare l'ID dell'area di lavoro Log Analytics per cui si sta eseguendo la query.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Recuperare l'ID di Log Analytics con una sottoscrizione di Azure
Se si dispone di una sola sottoscrizione di Azure e di una singola area di lavoro Log Analytics, digitare quanto segue per eseguire l'autenticazione ad Azure AD, connettersi a tale sottoscrizione e recuperare l'area di lavoro:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Recuperare l'ID di Log Analytics con più sottoscrizioni di Azure

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) funziona in una sottoscrizione alla volta. Perciò, se si dispone di più sottoscrizioni di Azure, è necessario assicurarsi di connettersi a quella contenente l'area di lavoro Log Analytics con i log di Azure AD. 
 
 I cmdlet seguenti visualizzano un elenco di sottoscrizioni e individuano l'ID della sottoscrizione contenente l'area di lavoro Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
È possibile eseguire nuovamente l'autenticazione e associare la sessione di PowerShell a tale sottoscrizione usando un comando come `Connect-AzAccount –Subscription $subs[0].id`. Per altre informazioni su come eseguire l'autenticazione in Azure da PowerShell, anche in modo non interattivo, vedere [Accedere con Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
).

Se nella sottoscrizione sono presenti più aree di lavoro Log Analytics, il cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) restituisce l'elenco delle aree di lavoro. Sarà quindi possibile trovare quella contenente i log Azure AD. Il campo `CustomerId` restituito da questo cmdlet corrisponde al valore dell'"ID area di lavoro" visualizzato nel portale di Azure nella panoramica dell'area di lavoro Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Inviare la query all'area di lavoro Log Analytics
Infine, dopo aver identificato un'area di lavoro, è possibile usare [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) per inviare una query Kusto a tale area di lavoro. Queste query sono scritte nel [linguaggio di query Kusto](/azure/kusto/query/).
 
Ad esempio, è possibile recuperare l'intervallo di date dei record degli eventi di controllo dall'area di lavoro Log Analytics, con i cmdlet di PowerShell per inviare una query come la seguente:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

È anche possibile recuperare gli eventi di gestione entitlement usando una query come la seguente:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Passaggi successivi:
- [Creare report interattivi con cartelle di lavoro di Monitoraggio di Azure](../../azure-monitor/platform/workbooks-overview.md)