---
title: Controllo degli accessi in base al ruolo in Automazione di Azure
description: Il controllo degli accessi in base al ruolo consente di gestire gli accessi per le risorse di Azure. Questo articolo descrive come impostare il controllo degli accessi in base al ruolo in Automazione di Azure.
keywords: controllo degli accessi in base al ruolo di Automazione, controllo degli accessi in base al ruolo, controllo degli accessi in base al ruolo di Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 9fb77f3b435491b5ac5b16327d6ce74f90664a79
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="role-based-access-control-in-azure-automation"></a>Controllo degli accessi in base al ruolo in Automazione di Azure

Il controllo degli accessi in base al ruolo consente di gestire gli accessi per le risorse di Azure. Con il [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) è possibile separare i compiti all'interno del team e concedere a utenti, gruppi e applicazioni solo il livello di accesso necessario per svolgere le proprie attività. È possibile concedere l'accesso in base al ruolo agli utenti tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API di gestione di Azure.

## <a name="roles-in-automation-accounts"></a>Ruoli negli account di Automazione
In Automazione di Azure l'accesso viene concesso assegnando il ruolo Controllo degli accessi in base al ruolo appropriato a utenti, gruppi e applicazioni nell'ambito dell'account di automazione. Di seguito sono elencati i ruoli predefiniti supportati da un account di automazione:

| **Ruolo** | **Descrizione** |
|:--- |:--- |
| Proprietario |Il ruolo Proprietario consente l'accesso a tutte le risorse e le azioni in un account di Automazione, inclusa la possibilità di concedere l'accesso ad altri utenti e gruppi e ad altre applicazioni per gestire l'account di Automazione. |
| Collaboratore |Il ruolo Collaboratore consente di gestire tutto, tranne la modifica delle autorizzazioni di accesso di altri utenti a un account di automazione. |
| Reader |Il ruolo Lettore consente di visualizzare tutte le risorse in un account di automazione, ma non di apportare modifiche. |
| Operatore di automazione |Il ruolo Operatore di automazione permette di eseguire attività operative, tra cui l'avvio, l'arresto, la sospensione, la ripresa e la pianificazione di processi. Questo ruolo è utile per proteggere le risorse dell'account di automazione, come asset delle credenziali e runbook, dalla visualizzazione o dalla modifica, consentendo però ai membri dell'organizzazione di eseguire i runbook. |
|Operatore processo di automazione|Il ruolo Operatore processo di automazione consente di creare e gestire processi usando i runbook di Automazione.|
|Operatore runbook di automazione|Il ruolo Operatore runbook di automazione consente di leggere le proprietà del runbook. È anche possibile creare i processi del runbook.|
| Collaboratore di Log Analytics | Il ruolo Collaboratore di Log Analytics consente di leggere tutti i dati di monitoraggio e di modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure.|
| Lettore di Log Analytics | Il ruolo Lettore di Log Analytics consente di visualizzare e cercare tutti i dati di monitoraggio, oltre alle impostazioni di monitoraggio, inclusa la configurazione della diagnostica di Azure in tutte le risorse di Azure. |
| Collaboratore al monitoraggio | Il ruolo Monitoraggio del ruolo Collaboratore consente di leggere tutti i dati di monitoraggio e di aggiornare le impostazioni di monitoraggio.|
| Lettore di monitoraggio | Il ruolo Monitoraggio del ruolo Lettore consente di leggere tutti i dati di monitoraggio. |
| Amministratore accessi utente |Il ruolo Amministratore Accesso utenti consente di gestire l'accesso utente agli account di automazione di Azure. |

## <a name="role-permissions"></a>Autorizzazioni ruoli

Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo. Può trattarsi di proprietà Actions, che concedono le autorizzazioni, e NotActions, che le limitano.

### <a name="owner"></a>Proprietario

Un proprietario può gestire tutto, compresi gli accessi. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|Azioni|DESCRIZIONE|
|---|---|
|Microsoft.Automation/automationAccounts/|Può creare e gestire ogni tipo di risorsa.|

### <a name="contributor"></a>Collaboratore

Un collaboratore può gestire tutto a eccezione degli accessi. La tabella seguente illustra le autorizzazioni concesse e negate per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|È in grado di creare e gestire ogni tipo di risorsa|
|**Non azioni**||
|Microsoft.Authorization/*/Delete| Elimina ruoli e assegnazioni di ruoli.       |
|Microsoft.Authorization/*/Write     |  Crea ruoli e assegnazioni di ruoli.       |
|Microsoft.Authorization/elevateAccess/Action    | Nega la possibilità di creare un amministratore Accesso utenti.       |

### <a name="reader"></a>Reader

Un lettore può visualizzare tutte le risorse in un account di Automazione, ma non apportare modifiche.

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Visualizza tutte le risorse in un account di Automazione. |

### <a name="automation-job-operator"></a>Operatore processo di automazione

Un operatore processo di automazione viene concesso nell'ambito dell'account di Automazione. In questo modo le autorizzazioni di operatore possono gestire i processi nell'account.

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Authorization/*/read|Legge l'autorizzazione.|
|Microsoft.Automation/automationAccounts/jobs/read|Elenca i processi del runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Riprende un processo in pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annulla un processo in corso.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Legge i flussi e l'output di processo.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Sospende un processo in corso.|
|Microsoft.Automation/automationAccounts/jobs/write|Crea processi.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Legge i ruoli e le assegnazioni di ruoli.       |
|Microsoft.Resources/deployments/*      |Crea e gestisce distribuzioni di gruppi di risorse.         |
|Microsoft.Insights/alertRules/*      | Crea e gestisce regole di avviso.        |
|Microsoft.Support/* |Crea e gestisce ticket di supporto.|

### <a name="automation-runbook-operator"></a>Operatore runbook di automazione

Un ruolo Operatore runbook di automazione viene concesso nell'ambito del runbook. Un operatore runbook di automazione può visualizzare il nome del runbook. Questa autorizzazione, combinata con "Operatore processo di automazione" nell'ambito di account di Automazione, consente all'operatore di eseguire le azioni di Operatore di automazione per un runbook specifico. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Elenca i runbook.        |
|Microsoft.Authorization/*/read      | Legge l'autorizzazione.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Legge i ruoli e le assegnazioni di ruoli.         |
|Microsoft.Resources/deployments/*      | Crea e gestisce distribuzioni di gruppi di risorse.         |
|Microsoft.Insights/alertRules/*      | Crea e gestisce regole di avviso.        |
|Microsoft.Support/*      | Crea e gestisce ticket di supporto.        |

### <a name="automation-operator"></a>Operatore di automazione

Un operatore di automazione può avviare, arrestare, sospendere e riprendere processi. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Authorization/*/read|Legge l'autorizzazione.|
|Microsoft.Automation/automationAccounts/jobs/read|Elenca i processi del runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Riprende un processo in pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annulla un processo in corso.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Legge i flussi e l'output di processo.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Sospende un processo in corso.|
|Microsoft.Automation/automationAccounts/jobs/write|Crea processi.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Legge i ruoli e le assegnazioni di ruoli.         |
|Microsoft.Resources/deployments/*      |Crea e gestisce distribuzioni di gruppi di risorse.         |
|Microsoft.Insights/alertRules/*      | Crea e gestisce regole di avviso.        |
|Microsoft.Support/* |Crea e gestisce ticket di supporto.|

### <a name="log-analytics-contributor"></a>Collaboratore di Log Analytics

Un collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge risorse di tutti i tipi, eccetto i segreti.|
|Microsoft.Automation/automationAccounts/*|Gestisce gli account di Automazione.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Crea e gestisce le estensioni macchina virtuale.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Elenca le chiavi dell'account di archiviazione classico.|
|Microsoft.Compute/virtualMachines/extensions/*|Crea e gestisce le estensioni macchina virtuale classiche.|
|Microsoft.Insights/alertRules/*|Regole di avviso di lettura, scrittura ed eliminazione.|
|Microsoft.Insights/diagnosticSettings/*|Impostazioni di diagnostica di lettura, scrittura ed eliminazione.|
|Microsoft.OperationalInsights/*|Gestisce Log Analytics.|
|Microsoft.OperationsManagement/*|Gestisce le soluzioni nelle aree di lavoro.|
|Microsoft.Resources/deployments/*|Crea e gestisce distribuzioni di gruppi di risorse.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Crea e gestisce distribuzioni di gruppi di risorse.|
|Microsoft.Storage/storageAccounts/listKeys/action|Elenca le chiavi dell'account di archiviazione.|
|Microsoft.Support/*|Crea e gestisce ticket di supporto.|


### <a name="log-analytics-reader"></a>Lettore di Log Analytics

Un ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. La tabella seguente illustra le autorizzazioni concesse o negate per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge risorse di tutti i tipi, eccetto i segreti.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Gestisce le query in Log Analytics.|
|Microsoft.OperationalInsights/workspaces/search/action|Cerca i dati in Log Analytics.|
|Microsoft.Support/*|Crea e gestisce ticket di supporto.|
|**Non azioni**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Non può leggere le chiavi di accesso condivise.|

### <a name="monitoring-contributor"></a>Collaboratore al monitoraggio

Un ruolo Monitoraggio del ruolo Collaboratore può leggere tutti i dati di monitoraggio e aggiornare le impostazioni di monitoraggio. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge risorse di tutti i tipi, eccetto i segreti.|
|Microsoft.AlertsManagement/alerts/*|Gestisce gli avvisi.|
|Microsoft.AlertsManagement/alertsSummary/*|Gestisce il dashboard degli avvisi.|
|Microsoft.Insights/AlertRules/*|Gestisce le regole di avviso.|
|Microsoft.Insights/components/*|Gestisce i componenti di Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Gestisce le impostazioni di diagnostica.|
|Microsoft.Insights/eventtypes/*|Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività.|
|Microsoft.Insights/LogDefinitions/*|Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai registri attività tramite il portale. Elencare categorie di log nel log attività.|
|Microsoft.Insights/MetricDefinitions/*|Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa).|
|Microsoft.Insights/Metrics/*|Metriche per una risorsa.|
|Microsoft.Insights/Register/Action|Registra il provider Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Gestisce i test Web di Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gestisce i pacchetti della soluzione Log Analytics.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Gestisce le ricerche salvate di Log Analytics.|
|Microsoft.OperationalInsights/workspaces/search/action|Cercare aree di lavoro di Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Elencare chiavi per un'area di lavoro di Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gestisce la configurazione di dati di archiviazione di Log Analytics.|
|Microsoft.Support/*|Crea e gestisce ticket di supporto.|
|Microsoft.WorkloadMonitor/workloads/*|Gestisce i carichi di lavoro.|

### <a name="monitoring-reader"></a>Lettore di monitoraggio

Un ruolo Monitoraggio del ruolo Lettore può leggere tutti i dati di monitoraggio. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge risorse di tutti i tipi, eccetto i segreti.|
|Microsoft.OperationalInsights/workspaces/search/action|Cercare aree di lavoro di Log Analytics.|
|Microsoft.Support/*|Creare e gestire ticket di supporto|

### <a name="user-access-administrator"></a>Amministratore accessi utente

Un amministratore Accesso utenti può gestire l'accesso degli utenti alle risorse di Azure. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge tutte le risorse|
|Microsoft.Authorization/*|Gestire l'autorizzazione|
|Microsoft.Support/*|Creare e gestire ticket di supporto|

## <a name="onboarding"></a>Onboarding

Le tabelle seguenti illustrano le autorizzazioni minime necessarie per l'onboarding delle macchine virtuali per le soluzioni Rilevamento modifiche o Gestione aggiornamenti.

### <a name="onboarding-from-a-virtual-machine"></a>Onboarding da una macchina virtuale

|**Azione**  |**Autorizzazione**  |**Ambito minimo**  |
|---------|---------|---------|
|Scrivere una nuova distribuzione      | Microsoft.Resources/deployments/*          |Sottoscrizione          |
|Scrivere un nuovo gruppo di risorse      | Microsoft.Resources/subscriptions/resourceGroups/write        | Sottoscrizione          |
|Creare una nuova area di lavoro predefinita      | Microsoft.OperationalInsights/workspaces/write         | Gruppo di risorse         |
|Creare un nuovo account      |  Microsoft.Automation/automationAccounts/write        |Gruppo di risorse         |
|Collegare un'area di lavoro e un account      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Area di lavoro</br>Account di Automazione
|Creare una soluzione      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Gruppo di risorse          |
|Creare un'estensione MMA      | Microsoft.Compute/virtualMachines/write         | Macchina virtuale         |
|Creare una ricerca salvata      | Microsoft.OperationalInsights/workspaces/write          | Area di lavoro         |
|Creare una configurazione di ambito      | Microsoft.OperationalInsights/workspaces/write          | Area di lavoro         |
|Collegare una soluzione a una configurazione di ambito      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Soluzione         |
|Controllo dello stato di onboarding - Leggere l'area di lavoro      | Microsoft.OperationalInsights/workspaces/read         | Area di lavoro         |
|Controllo dello stato di onboarding - Leggere la proprietà dell'area di lavoro collegata dell'account     | Microsoft.Automation/automationAccounts/read      | Account di Automazione        |
|Controllo dello stato di onboarding - Leggere la soluzione      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Soluzione         |
|Controllo dello stato di onboarding - Leggere la VM      | Microsoft.Compute/virtualMachines/read         | Macchina virtuale         |
|Controllo dello stato di onboarding - Leggere l'account      | Microsoft.Automation/automationAccounts/read  |  Account di Automazione   |

### <a name="onboarding-from-automation-account"></a>Onboarding dall'account di Automazione

|**Azione**  |**Autorizzazione** |**Ambito minimo**  |
|---------|---------|---------|
|Creare una nuova distribuzione     | Microsoft.Resources/deployments/*        | Sottoscrizione         |
|Creare un nuovo gruppo di risorse     | Microsoft.Resources/subscriptions/resourceGroups/write         | Sottoscrizione        |
|Pannello AutomationOnboarding - Creare una nuova area di lavoro     |Microsoft.OperationalInsights/workspaces/write           | Gruppo di risorse        |
|Pannello AutomationOnboarding - Leggere un'area di lavoro collegata     | Microsoft.Automation/automationAccounts/read        | Account di Automazione       |
|Pannello AutomationOnboarding - Leggere una soluzione     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Soluzione        |
|Pannello AutomationOnboarding - Leggere un'area di lavoro     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Area di lavoro        |
|Creare un collegamento per un'area di lavoro e un account     | Microsoft.OperationalInsights/workspaces/write        | Area di lavoro        |
|Scrivere un account per Shoebox      | Microsoft.Automation/automationAccounts/write        | Account        |
|Creare una soluzione      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Gruppo di risorse         |
|Creare/Modificare una ricerca salvata     | Microsoft.OperationalInsights/workspaces/write        | Area di lavoro        |
|Creare/Modificare una configurazione di ambito     | Microsoft.OperationalInsights/workspaces/write        | Area di lavoro        |
|Collegare una soluzione a una configurazione di ambito      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Soluzione         |
|**Passaggio 2: Caricare più VM**     |         |         |
|Pannello VMOnboarding - Creare un'estensione MMA     | Microsoft.Compute/virtualMachines/write           | Macchina virtuale        |
|Creare/Modificare una ricerca salvata     | Microsoft.OperationalInsights/workspaces/write           | Area di lavoro        |
|Creare/Modificare una configurazione di ambito  | Microsoft.OperationalInsights/workspaces/write   | Area di lavoro|

## <a name="update-management"></a>Gestione degli aggiornamenti

La gestione degli aggiornamenti copre più servizi per fornire il proprio servizio. La tabella seguente illustra le autorizzazioni necessarie per gestire le distribuzioni di gestione degli aggiornamenti:

|**Risorsa**  |**Ruolo**  |**Ambito**  |
|---------|---------|---------|
|Account di Automazione     | Collaboratore di Log Analytics       | Account di Automazione        |
|Account di Automazione    | Collaboratore macchine virtuali        | Gruppo di risorse per l'account        |
|Area di lavoro di Log Analytics     | Collaboratore di Log Analytics| Area di lavoro di Log Analytics        |
|Area di lavoro di Log Analytics |Lettore di Log Analytics| Sottoscrizione|
|Soluzione     |Collaboratore di Log Analytics         | Soluzione|
|Macchina virtuale     | Collaboratore macchine virtuali        | Macchina virtuale        |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Configurare il controllo degli accessi in base al ruolo per l'account di Automazione usando il portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com/) e aprire l'account di Automazione nella pagina Account di automazione.  
2. Fare clic sul controllo **Controllo di accesso (IAM)** nell'angolo superiore sinistro. Verrà visualizzata la pagina **Controllo di accesso (IAM)**, in cui è possibile aggiungere nuovi utenti, gruppi e applicazioni per gestire il proprio account di Automazione e visualizzare i ruoli esistenti che possono essere configurati per l'account di Automazione.
   
   ![Pulsante Accesso](media/automation-role-based-access-control/automation-01-access-button.png)  

### <a name="add-a-new-user-and-assign-a-role"></a>Aggiungere un nuovo utente e assegnare un ruolo
1. Nella pagina **Controllo di accesso (IAM)** fare clic su **+ Aggiungi** per aprire la pagina **Aggiungi autorizzazioni**, in cui è possibile aggiungere un utente, un gruppo o un'applicazione cui assegnare un ruolo.  

2. Selezionare un ruolo dall'elenco di ruoli disponibili. È possibile scegliere uno qualsiasi dei ruoli predefiniti disponibili supportati da un account di Automazione oppure un ruolo personalizzato definito dall'utente.

3. Digitare il nome utente dell'utente a cui si vogliono concedere le autorizzazioni nel campo **Seleziona**. Selezionare l'utente dall'elenco e fare clic su **Salva**.
   
   ![Aggiungere utenti](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   L'utente aggiunto verrà ora visualizzato nella pagina **Utenti** con il ruolo selezionato assegnato.  
   
   ![Elencare gli utenti](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   È anche possibile assegnare un ruolo all'utente dalla pagina **Ruoli** . 
4. Fare clic su **Ruoli** nella pagina **Controllo di accesso (IAM)** per aprire la pagina **Ruoli**. Da qui è possibile visualizzare il nome del ruolo e il numero di utenti e gruppi assegnati al ruolo.
   
    ![Assegnare un ruolo dalla pagina Utenti](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > Il controllo degli accessi in base al ruolo può essere impostato solo nell'ambito dell'account di Automazione e in nessun'altra risorsa al di sotto dell'account di Automazione.

### <a name="remove-a-user"></a>Rimuovere un utente
È possibile rimuovere l'autorizzazione di accesso per un utente che non gestisce l'account di Automazione o che non lavora più per l'organizzazione. Ecco la procedura da seguire per rimuovere un utente: 

1. Nella pagina **Controllo di accesso (IAM)** selezionare l'utente che si vuole rimuovere e fare clic su **Rimuovi**.
2. Fare clic sul pulsante **Rimuovi** nel pannello dei dettagli dell'assegnazione.
3. Fare clic su **Sì** per confermare la rimozione.

   ![Rimuovere utenti](media/automation-role-based-access-control/automation-08-remove-users.png)

## <a name="role-assigned-user"></a>Utente assegnato a un ruolo

Quando un utente assegnato a un ruolo accede ad Azure e seleziona l'account di Automazione, può visualizzare l'account del proprietario nell'elenco delle **directory**. Per visualizzare l'account di automazione a cui è stato aggiunto, l'utente deve passare dalla directory predefinita alla directory predefinita del proprietario.

### <a name="user-experience-for-automation-operator-role"></a>Esperienza utente per il ruolo Automation Operator
Quando un utente assegnato al ruolo Operatore di automazione visualizza l'account di Automazione a cui è stato assegnato, può vedere solo l'elenco delle pianificazioni, dei runbook e dei processi di runbook creati nell'account di Automazione, ma non le relative definizioni. L'utente può avviare, arrestare, sospendere, riprendere o pianificare il processo del runbook, ma non avrà accesso ad altre risorse di Automazione, tra cui configurazioni, gruppi di ruoli di lavoro ibridi o nodi DSC.

![Nessun accesso alle risorse](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

L'utente può accedere per visualizzare e creare pianificazioni, ma non avrà accesso ad altri tipi di asset.

L'utente non può accedere nemmeno per visualizzare i webhook associati a un runbook.

![Nessun accesso ai webhook](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Configurare il controllo degli accessi in base al ruolo per l'account di Automazione usando Azure PowerShell
L'accesso in base al ruolo per un account di Automazione può essere configurato anche con i [cmdlet di Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md) seguenti:

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) elenca tutti i ruoli del controllo degli accessi in base al ruolo disponibili in Azure Active Directory. È possibile usare questo comando con la proprietà **Name** per elencare tutte le azioni che possono essere eseguite da un ruolo specifico.

```powershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Di seguito è riportato l'output di esempio:

```powershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action, 
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
``` 

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) elenca le assegnazioni di ruolo del controllo degli accessi in base al ruolo di Azure AD nell'ambito specificato. Senza parametri, questo comando restituisce tutte le assegnazioni del ruolo eseguite nell'ambito della sottoscrizione. Usare il parametro **ExpandPrincipalGroups** per elencare le assegnazioni di accesso per l'utente specificato e per i gruppi di cui l'utente è membro.  
    **Esempio:** usare il comando seguente per elencare tutti gli utenti e i relativi ruoli all'interno di un account di automazione.

```powershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Di seguito è riportato l'output di esempio:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

• Usare [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) per assegnare l'accesso a utenti, gruppi e applicazioni in un determinato ambito.  
    **Esempio:** usare il comando seguente per assegnare il ruolo "Operatore di automazione" a un utente nell'ambito dell'account di Automazione.

```powershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Di seguito è riportato l'output di esempio:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

• Usare [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) per rimuovere l'accesso di un utente, un gruppo o un'applicazione specificata da un determinato ambito.  
    **Esempio:** usare il comando seguente per rimuovere l'utente dal ruolo "Operatore di automazione" nell'ambito dell'account di Automazione.

```powershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Negli esempi precedenti sostituire l'**ID di accesso**, l'**ID sottoscrizione**, il **nome del gruppo di risorse** e il **nome dell'account di Automazione** con i dettagli del proprio account. Scegliere **Sì** quando viene richiesto di confermare se si vuole rimuovere un'assegnazione di ruolo per un utente.   

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulle diverse modalità disponibili per configurare il controllo degli accessi in base al ruolo per Automazione di Azure, vedere [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
* Per informazioni dettagliate sulle diverse modalità di avvio dei runbook, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)
* Per informazioni sui diversi tipi di runbook, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)

