---
title: Gestire un account RunAs di Automazione di Azure
description: Questo articolo descrive come gestire l'account RunAs con PowerShell o nel portale di Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: c2d6e026f87211260a2cf45c0623806cc024b44e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87530667"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gestire un account RunAs di Automazione di Azure

Gli account RunAs in Automazione di Azure offrono l'autenticazione per la gestione delle risorse in Azure con i cmdlet di Azure. Quando si crea un account RunAs, viene creato un nuovo utente dell'entità servizio in Azure Active Directory (AD), a cui viene assegnato il ruolo Collaboratore a livello della sottoscrizione.

## <a name="types-of-run-as-accounts"></a>Tipi di account RunAs

Automazione di Azure usa due tipi di account RunAs:

* Account RunAs di Azure
* Account RunAs classico di Azure

>[!NOTE]
>Le sottoscrizioni Azure Cloud Solution Provider (CSP) supportano solo il modello di Azure Resource Manager. I servi diversi da Azure Resource Manager non sono disponibili nel programma. Quando si usa una sottoscrizione CSP, non viene creato l'account RunAs classico di Azure, ma l'account RunAs di Azure. Per altre informazioni sulle sottoscrizioni CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Per impostazione predefinita l'entità servizio per un account RunAs non dispone delle autorizzazioni per leggere Azure AD. Se si desidera aggiungere autorizzazioni per la lettura o la gestione di Azure AD, è necessario concedere le autorizzazioni all'entità servizio in **Autorizzazioni API**. Per altre informazioni, vedere [Aggiungere autorizzazioni per accedere ad API Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>account RunAs

L'account RunAs gestisce le risorse del [modello di distribuzione di Resource Manager](../azure-resource-manager/management/deployment-models.md) ed effettua le attività seguenti.

* Crea un'applicazione Azure AD da esportare con un certificato autofirmato, crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo Collaboratore per l'account nella sottoscrizione corrente. È possibile cambiare l'impostazione del certificato in Proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
  
* Crea un asset del certificato di Automazione denominato `AzureRunAsCertificate` nell'account di Automazione specificato. L'asset del certificato contiene la chiave privata del certificato usata dall'applicazione Azure AD.
  
* Crea un asset della connessione di Automazione denominato `AzureRunAsConnection` nell'account di Automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

### <a name="azure-classic-run-as-account"></a>Account RunAs classico di Azure

L'account RunAs classico di Azure gestisce le risorse del [modello di distribuzione classica](../azure-resource-manager/management/deployment-models.md). Per creare o rinnovare questo tipo di account, è necessario essere un coamministratore della sottoscrizione.

L'account RunAs classico di Azure esegue le attività seguenti.

  * Crea un certificato di gestione nella sottoscrizione.

  * Crea un asset del certificato di Automazione denominato `AzureClassicRunAsCertificate` nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.

  * Crea un asset della connessione di Automazione denominato `AzureClassicRunAsConnection` nell'account di Automazione specificato. L'asset della connessione contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset del certificato.

>[!NOTE]
>Gli account RunAs classici di Azure non vengono creati per impostazione predefinita mentre si crea un account di Automazione. Questo account viene creato separatamente seguendo i passaggi descritti più avanti in questo articolo.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Ottenere le autorizzazioni dell'account RunAs

Questa sezione definisce le autorizzazioni sia per i normali account RunAs che per gli account RunAs classici.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Ottenere le autorizzazioni per configurare gli account RunAs

Per creare o aggiornare un account RunAs, è necessario avere autorizzazioni e privilegi specifici. Un Amministratore di applicazioni in Azure Active Directory e un Proprietario in una sottoscrizione possono completare tutte le attività. Nel caso in cui i compiti siano separati, nella tabella seguente è disponibile un elenco delle attività, dei relativi cmdlet e delle autorizzazioni necessarie:

|Attività|Cmdlet  |Autorizzazioni minime  |Dove impostare le autorizzazioni|
|---|---------|---------|---|
|Creare un'applicazione Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Ruolo di Sviluppatore di applicazioni<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registrazioni app |
|Aggiungere una credenziale all'applicazione.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Amministratore di applicazioni o Amministratore globale<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registrazioni app|
|Creare e ottenere un'entità servizio di Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Amministratore di applicazioni o Amministratore globale<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registrazioni app|
|Assegna o ottiene il ruolo di Azure per l'entità specificata|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Proprietario o Amministratore Accesso utenti oppure disporre delle autorizzazioni seguenti:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Sottoscrizione](../role-based-access-control/role-assignments-portal.md)</br>Home > sottoscrizioni > \<subscription name\> -controllo di accesso (IAM)|
|Creare o rimuovere un certificato di Automazione|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Collaboratore nel gruppo di risorse         |Gruppo di risorse dell'account di Automazione|
|Creare o rimuovere una connessione di Automazione|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Collaboratore nel gruppo di risorse |Gruppo di risorse dell'account di Automazione|

<sup>1</sup> Gli utenti non amministratori nel tenant di Azure AD possono [registrare le applicazioni di AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) se l'opzione **Gli utenti possono registrare applicazioni** del tenant di Azure AD nella pagina Impostazioni utente è impostata su **Sì**. Se l'impostazione di registrazione dell'applicazione è impostata su **No**, l'utente che esegue questa azione deve essere quello definito in questa tabella.

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima di essere aggiunto al ruolo di amministratore globale della sottoscrizione, viene aggiunto come guest. In questa situazione viene visualizzato un `You do not have permissions to create…` avviso nella pagina **Aggiungi account di automazione** .

Se si è un membro dell'istanza Active Directory della sottoscrizione quando viene assegnato il ruolo di amministratore globale, è anche possibile ricevere un `You do not have permissions to create…` avviso nella pagina **Aggiungi account di automazione** . In questo caso l'utente può richiedere di essere rimosso dall'istanza di Active Directory della sottoscrizione e poi di essere aggiunto di nuovo, in modo da diventare un utente completo in Active Directory.

Per verificare che la situazione che ha prodotto il messaggio di errore è stata risolta:

1. Nel riquadro di Azure Active Directory del portale di Azure selezionare **Utenti e gruppi**.
2. Selezionare **Tutti gli utenti**.
3. Scegliere il nome, quindi selezionare **Profilo**. 
4. Verificare che il valore dell'attributo **Tipo utente** nel profilo dell'utente non sia impostato su **Guest**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Ottenere le autorizzazioni per configurare gli account RunAs classici

Per configurare o rinnovare gli account RunAs classici, è necessario avere il ruolo di coamministratore a livello di sottoscrizione. Per altre informazioni sulle autorizzazioni della sottoscrizione classica, vedere [Amministratori della sottoscrizione classica di Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Creare un account RunAs nel portale di Azure

Eseguire la procedura seguente per aggiornare l'account di Automazione di Azure nel portale di Azure. Creare gli account RunAs e RunAs classico separatamente. Se non è necessario gestire le risorse classiche, è sufficiente creare l'account RunAs di Azure.

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

2. Cercare e selezionare **Account di Automazione**.

3. Nella pagina Account di automazione selezionare l'account di Automazione dall'elenco.

4. Nel riquadro a sinistra selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

5. A seconda del tipo di account necessario, selezionare **Account RunAs di Azure** o **Account RunAs classico di Azure**. 

6. A seconda del tipo di account interessato, usare il riquadro **Aggiungi account RunAs di Azure** o **Aggiungi account RunAs classico di Azure**. Dopo aver esaminato le informazioni generali, fare clic su **Crea**.

7. Mentre Azure crea l'account RunAs, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu. Viene anche visualizzato un banner che indica che l'account è in fase di creazione. Il completamento del processo potrebbe richiedere alcuni minuti.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminare un account RunAs o un account RunAs classico

Questa sezione illustra come eliminare un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account, è possibile ricrearlo nel portale di Azure.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel riquadro a sinistra selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

3. Nella pagina delle proprietà dell'account RunAs selezionare l'account RunAs o l'account RunAs classico che si desidera eliminare. 

4. Nel riquadro Proprietà per l'account selezionato fare quindi clic su **Elimina**.

   ![Eliminare un account RunAs](media/manage-runas-account/automation-account-delete-runas.png)

5. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

6. Dopo aver eliminato l'account, è possibile crearlo di nuovo nella pagina delle proprietà dell'account RunAs selezionando l'opzione di creazione **Account RunAs di Azure**.

   ![Ricreare l'account RunAs di Automazione](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Rinnovare un certificato autofirmato

Il certificato autofirmato creato per l'account RunAs scade un anno dopo la data di creazione. Prima della scadenza dell'account RunAs, è necessario rinnovare il certificato. È possibile rinnovarlo in qualsiasi momento prima della scadenza. 

Quando si rinnova il certificato autofirmato, il certificato valido corrente viene mantenuto per evitare di influire negativamente su eventuali runbook messi in coda o in esecuzione, che eseguono l'autenticazione con l'account RunAs. Il certificato rimane valido fino alla relativa data di scadenza.

>[!NOTE]
>Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminare e creare nuovamente il certificato autofirmato.

>[!NOTE]
>Se l'account RunAs è stato configurato per l'uso di un certificato rilasciato dall'autorità di certificazione globale (enterprise) e si usa questa opzione per rinnovare il certificato autofirmato, il certificato globale (enterprise) viene sostituito da un certificato autofirmato.

Usare la procedura seguente per rinnovare il certificato autofirmato.

1. Nel Portale di Azure aprire l'account di Automazione.

1. Selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

    ![Riquadro delle proprietà dell'account di Automazione](media/manage-runas-account/automation-account-properties-pane.png)

1. Nella pagina delle proprietà dell'Account RunAs selezionare l'account RunAs o l'account RunAs classico per cui si vuole rinnovare il certificato.

1. Nel riquadro delle Proprietà per l'account selezionato fare clic su **Rinnova certificato**.

    ![Rinnovare il certificato per l'account RunAs](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="limit-run-as-account-permissions"></a>Limitare le autorizzazioni dell'account RunAs

Per controllare la destinazione di Automazione rispetto alle risorse in Azure, è possibile eseguire lo script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Questo script modifica l'entità servizio dell'account RunAs esistente per creare e usare una definizione del ruolo personalizzata. Il ruolo dispone delle autorizzazioni per tutte le risorse eccetto [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Dopo aver eseguito lo script **Update-AutomationRunAsAccountRoleAssignments.ps1**, i runbook che accedono a Key Vault con gli account RunAs non funzionano più. Prima di eseguire lo script, è necessario rivedere i runbook nell'account per le chiamate ad Azure Key Vault. Per consentire l'accesso a Key Vault dai runbook di Automazione di Azure, è necessario [aggiungere l'account RunAs alle autorizzazioni di Key Vault](#add-permissions-to-key-vault).

Se è necessario limitare ancora le operazioni che l'entità servizio RunAs può eseguire, aggiungere altri tipi di risorse all'elemento `NotActions` della definizione di ruolo personalizzata. L'esempio seguente limita l'accesso a `Microsoft.Compute/*`. Se si aggiunge questo tipo di risorsa a `NotActions` per la definizione del ruolo, il ruolo non potrà accedere alle risorse di calcolo. Per altre informazioni sulle definizioni dei ruoli, vedere [Informazioni sulle definizioni dei ruoli per le risorse di Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

È possibile determinare se l'entità servizio usata dall'account RunAs si trova nella definizione del ruolo Collaboratore o in una definizione personalizzata. 

1. Passare all'account di Automazione selezionare **Account RunAs** nella sezione di impostazioni dell'account.
2. Selezionare **Account RunAs di Azure**. 
3. Selezionare **Ruolo** per individuare la definizione del ruolo usata.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Verificare il ruolo account RunAs." lightbox="media/manage-runas-account/verify-role-expanded.png":::


È anche possibile determinare la definizione del ruolo usata dagli account RunAs per più sottoscrizioni o account di Automazione. A tale scopo, usare lo script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) nella PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Aggiungere autorizzazioni a Key Vault

È possibile consentire ad Automazione di Azure di verificare se Key Vault e l'entità servizio dell'account RunAs usano una definizione del ruolo personalizzata. È necessario:

* Concedere le autorizzazioni a Key Vault.
* Impostare i criteri di accesso.

È possibile usare lo script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) nella PowerShell Gallery per concedere le autorizzazioni dell'account RunAs a Key Vault. Per informazioni dettagliate sull'impostazione delle autorizzazioni in Key Vault, vedere [Concedere l'accesso delle applicazioni a un insieme di credenziali delle chiavi](../key-vault/general/group-permissions-for-apps.md).

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Risolvere i problemi di configurazione errata per gli account RunAs

Alcuni elementi di configurazione necessari per l'account RunAs o l'account RunAs classico potrebbero essere stati eliminati o non essere stati creati correttamente durante la configurazione iniziale. Tra le istanze di configurazione errata possibili ci sono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo di Collaboratore
* Entità servizio o applicazione in Azure AD

Per tali istanze di configurazione errata, l'account di Automazione rileva le modifiche e visualizza lo stato *Incompleto* nel riquadro delle proprietà dell'Account RunAs.

![Stato di configurazione Incompleto dell'account RunAs](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando si seleziona l'account RunAs, il riquadro delle proprietà dell'account mastra il messaggio di errore seguente:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account.

## <a name="next-steps"></a>Passaggi successivi

* [Oggetti applicazione e oggetti entità servizio](../active-directory/develop/app-objects-and-service-principals.md).
* [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).
