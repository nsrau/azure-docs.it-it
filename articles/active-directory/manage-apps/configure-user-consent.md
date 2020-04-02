---
title: Configurare il consenso degli utenti finali alle applicazioni tramite Azure ADConfigure how end-users consent to applications using Azure AD
description: Informazioni su come gestire come e quando gli utenti possono acconsentire alle applicazioni che avranno accesso ai dati dell'organizzazione.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519616"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurare il modo in cui gli utenti finali consentono di concedere il consenso alle applicazioni

Le applicazioni possono integrarsi con la piattaforma Microsoft Identity per consentire agli utenti di accedere usando il proprio account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD) e per accedere ai dati dell'organizzazione per offrire esperienze avanzate basate sui dati. Autorizzazioni diverse consentono all'applicazione un diverso livello di accesso ai dati degli utenti e dell'organizzazione.

Per impostazione predefinita, gli utenti possono acconsentire alle applicazioni che accedono ai dati dell'organizzazione, anche se solo per alcune autorizzazioni. Ad esempio, per impostazione predefinita un utente può consentire a un'app di accedere alla propria cassetta postale o alle conversazioni di Teams per un team di cui l'utente è proprietario, ma non può consentire a un'app di concedere l'accesso automatico per la lettura e la scrittura in tutti i siti di SharePoint dell'organizzazione. Sebbene consentire agli utenti di acconsentire autonomamente consente agli utenti di acquisire facilmente applicazioni utili che si integrano con Microsoft 365, Azure e altri servizi, può rappresentare un rischio se non utilizzato e monitorato con attenzione.

Microsoft consiglia di disabilitare le future operazioni di consenso dell'utente per ridurre la superficie e ridurre questo rischio. Se il consenso dell'utente è disabilitato, le concessioni di consenso precedenti verranno comunque rispettate, ma tutte le operazioni future di consenso devono essere eseguite da un amministratore. Il consenso dell'amministratore a livello di tenant può essere richiesto dagli utenti tramite un flusso di lavoro di richiesta di [consenso dell'amministratore](configure-admin-consent-workflow.md) integrato o tramite i propri processi di supporto. Per ulteriori informazioni, vedere [Cinque passaggi per la protezione dell'infrastruttura di identità.](../../security/fundamentals/steps-secure-identity.md)

## <a name="configure-user-consent-to-applications"></a>Configurare il consenso dell'utente alle applicazioni
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Disabilitare o abilitare il consenso dell'utente dal portale di AzureDisable or enable user consent from the Azure portal

È possibile usare il portale di Azure per disabilitare o abilitare la capacità degli utenti di acconsentire alle applicazioni che accedono ai dati dell'organizzazione:You can use the Azure portal to disable or enable users' ability to consent to applications accessing your organization's data:

1. Accedere al [portale](https://portal.azure.com) di Azure come [amministratore globale.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
2. Selezionare **Azure Active Directory**, quindi Applicazioni **aziendali**, quindi **Impostazioni utente**.
3. Abilitare o disabilitare il consenso dell'utente con il controllo etichettato **Gli utenti possono acconsentire alle app che accedono ai dati aziendali per loro conto.**
4. (Facoltativo) Configurare il flusso di lavoro di richiesta di [consenso dell'amministratore](configure-admin-consent-workflow.md) per garantire che gli utenti che non sono autorizzati ad acconsentire a un'app possano richiedere l'approvazione.

> [!TIP]
> Per consentire agli utenti di richiedere la revisione di un amministratore di un'applicazione a cui l'utente non è autorizzato a concedere (ad esempio, perché il consenso dell'utente è stato disabilitato o perché l'applicazione richiede le autorizzazioni che l'utente non è autorizzato a concedere), è consigliabile configurare il flusso di lavoro di [consenso dell'amministratore.](configure-admin-consent-workflow.md)

### <a name="disable-or-enable-user-consent-using-powershell"></a>Disabilitare o abilitare il consenso dell'utente tramite PowerShellDisable or enable user consent using PowerShell

È possibile usare il modulo Azure AD PowerShell v1 ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) per abilitare o disabilitare la possibilità degli utenti di acconsentire alle applicazioni che accedono ai dati dell'organizzazione.

1. Accedere all'organizzazione eseguendo questo cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Verificare se il consenso dell'utente è abilitato eseguendo questo cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Abilitare o disabilitare il consenso dell'utente. Ad esempio, per disabilitare il consenso dell'utente, eseguire questo cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configurare il consenso del proprietario del gruppo alle app che accedono ai dati del gruppoConfigure group owner consent to apps accessing group data

> [!IMPORTANT]
> Le seguenti informazioni sono per una funzione imminente che consentirà ai proprietari dei gruppi di concedere l'accesso alle domande ai dati dei loro gruppi. Quando questa funzionalità viene rilasciata, verrà abilitata per impostazione predefinita. Anche se questa funzionalità non è ancora stata rilasciata ampiamente, è possibile utilizzare queste istruzioni per disabilitare la funzionalità prima del rilascio.

I proprietari dei gruppi possono autorizzare le applicazioni (ad esempio, le applicazioni pubblicate da fornitori di terze parti) ad accedere ai dati dell'organizzazione associati a un gruppo. Ad esempio, il proprietario di un team (proprietario del gruppo di Office 365 per il team) può consentire a un'app di leggere tutti i messaggi di Teams nel team o elencare il profilo di base dei membri di un gruppo.

> [!NOTE]
> Indipendentemente da questa impostazione, il proprietario di un gruppo può sempre aggiungere altri utenti o app direttamente come proprietari di un gruppo.

### <a name="configure-group-owner-consent-using-powershell"></a>Configurare il consenso del proprietario del gruppo tramite PowerShellConfigure group owner consent using PowerShell

È possibile usare il modulo Azure AD PowerShell Preview ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) per abilitare o disabilitare la possibilità dei proprietari dei gruppi di acconsentire alle applicazioni che accedono ai dati dell'organizzazione per i gruppi di cui sono proprietari.

1. Assicurarsi di usare il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (questo passaggio è importante se sono stati installati sia il modulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) che il modulo [AzureADPreview).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Connettersi ad Azure AD PowerShell.Connect to Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Recuperare il valore corrente per le impostazioni della directory *delle impostazioni dei criteri* di consenso nel tenant. Ciò richiede di verificare se le impostazioni della directory per questa funzionalità sono state create e, in caso contrario, utilizzando i valori del modello di impostazioni di directory corrispondente.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. Comprendere i valori delle impostazioni. Esistono due valori di impostazioni che definiscono quali utenti sarebbero in grado di consentire a un'app di accedere ai dati del proprio gruppo:There are two settings values that define which users would be able to allow an app to access their group's data:

    | Impostazione       | Type         | Descrizione  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean |  Flag che indica se ai proprietari dei gruppi è consentito concedere autorizzazioni specifiche del gruppo. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Se _EnableGroupSpecificConsent_ è impostato su "True" e questo valore è impostato sull'ID oggetto di un gruppo, i membri del gruppo identificato saranno autorizzati a concedere autorizzazioni specifiche del gruppo ai gruppi di cui sono proprietari. |

5. Aggiornare i valori delle impostazioni per la configurazione desiderata:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Salvare le impostazioni.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Configurare il consenso step-up basato sul rischio

Il consenso step-up basato sul rischio consente di ridurre l'esposizione degli utenti ad app dannose che effettuano richieste di [consenso illecite.](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Se Microsoft rileva una richiesta di consenso dell'utente finale rischiosa, la richiesta richiederà invece un "step-up" per il consenso dell'amministratore. Questa funzionalità è abilitata per impostazione predefinita, ma comporterà una modifica del comportamento solo quando è abilitato il consenso dell'utente finale.

Quando viene rilevata una richiesta di consenso rischiosa, la richiesta di consenso visualizza un messaggio che indica che è necessaria l'approvazione dell'amministratore. Se il flusso di lavoro della richiesta di [consenso dell'amministratore](configure-admin-consent-workflow.md) è abilitato, l'utente può inviare la richiesta a un amministratore per un'ulteriore revisione direttamente dalla richiesta di consenso. Se non è abilitato, verrà visualizzato il seguente messaggio:

* **AADSTS90094:** &lt;clientAppDisplayName&gt; necessita dell'autorizzazione per accedere alle risorse dell'organizzazione che solo un amministratore può concedere. Prima di usarla, è necessario chiedere a un amministratore di concedere l'autorizzazione a questa app.

In questo caso, un evento di controllo verrà registrato anche con una categoria di "ApplicationManagement", il tipo di attività "Consenso all'applicazione" e il motivo stato di "Applicazione rischiosa rilevata".

> [!IMPORTANT]
> Gli amministratori devono [valutare attentamente tutte le richieste](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) di consenso prima dell'approvazione, soprattutto quando Microsoft ha rilevato dei rischi.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Disabilitare o riabilitare il consenso step-up basato sul rischio tramite PowerShellDisable or re-enable risk-based step-up consent using PowerShell

È possibile usare il modulo Azure AD PowerShell Preview ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) per disabilitare il consenso dell'amministratore richiesto nei casi in cui Microsoft rileva i rischi o per riabilitarlo se è stato disabilitato in precedenza.

Questa operazione può essere eseguita utilizzando la stessa procedura illustrata in precedenza per configurare il consenso del proprietario del [gruppo tramite PowerShell](#configure-group-owner-consent-using-powershell), ma sostituendo un valore di impostazioni diverso. Esistono tre differenze nei passaggi:There are three differences in steps: 

1. Comprendere i valori di impostazione per il consenso step-up basato sul rischio:

    | Impostazione       | Type         | Descrizione  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Contrassegno che indica se il consenso dell'utente verrà bloccato quando viene rilevata una richiesta rischiosa. |

2. Sostituire il seguente valore nel passaggio 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. Sostituire uno dei seguenti elementi nel passaggio 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Passaggi successivi

[Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)

[Scopri come gestire il consenso alle applicazioni e valutare le richieste di consenso](manage-consent-requests.md)

[Concedere il consenso di amministratore a livello di tenant a un'applicazioneGrant tenant-wide admin consent to an application](grant-admin-consent.md)

[Autorizzazioni e consenso nella piattaforma di identità Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD on StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
