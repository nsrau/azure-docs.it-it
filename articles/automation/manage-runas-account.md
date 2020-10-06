---
title: Gestire un account RunAs di Automazione di Azure
description: Questo articolo descrive come gestire l'account RunAs con PowerShell o nel portale di Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 3357cb40ff476a3cc0bce259930068aeccf2c10c
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767376"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gestire un account RunAs di Automazione di Azure

Gli account RunAs in automazione di Azure forniscono l'autenticazione per la gestione delle risorse nel modello di distribuzione Azure Resource Manager o di Azure classico usando manuali operativi di automazione e altre funzionalità di automazione. Questo articolo fornisce indicazioni su come gestire un account RunAs o un account RunAs classico.

Per altre informazioni sull'autenticazione dell'account di automazione di Azure e le linee guida relative agli scenari di automazione dei processi, vedere [Panoramica dell'autenticazione degli account di automazione](automation-security-overview.md).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Autorizzazioni account RunAs

Questa sezione definisce le autorizzazioni sia per i normali account RunAs che per gli account RunAs classici.

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

Se si è un membro dell'istanza Active Directory della sottoscrizione a cui è assegnato il ruolo di amministratore globale, è anche possibile ricevere un `You do not have permissions to create…` avviso nella pagina **Aggiungi account di automazione** . In questo caso l'utente può richiedere di essere rimosso dall'istanza di Active Directory della sottoscrizione e poi di essere aggiunto di nuovo, in modo da diventare un utente completo in Active Directory.

Per verificare che la situazione che ha prodotto il messaggio di errore è stata risolta:

1. Nel riquadro di Azure Active Directory del portale di Azure selezionare **Utenti e gruppi**.
2. Selezionare **Tutti gli utenti**.
3. Scegliere il nome, quindi selezionare **Profilo**.
4. Verificare che il valore dell'attributo **Tipo utente** nel profilo dell'utente non sia impostato su **Guest**.

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>Autorizzazioni necessarie per creare o gestire gli account RunAs classici

Per configurare o rinnovare gli account RunAs classici, è necessario avere il ruolo di coamministratore a livello di sottoscrizione. Per altre informazioni sulle autorizzazioni della sottoscrizione classica, vedere [Amministratori della sottoscrizione classica di Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Creare un account RunAs nel portale di Azure

Eseguire la procedura seguente per aggiornare l'account di Automazione di Azure nel portale di Azure. Creare gli account RunAs e RunAs classico separatamente. Se non è necessario gestire le risorse classiche, è sufficiente creare l'account RunAs di Azure.

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

2. Cercare e selezionare **Account di Automazione**.

3. Nella pagina Account di automazione selezionare l'account di Automazione dall'elenco.

4. Nel riquadro sinistro selezionare **account RunAs** nella sezione **Impostazioni account** .

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Selezionare l'opzione account RunAs.":::

5. A seconda dell'account necessario, usare l' **account RunAs di Azure** o il riquadro **account RunAs classico di Azure** . Dopo aver esaminato le informazioni generali, fare clic su **Crea**.

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="Selezionare l'opzione account RunAs.":::

6. Mentre Azure crea l'account RunAs, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu. Viene anche visualizzato un banner che indica che l'account è in fase di creazione. Il completamento del processo potrebbe richiedere alcuni minuti.

## <a name="create-a-run-as-account-using-powershell"></a>Creare un account RunAs tramite PowerShell

L'elenco seguente include i requisiti per creare un account RunAs in PowerShell usando uno script fornito. Questi requisiti si applicano a entrambi i tipi di account RunAs.

* Windows 10 o Windows Server 2016 con i moduli di Azure Resource Manager 3.4.1 e versioni successive. Lo script di PowerShell non supporta versioni precedenti di Windows.
* Azure PowerShell PowerShell 6.2.4 o versione successiva. Per informazioni, vedere [come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).
* Un account di Automazione, a cui si fa riferimento con il valore per i parametri `AutomationAccountName` e `ApplicationDisplayName`.
* Autorizzazioni equivalenti a quanto indicato in [Autorizzazioni necessarie per la configurazione degli account RunAs](#permissions).

Per ottenere i valori per `AutomationAccountName` , `SubscriptionId` e `ResourceGroupName` , che sono parametri obbligatori per lo script di PowerShell, completare i passaggi seguenti.

1. Nel portale di Azure selezionare **Account di Automazione**.

1. Nella pagina Account di Automazione selezionare l'account di Automazione.

1. Nella sezione delle impostazioni per l'account selezionare **Proprietà**.

1. Prendere nota dei valori per **nome**, **ID sottoscrizione**e **gruppo di risorse** nella pagina delle **Proprietà** .

   ![Pagina delle proprietà dell'account di Automazione](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script di PowerShell per creare un account RunAs

Lo script di PowerShell include il supporto per diverse configurazioni.

* Creare un account RunAs usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato rilasciato dall'autorità di certificazione globale (enterprise).
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici.

1. Scaricare e salvare lo script in una cartella locale usando il comando seguente.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Avviare PowerShell con diritti utente elevati e passare alla cartella che contiene lo script.

3. Eseguire uno dei comandi seguenti per creare un account RunAs e/o un account RunAs classico in base ai propri requisiti.

    * Creare un account RunAs usando un certificato autofirmato.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Creare un account RunAs e un account RunAs classico usando un certificato enterprise.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Se è stato creato un account RunAs classico con un certificato pubblico globale (enterprise), ovvero un file con estensione CER, usare questo certificato. Lo script lo crea e lo salva nella cartella dei file temporanei nel computer, nel profilo utente `%USERPROFILE%\AppData\Local\Temp` usato per eseguire la sessione di PowerShell. Vedere [Caricamento di un certificato dell'API di gestione nel portale di Azure](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Dopo l'esecuzione dello script, all'utente viene richiesto di autenticarsi con Azure. Accedere con un account membro del ruolo amministratori della sottoscrizione. Se si sta creando un account RunAs classico, è necessario che l'account sia un coamministratore della sottoscrizione.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminare un account RunAs o un account RunAs classico

Questa sezione illustra come eliminare un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account RunAs, è possibile ricrearlo nel portale di Azure o con lo script di PowerShell fornito.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel riquadro a sinistra selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

3. Nella pagina delle proprietà dell'account RunAs selezionare l'account RunAs o l'account RunAs classico che si desidera eliminare.

4. Nel riquadro Proprietà per l'account selezionato fare quindi clic su **Elimina**.

   ![Eliminare un account RunAs](media/manage-runas-account/automation-account-delete-runas.png)

5. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

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

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Selezionare l'opzione account RunAs." lightbox="media/manage-runas-account/verify-role-expanded.png":::

È anche possibile determinare la definizione del ruolo usata dagli account RunAs per più sottoscrizioni o account di Automazione. A tale scopo, usare lo script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) nella PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Aggiungere autorizzazioni a Key Vault

È possibile consentire ad Automazione di Azure di verificare se Key Vault e l'entità servizio dell'account RunAs usano una definizione del ruolo personalizzata. È necessario:

* Concedere le autorizzazioni a Key Vault.
* Impostare i criteri di accesso.

È possibile usare lo script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) nella PowerShell Gallery per concedere le autorizzazioni dell'account RunAs a Key Vault. Per ulteriori informazioni sull'impostazione delle autorizzazioni per Key Vault, vedere [assegnare un criterio di accesso key Vault](/azure/key-vault/general/assign-access-policy-powershell) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Risolvere i problemi di configurazione errata per gli account RunAs

Alcuni elementi di configurazione necessari per l'account RunAs o l'account RunAs classico potrebbero essere stati eliminati o non essere stati creati correttamente durante la configurazione iniziale. Tra le istanze di configurazione errata possibili ci sono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo di Collaboratore
* Entità servizio o applicazione in Azure AD

Per tali istanze di configurazione errata, l'account di Automazione rileva le modifiche e visualizza lo stato *Incompleto* nel riquadro delle proprietà dell'Account RunAs.

![Stato di configurazione Incompleto dell'account RunAs](media/manage-runas-account/automation-account-runas-config-incomplete.png)

Quando si seleziona l'account RunAs, il riquadro delle proprietà dell'account mastra il messaggio di errore seguente:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account RunAs.

## <a name="next-steps"></a>Passaggi successivi

* [Oggetti applicazione e oggetti entità servizio](../active-directory/develop/app-objects-and-service-principals.md).
* [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).
