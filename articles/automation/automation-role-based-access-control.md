<properties 
   pageTitle="Controllo degli accessi in base al ruolo in Automazione di Azure | Microsoft Azure"
   description="Il controllo degli accessi in base al ruolo consente di gestire gli accessi per le risorse di Azure. Questo articolo descrive come impostare il controllo degli accessi in base al ruolo in Automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="sngun"/>

# Controllo degli accessi in base al ruolo in Automazione di Azure

## Controllo degli accessi in base al ruolo

Il controllo degli accessi in base al ruolo consente di gestire gli accessi per le risorse di Azure. Con il [Controllo degli accessi in base al ruolo ](../active-directory/role-based-access-control-configure.md) è possibile separare i compiti all'interno del team e concedere a utenti, gruppi e applicazioni solo il livello di accesso necessario per svolgere le proprie attività. L'accesso in base al ruolo può essere concesso agli utenti tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API di gestione di Azure.

## Controllo degli accessi in base al ruolo negli account di automazione

In Automazione di Azure l'accesso viene concesso assegnando il ruolo Controllo degli accessi in base al ruolo appropriato a utenti, gruppi e applicazioni nell'ambito dell'account di automazione. Di seguito sono elencati i ruoli predefiniti supportati da un account di automazione:

|**Ruolo** | **Descrizione** |
|:--- |:---|
| Proprietario | Il ruolo Proprietario consente l'accesso a tutte le risorse e le azioni in un account di automazione, inclusa la possibilità di concedere l'accesso ad altri utenti e gruppi e ad altre applicazioni per gestire l'account di automazione. |
| Collaboratore | Il ruolo Collaboratore consente di gestire tutto, tranne la modifica delle autorizzazioni di accesso di altri utenti a un account di automazione. |
| Lettore | Il ruolo Lettore consente di visualizzare tutte le risorse in un account di automazione, ma non di apportare modifiche. |
| Automation Operator | Il ruolo Automation Operator consente di eseguire attività operative, ad esempio avviare, arrestare, sospendere, riprendere e pianificare i processi. Questo ruolo è utile per proteggere le risorse dell'account di automazione, come asset delle credenziali e runbook, dalla visualizzazione o dalla modifica, consentendo però ai membri dell'organizzazione di eseguire i runbook. [Le azioni di Automation Operator ](../active-directory/role-based-access-built-in-roles.md#automation-operator) includono l'elenco delle azioni supportate dal ruolo Automation Operator nell'account di automazione e nelle relative risorse. |
| Amministratore Accesso utenti | Il ruolo Amministratore Accessi utenti consente di gestire l'accesso utente agli account di automazione di Azure. |

Questo articolo illustra come configurare il controllo degli accessi in base al ruolo in Automazione di Azure.

## Configurare il controllo degli accessi in base al ruolo per l'account di automazione tramite il portale di Azure

1.	Accedere al [portale di Azure](https://portal.azure.com/) e aprire l'account di automazione nel pannello Account di automazione.  

2.	Fare clic sul comando **Accesso** nell'angolo superiore destro. Verrà visualizzato il pannello **Utenti** in cui è possibile aggiungere nuovi utenti, gruppi e applicazioni per gestire il proprio account di automazione e visualizzare i ruoli esistenti che possono essere configurati per l'account di automazione.

    ![Pulsante Accesso](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE]  **Amministratori della sottoscrizione** è già presente come utente predefinito. Il gruppo di Active Directory Amministratori della sottoscrizione include gli amministratori e i coamminstratori del servizio per la sottoscrizione di Azure. L'amministratore del servizio è il proprietario della sottoscrizione di Azure e delle relative risorse ed eredita anche il ruolo Proprietario per gli account di automazione. Ciò significa che l'accesso è **Ereditato** per **Amministratori e coamministratori del servizio** di una sottoscrizione ed è **Assegnato** per tutti gli altri utenti. Fare clic su **Amministratori della sottoscrizione** per visualizzare altri dettagli sulle relative autorizzazioni.

### Aggiungere un nuovo utente e assegnare un ruolo

1.	Nel pannello Utenti fare clic su **Aggiungi** per aprire il pannello **Aggiungi accesso** dove è possibile aggiungere un utente, un gruppo o un'applicazione e assegnare un ruolo.  

    ![Add user](media/automation-role-based-access-control/automation-02-add-user.png)

2.	Selezionare un ruolo dall'elenco di ruoli disponibili. Qui si sceglierà il ruolo **Lettore**, ma è possibile scegliere uno qualsiasi dei ruoli predefiniti disponibili supportati da un account di automazione oppure un ruolo personalizzato definito dall'utente.

    ![Selezionare il ruolo](media/automation-role-based-access-control/automation-03-select-role.png)

3.	Fare clic su **Aggiungi utenti** per aprire il pannello **Aggiungi utenti**. Se sono stati aggiunti utenti, gruppi o applicazioni per gestire la sottoscrizione, questi saranno elencati e si potranno selezionare per aggiungere l'accesso. Se non ci sono utenti elencati o se l'utente che si vuole aggiungere non è nell'elenco, fare clic su **Invita** per aprire il pannello **Invitare un utente guest** dove è possibile invitare un utente con un indirizzo di posta elettronica di un account Microsoft valido, ad esempio Outlook.com, OneDrive o ID di Xbox Live. Dopo aver immesso l'indirizzo di posta elettronica dell'utente, fare clic su **Seleziona** per aggiungere l'utente e quindi fare clic su **OK**.

    ![Add users](media/automation-role-based-access-control/automation-04-add-users.png)
 
L'utente aggiunto verrà visualizzato nel pannello **Utenti** con assegnato il ruolo **Lettore**.

![Elencare gli utenti](media/automation-role-based-access-control/automation-05-list-users.png)

È anche possibile assegnare un ruolo all'utente dal pannello **Ruoli**. Fare clic su **Ruoli** nel pannello Utenti per aprire il pannello **Ruoli**. In questo pannello è possibile visualizzare il nome del ruolo, il numero di utenti e i gruppi assegnati al ruolo.

![Assegnare un ruolo dal pannello Utenti](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
>[AZURE.NOTE] Il controllo degli accessi in base al ruolo può essere impostato solo a livello di account di automazione e in nessun'altra risorsa al di sotto dell'account di automazione.

È possibile assegnare più di un ruolo a un utente, un gruppo o un'applicazione. Ad esempio, se si aggiunge il ruolo **Automation Operator** oltre al ruolo **Lettore** per un utente, quest'ultimo potrà visualizzare tutte le risorse di automazione ed eseguire i processi del runbook. È possibile espandere l'elenco a discesa per visualizzare un elenco dei ruoli assegnati all'utente.

![Visualizzare più ruoli](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### Rimuovere un utente

È possibile rimuovere l'autorizzazione di accesso per un utente che non gestisce l'account di automazione o che non lavora più per l'organizzazione. Ecco la procedura da seguire per rimuovere un utente:

1.	Nel pannello **Utenti** selezionare l'assegnazione del ruolo che si vuole rimuovere.

2.	Fare clic sul pulsante **Rimuovi** nel pannello dei dettagli dell'assegnazione.

3.	Fare clic su **Sì** per confermare la rimozione.

    ![Rimuovere utenti](media/automation-role-based-access-control/automation-08-remove-users.png)

## Utente assegnato a un ruolo

Quando un utente assegnato a un ruolo accede con l'account di automazione, può visualizzare l'account del proprietario nell'elenco **Directory predefinita**. Per visualizzare l'account di automazione a cui è stato aggiunto, l'utente deve passare dalla directory predefinita alla directory predefinita del proprietario.

![Directory predefinita](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Esperienza utente per il ruolo Automation Operator

Quando un utente assegnato al ruolo Automation Operator visualizza l'account di automazione a cui è stato assegnato, può vedere solo l'elenco delle pianificazioni, dei runbook e processi dei runbook creati nell'account di automazione, ma non le relative definizioni. L'utente può avviare, arrestare, sospendere, riprendere o pianificare il processo del runbook, ma non avrà accesso ad altre risorse di automazione, ad esempio configurazioni, gruppi di lavoro ibridi o nodi DSC.

![Nessun accesso alle risorse](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Quando l'utente fa clic sul runbook, i comandi per visualizzare l'origine o modificare il runbook non sono disponibili, perché il ruolo Automation Operator non ne consente l'accesso.

![Nessun accesso per modificare i runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

L'utente può accedere per visualizzare e creare pianificazioni, ma non avrà accesso ad altri tipi di asset.

![Nessun accesso agli asset](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

L'utente non può accedere nemmeno per visualizzare i webhook associati a un runbook.

![Nessun accesso ai webhook](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Configurare il controllo degli accessi in base al ruolo per l'account di automazione tramite Azure PowerShell

L'accesso in base al ruolo per un account di automazione può essere configurato anche con i [cmdlet di Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md) seguenti.

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) elenca tutti i ruoli disponibili nel Controllo degli accessi in base al ruolo di Azure Active Directory. È possibile usare questo comando con la proprietà **Name** per elencare tutti gli utenti con un ruolo specifico. **Esempio:** ![Ottenere la definizione del ruolo](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) elenca le assegnazioni del ruolo Controllo degli accessi in base al ruolo di Azure nell'ambito specificato. Senza parametri questo comando restituisce tutte le assegnazioni del ruolo eseguite nell'ambito della sottoscrizione. Usare il parametro **ExpandPrincipalGroups** per elencare le assegnazioni di accesso all'utente specificato e ai gruppi di cui l'utente è membro. **Esempio:** usare il comando seguente per elencare tutti gli utenti e i relativi ruoli all'interno di un account di automazione.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Ottenere l'assegnazione del ruolo](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) per concedere l'accesso a utenti, gruppi e applicazioni in un determinato ambito. **Esempio:** usare il comando seguente per creare un nuovo ruolo "Automation Operator" per un utente nell'ambito dell'account di automazione.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nuova assegnazione del ruolo](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Usare [Remove AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) per rimuovere l'accesso dell'utente, del gruppo o dell'applicazione specificata in un determinato ambito. **Esempio:** usare il comando seguente per creare un nuovo ruolo "Automation Operator" per un utente nell'ambito dell'account di automazione.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Nel cmdlet precedente sostituire il nome di accesso, l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'account di automazione con i dettagli del proprio account. Scegliere **Sì** quando viene richiesto di continuare per eliminare l'assegnazione del ruolo.


## Passaggi successivi
-  Per informazioni sulle diverse modalità disponibili per configurare il controllo degli accessi in base al ruolo in Automazione di Azure, vedere [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Per informazioni dettagliate sulle diverse modalità disponibili per l'avvio dei runbook, vedere [Avvio di un Runbook](automation-starting-a-runbook.md)
- Per informazioni sui diversi tipi, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)

<!---HONumber=AcomDC_0128_2016-->