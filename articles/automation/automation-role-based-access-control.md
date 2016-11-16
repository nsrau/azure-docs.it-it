---
title: Controllo degli accessi in base al ruolo in Automazione di Azure | Documentazione Microsoft
description: Il controllo degli accessi in base al ruolo consente di gestire gli accessi per le risorse di Azure. Questo articolo descrive come impostare il controllo degli accessi in base al ruolo in Automazione di Azure.
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: controllo degli accessi in base al ruolo di Automazione, controllo degli accessi in base al ruolo, controllo degli accessi in base al ruolo di Azure
ms.assetid: 04b5625e-0ee8-4b5b-85cd-7734c1b3d4a3
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;sngun
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9fff24dfd2b20a785c6046b6c9700b583c309de4


---
# <a name="rolebased-access-control-in-azure-automation"></a>Controllo degli accessi in base al ruolo in Automazione di Azure
## <a name="rolebased-access-control"></a>Controllo degli accessi in base al ruolo
Il controllo degli accessi in base al ruolo consente di gestire gli accessi per le risorse di Azure. Con il [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md)è possibile separare i compiti all'interno del team e concedere a utenti, gruppi e applicazioni solo il livello di accesso necessario per svolgere le proprie attività. L'accesso in base al ruolo può essere concesso agli utenti tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API di gestione di Azure.

## <a name="rbac-in-automation-accounts"></a>Controllo degli accessi in base al ruolo negli account di automazione
In Automazione di Azure l'accesso viene concesso assegnando il ruolo Controllo degli accessi in base al ruolo appropriato a utenti, gruppi e applicazioni nell'ambito dell'account di automazione. Di seguito sono elencati i ruoli predefiniti supportati da un account di automazione:  

| **Ruolo** | **Descrizione** |
|:--- |:--- |
| Proprietario |Il ruolo Proprietario consente l'accesso a tutte le risorse e le azioni in un account di automazione, inclusa la possibilità di concedere l'accesso ad altri utenti e gruppi e ad altre applicazioni per gestire l'account di automazione. |
| Collaboratore |Il ruolo Collaboratore consente di gestire tutto, tranne la modifica delle autorizzazioni di accesso di altri utenti a un account di automazione. |
| Lettore |Il ruolo Lettore consente di visualizzare tutte le risorse in un account di automazione, ma non di apportare modifiche. |
| Operatore di automazione |Il ruolo Automation Operator consente di eseguire attività operative, ad esempio avviare, arrestare, sospendere, riprendere e pianificare i processi. Questo ruolo è utile per proteggere le risorse dell'account di automazione, come asset delle credenziali e runbook, dalla visualizzazione o dalla modifica, consentendo però ai membri dell'organizzazione di eseguire i runbook. |
| Amministratore accessi utente |Il ruolo Amministratore Accesso utenti consente di gestire l'accesso utente agli account di automazione di Azure. |

> [!NOTE]
> Non è possibile concedere diritti di accesso a uno o più runbook specifici, ma solo alle risorse e le azioni all'interno dell'account di automazione.  
> 
> 

Questo articolo illustra come configurare il controllo degli accessi in base al ruolo in Automazione di Azure. Verranno prima illustrate le singole autorizzazioni concesse ai ruoli Collaboratore, Lettore, Automation Operator e Amministratore Accesso utenti, per comprenderle a fondo prima di concedere diritti a qualcuno per l'account di automazione.  In caso contrario potrebbero verificarsi conseguenze impreviste o indesiderate.     

## <a name="contributor-role-permissions"></a>Autorizzazioni del ruolo Collaboratore
La tabella seguente illustra le azioni specifiche che possono essere eseguite dal ruolo Collaboratore in Automazione.

| **Tipo di risorsa** | **Lettura** | **Scrittura** | **Eliminazione** | **Altre azioni** |
|:--- |:--- |:--- |:--- |:--- |
| Account di automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Asset di certificato di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Asset di connessione di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Asset del tipo di connessione di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Asset credenziali di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Asset di pianificazione di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Asset di tipo variabile di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Configurazione dello stato desiderato di Automazione | | | |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |
| Tipo di risorsa del ruolo di lavoro ibrido per runbook |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Processo di automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |
| Flusso del processo di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Pianificazione del processo di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Modulo di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Runbook di Automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |
| Bozza di runbook di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |
| Processo di test della bozza di runbook di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |
| Webhook di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |

## <a name="reader-role-permissions"></a>Autorizzazioni del ruolo Lettore
La tabella seguente illustra le azioni specifiche che possono essere eseguite dal ruolo Lettore in Automazione.

| **Tipo di risorsa** | **Lettura** | **Scrittura** | **Eliminazione** | **Altre azioni** |
|:--- |:--- |:--- |:--- |:--- |
| Amministratore sottoscrizione classico |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Blocco di gestione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Autorizzazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Operazioni del provider |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Assegnazione di ruolo |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Definizione di ruolo |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="automation-operator-role-permissions"></a>Autorizzazioni del ruolo Automation Operator
La tabella seguente illustra le azioni specifiche che possono essere eseguite dal ruolo Automation Operator in Automazione.

| **Tipo di risorsa** | **Lettura** | **Scrittura** | **Eliminazione** | **Altre azioni** |
|:--- |:--- |:--- |:--- |:--- |
| Account di automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Asset di certificato di Automazione | | | | |
| Asset di connessione di Automazione | | | | |
| Asset del tipo di connessione di Automazione | | | | |
| Asset credenziali di Automazione | | | | |
| Asset di pianificazione di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Asset di tipo variabile di Automazione | | | | |
| Configurazione dello stato desiderato di Automazione | | | | |
| Tipo di risorsa del ruolo di lavoro ibrido per runbook | | | | |
| Processo di automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |
| Flusso del processo di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Pianificazione del processo di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Modulo di automazione | | | | |
| Runbook di Automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Bozza di runbook di Automazione | | | | |
| Processo di test della bozza di runbook di Automazione | | | | |
| Webhook di automazione | | | | |

Per altre informazioni, vedere l'elenco delle [azioni supportate dal ruolo Automation Operator](../active-directory/role-based-access-built-in-roles.md#automation-operator) nell'account di automazione e nelle relative risorse.

## <a name="user-access-administrator-role-permissions"></a>Autorizzazioni del ruolo Amministratore Accesso utenti
La tabella seguente illustra le azioni specifiche che possono essere eseguite dal ruolo Amministratore Accesso utenti in Automazione.

| **Tipo di risorsa** | **Lettura** | **Scrittura** | **Eliminazione** | **Altre azioni** |
|:--- |:--- |:--- |:--- |:--- |
| Account di automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Asset di certificato di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Asset di connessione di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Asset del tipo di connessione di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Asset credenziali di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Asset di pianificazione di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Asset di tipo variabile di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Configurazione dello stato desiderato di Automazione | | | | |
| Tipo di risorsa del ruolo di lavoro ibrido per runbook |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Processo di automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Flusso del processo di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Pianificazione del processo di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Modulo di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Runbook di Automazione di Azure |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Bozza di runbook di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Processo di test della bozza di runbook di Automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Webhook di automazione |![Stato verde](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Configurare il controllo degli accessi in base al ruolo per l'account di automazione tramite il portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com/) e aprire l'account di automazione nel pannello Account di automazione.  
2. Fare clic sul comando **Accesso** nell'angolo superiore destro. Verrà visualizzato il pannello **Utenti** in cui è possibile aggiungere nuovi utenti, gruppi e applicazioni per gestire il proprio account di automazione e visualizzare i ruoli esistenti che possono essere configurati per l'account di automazione.  
   
   ![Pulsante Accesso](media/automation-role-based-access-control/automation-01-access-button.png)  

> [!NOTE]
> **Amministratori della sottoscrizione** è già presente come utente predefinito. Il gruppo di Active Directory Amministratori della sottoscrizione include gli amministratori e i coamministratori del servizio per la sottoscrizione di Azure. L'amministratore del servizio è il proprietario della sottoscrizione di Azure e delle relative risorse ed eredita anche il ruolo Proprietario per gli account di automazione. Ciò significa che l'accesso è **Ereditato** per gli **amministratori e coamministratori del servizio** di una sottoscrizione ed è **Assegnato** per tutti gli altri utenti. Fare clic su **Amministratori della sottoscrizione** per visualizzare altri dettagli sulle relative autorizzazioni.  
> 
> 

### <a name="add-a-new-user-and-assign-a-role"></a>Aggiungere un nuovo utente e assegnare un ruolo
1. Nel pannello Utenti fare clic su **Aggiungi** per aprire il pannello **Aggiungi accesso** dove è possibile aggiungere un utente, un gruppo o un'applicazione e assegnare un ruolo.  
   
   ![Add user](media/automation-role-based-access-control/automation-02-add-user.png)  
2. Selezionare un ruolo dall'elenco di ruoli disponibili. Qui si sceglierà il ruolo **Lettore** , ma è possibile scegliere uno qualsiasi dei ruoli predefiniti disponibili supportati da un account di automazione oppure un ruolo personalizzato definito dall'utente.  
   
   ![Selezionare il ruolo](media/automation-role-based-access-control/automation-03-select-role.png)  
3. Fare clic su **Aggiungi utenti** per aprire il pannello **Aggiungi utenti**. Se sono stati aggiunti utenti, gruppi o applicazioni per gestire la sottoscrizione, questi saranno elencati e si potranno selezionare per aggiungere l'accesso. Se non ci sono utenti elencati o se l'utente che si vuole aggiungere non è nell'elenco, fare clic su **Invita** per aprire il pannello **Invitare un utente guest** dove è possibile invitare un utente con un indirizzo di posta elettronica di un account Microsoft valido, ad esempio Outlook.com, OneDrive o ID di Xbox Live. Dopo aver immesso l'indirizzo di posta elettronica dell'utente, fare clic su **Seleziona** per aggiungere l'utente e quindi fare clic su **OK**. 
   
   ![Aggiungi utenti](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   L'utente aggiunto verrà visualizzato nel pannello **Utenti** con assegnato il ruolo **Lettore**.  
   
   ![Elencare gli utenti](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   È anche possibile assegnare un ruolo all'utente dal pannello **Ruoli** . 
4. Fare clic su **Ruoli** nel pannello Utenti per aprire il pannello **Ruoli**. In questo pannello è possibile visualizzare il nome del ruolo, il numero di utenti e i gruppi assegnati al ruolo.
   
    ![Assegnare un ruolo dal pannello Utenti](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > Il controllo degli accessi in base al ruolo può essere impostato solo a livello di account di automazione e in nessun'altra risorsa al di sotto dell'account di automazione.
   > 
   > 
   
    È possibile assegnare più di un ruolo a un utente, un gruppo o un'applicazione. Se ad esempio si aggiunge il ruolo **Automation Operator** oltre al ruolo **Lettore** per un utente, quest'ultimo potrà visualizzare tutte le risorse di automazione ed eseguire i processi del runbook. È possibile espandere l'elenco a discesa per visualizzare un elenco dei ruoli assegnati all'utente.  
   
    ![Visualizzare più ruoli](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  

### <a name="remove-a-user"></a>Rimuovere un utente
È possibile rimuovere l'autorizzazione di accesso per un utente che non gestisce l'account di automazione o che non lavora più per l'organizzazione. Ecco la procedura da seguire per rimuovere un utente: 

1. Nel pannello **Utenti** selezionare l'assegnazione del ruolo che si vuole rimuovere.
2. Fare clic sul pulsante **Rimuovi** nel pannello dei dettagli dell'assegnazione.
3. Fare clic su **Sì** per confermare la rimozione. 
   
   ![Rimuovere utenti](media/automation-role-based-access-control/automation-08-remove-users.png)  

## <a name="role-assigned-user"></a>Utente assegnato a un ruolo
Quando un utente assegnato a un ruolo accede con l'account di automazione, può visualizzare l'account del proprietario nell'elenco delle **directory predefinite**. Per visualizzare l'account di automazione a cui è stato aggiunto, l'utente deve passare dalla directory predefinita alla directory predefinita del proprietario.  

![Directory predefinita](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### <a name="user-experience-for-automation-operator-role"></a>Esperienza utente per il ruolo Automation Operator
Quando un utente assegnato al ruolo Automation Operator visualizza l'account di automazione a cui è stato assegnato, può vedere solo l'elenco delle pianificazioni, dei runbook e dei processi di runbook creati nell'account di automazione, ma non le relative definizioni. L'utente può avviare, arrestare, sospendere, riprendere o pianificare il processo del runbook, ma non avrà accesso ad altre risorse di automazione, ad esempio configurazioni, gruppi di lavoro ibridi o nodi DSC.  

![Nessun accesso alle risorse](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Quando l'utente fa clic sul runbook, i comandi per visualizzare l'origine o modificare il runbook non sono disponibili, perché il ruolo Automation Operator non ne consente l'accesso.  

![Nessun accesso per modificare i runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

L'utente può accedere per visualizzare e creare pianificazioni, ma non avrà accesso ad altri tipi di asset.  

![Nessun accesso agli asset](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

L'utente non può accedere nemmeno per visualizzare i webhook associati a un runbook.

![Nessun accesso ai webhook](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Configurare il controllo degli accessi in base al ruolo per l'account di automazione tramite Azure PowerShell
L'accesso in base al ruolo per un account di automazione può essere configurato anche con i [cmdlet di Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)seguenti.

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) elenca tutti i ruoli del controllo degli accessi in base al ruolo disponibili in Azure Active Directory. È possibile usare questo comando con la proprietà **Name** per elencare tutte le azioni che possono essere eseguite da un ruolo specifico.  
    **Esempio:**  
    ![Ottenere la definizione del ruolo](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) elenca le assegnazioni di ruolo del controllo degli accessi in base al ruolo di Azure AD nell'ambito specificato. Senza parametri, questo comando restituisce tutte le assegnazioni del ruolo eseguite nell'ambito della sottoscrizione. Usare il parametro **ExpandPrincipalGroups** per elencare le assegnazioni di accesso per l'utente specificato e per i gruppi di cui l'utente è membro.  
    **Esempio:** usare il comando seguente per elencare tutti gli utenti e i relativi ruoli all'interno di un account di automazione.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Ottenere l'assegnazione del ruolo](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• Usare [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) per assegnare l'accesso a utenti, gruppi e applicazioni in un determinato ambito.  
    **Esempio:** usare il comando seguente per assegnare il ruolo "Automation Operator" a un utente nell'ambito dell'account di automazione.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nuova assegnazione del ruolo](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Usare [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) per rimuovere l'accesso di un utente, un gruppo o un'applicazione specificata da un determinato ambito.  
    **Esempio:** usare il comando seguente per rimuovere l'utente dal ruolo "Automation Operator" nell'ambito dell'account di automazione.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Negli esempi precedenti sostituire l'**ID di accesso**, l'**ID sottoscrizione**, il **nome del gruppo di risorse** e il **nome dell'account di automazione** con i dettagli del proprio account. Scegliere **Sì** quando viene richiesto di confermare se si vuole rimuovere un'assegnazione di ruolo per un utente.   

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulle diverse modalità disponibili per configurare il controllo degli accessi in base al ruolo per Automazione di Azure, vedere [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
* Per informazioni dettagliate sulle diverse modalità di avvio dei runbook, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)
* Per informazioni sui diversi tipi di runbook, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)




<!--HONumber=Nov16_HO2-->


