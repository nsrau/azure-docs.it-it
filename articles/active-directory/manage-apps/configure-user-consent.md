---
title: Configurare il modo in cui gli utenti finali accettano le applicazioni usando Azure AD
description: Informazioni su come gestire e quando gli utenti possono dare il consenso alle applicazioni che avranno accesso ai dati dell'organizzazione.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519616"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurare la modalità con cui gli utenti finali accettano le applicazioni

Le applicazioni possono essere integrate con la piattaforma di identità Microsoft per consentire agli utenti di accedere con l'account aziendale o dell'Istituto di istruzione in Azure Active Directory (Azure AD) e di accedere ai dati dell'organizzazione per offrire esperienze avanzate basate sui dati. Autorizzazioni diverse consentono all'applicazione di accedere a diversi livelli di accesso agli utenti e ai dati dell'organizzazione.

Per impostazione predefinita, gli utenti possono dare il consenso alle applicazioni che accedono ai dati dell'organizzazione, anche se solo per alcune autorizzazioni. Per impostazione predefinita, ad esempio, un utente può dare il consenso per consentire a un'app di accedere alla propria cassetta postale o alle conversazioni dei team per un team di cui l'utente è proprietario, ma non è in grado di concedere a un'app l'accesso automatico per la lettura e la scrittura in tutti i siti di SharePoint dell'organizzazione. Sebbene gli utenti possano acconsentire autonomamente, consentono agli utenti di acquisire facilmente applicazioni utili che si integrano con Microsoft 365, Azure e altri servizi, ma possono rappresentare un rischio se non vengono usate e monitorate con attenzione.

Microsoft consiglia di disabilitare le future operazioni di consenso utente per ridurre la superficie di attacco e mitigare questo rischio. Se il consenso dell'utente è disabilitato, le concessioni di consenso precedenti verranno comunque rispettate, ma tutte le future operazioni di consenso dovranno essere eseguite da un amministratore. Il consenso dell'amministratore a livello di tenant può essere richiesto dagli utenti tramite un [flusso di lavoro di richiesta di consenso dell'amministratore](configure-admin-consent-workflow.md) integrato o tramite processi di supporto personalizzati. Per altri dettagli, vedere [cinque passaggi per la sicurezza dell'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md) .

## <a name="configure-user-consent-to-applications"></a>Configurare il consenso dell'utente per le applicazioni
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Disabilitare o abilitare il consenso dell'utente dalla portale di Azure

È possibile usare la portale di Azure per disabilitare o abilitare la capacità degli utenti di fornire il consenso alle applicazioni che accedono ai dati dell'organizzazione:

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Selezionare **Azure Active Directory**, quindi **applicazioni aziendali**, quindi **impostazioni utente**.
3. Abilitare o disabilitare il consenso dell'utente con il controllo con etichetta **gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto**.
4. Opzionale Configurare il [flusso di lavoro della richiesta di consenso dell'amministratore](configure-admin-consent-workflow.md) per assicurarsi che gli utenti che non sono autorizzati a utilizzare un'app possano richiedere l'approvazione.

> [!TIP]
> Per consentire agli utenti di richiedere la revisione di un'applicazione di un amministratore a cui l'utente non è autorizzato (ad esempio, perché il consenso dell'utente è stato disabilitato o perché l'applicazione richiede le autorizzazioni che l'utente non è autorizzato a concedere), considerare la possibilità di [configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Disabilitare o abilitare il consenso dell'utente tramite PowerShell

È possibile usare il modulo Azure AD PowerShell V1 ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) per abilitare o disabilitare la capacità degli utenti di concedere il consenso alle applicazioni che accedono ai dati dell'organizzazione.

1. Accedere all'organizzazione eseguendo questo cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Controllare se il consenso dell'utente è abilitato eseguendo questo cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Abilita o Disabilita il consenso dell'utente. Per disabilitare il consenso dell'utente, ad esempio, eseguire il cmdlet seguente:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configurare il consenso del proprietario del gruppo per le app che accedono ai dati di gruppo

> [!IMPORTANT]
> Le informazioni seguenti si riferiscono a una funzionalità imminente che consentirà ai proprietari del gruppo di concedere l'accesso alle applicazioni ai dati dei relativi gruppi. Quando questa funzionalità viene rilasciata, verrà abilitata per impostazione predefinita. Anche se questa funzionalità non è ancora stata rilasciata, è possibile usare queste istruzioni per disabilitare la funzionalità in anticipo della versione.

I proprietari del gruppo possono autorizzare le applicazioni, ad esempio le applicazioni pubblicate da fornitori di terze parti, ad accedere ai dati dell'organizzazione associati a un gruppo. Ad esempio, un proprietario del team (proprietario del gruppo Office 365 per il team) può consentire a un'app di leggere tutti i messaggi dei team nel team o di elencare il profilo di base dei membri di un gruppo.

> [!NOTE]
> Indipendentemente da questa impostazione, a un proprietario del gruppo è sempre consentito aggiungere altri utenti o app direttamente come proprietari del gruppo.

### <a name="configure-group-owner-consent-using-powershell"></a>Configurare il consenso del proprietario del gruppo con PowerShell

È possibile usare il modulo Azure AD PowerShell Preview ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) per abilitare o disabilitare la capacità dei proprietari del gruppo di concedere il consenso alle applicazioni che accedono ai dati dell'organizzazione per i gruppi di cui sono proprietari.

1. Assicurarsi di usare il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (questo passaggio è importante se è stato installato il modulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) e il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) ).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Connettersi a Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Recuperare il valore corrente per le impostazioni della directory *delle impostazioni dei criteri di consenso* nel tenant. È necessario controllare se sono state create le impostazioni della directory per questa funzionalità e, in caso contrario, usando i valori del modello di impostazioni di directory corrispondente.

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

4. Comprendere i valori dell'impostazione. Sono disponibili due valori di impostazioni che definiscono gli utenti che possono consentire a un'app di accedere ai dati del gruppo:

    | Impostazione       | Type         | Descrizione  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean |  Flag che indica se i proprietari dei gruppi possono concedere autorizzazioni specifiche del gruppo. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Se _EnableGroupSpecificConsent_ è impostato su "true" e questo valore è impostato sull'ID oggetto di un gruppo, i membri del gruppo identificato saranno autorizzati a concedere autorizzazioni specifiche del gruppo ai gruppi di cui sono proprietari. |

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

## <a name="configure-risk-based-step-up-consent"></a>Configurare il consenso per step-up basato sul rischio

Il consenso basato sul rischio consente di ridurre l'esposizione degli utenti a app dannose che effettuano [richieste di consenso illecito](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Se Microsoft rileva una richiesta di consenso dell'utente finale rischiosa, la richiesta richiederà invece un "step-up" al consenso dell'amministratore. Questa funzionalità è abilitata per impostazione predefinita, ma comporterà una modifica del comportamento solo quando è abilitato il consenso dell'utente finale.

Quando viene rilevata una richiesta di consenso rischiosa, nella richiesta di consenso viene visualizzato un messaggio che indica che è necessaria l'approvazione dell'amministratore. Se il [flusso di lavoro della richiesta di consenso dell'amministratore](configure-admin-consent-workflow.md) è abilitato, l'utente può inviare la richiesta a un amministratore per un'ulteriore verifica diretta dalla richiesta di consenso. Se non è abilitato, verrà visualizzato il messaggio seguente:

* **AADSTS90094:** &lt;nomevisualizzatoappclient&gt; richiede l'autorizzazione per accedere alle risorse dell'organizzazione che solo un amministratore può concedere. Prima di usarla, è necessario chiedere a un amministratore di concedere l'autorizzazione a questa app.

In questo caso, verrà registrato anche un evento di controllo con una categoria "ApplicationManagement", un tipo di attività di "consenso per l'applicazione" e il motivo dello stato "applicazione rischiosa rilevata".

> [!IMPORTANT]
> Gli amministratori devono [valutare attentamente tutte le richieste di consenso](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) prima di approvare, specialmente quando Microsoft ha rilevato il rischio.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Disabilitare o riabilitare il consenso per step-up basato sul rischio usando PowerShell

È possibile usare il modulo Azure AD PowerShell Preview ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)), per disabilitare il consenso dell'amministratore, necessario nei casi in cui Microsoft rileva il rischio o riattivarlo se è stato disabilitato in precedenza.

Questa operazione può essere eseguita usando la stessa procedura illustrata in precedenza per [configurare il consenso del proprietario del gruppo tramite PowerShell](#configure-group-owner-consent-using-powershell), sostituendo però un valore di impostazioni diverso. Esistono tre differenze nei passaggi: 

1. Informazioni sui valori di impostazione per il consenso step-up basato sul rischio:

    | Impostazione       | Type         | Descrizione  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Flag che indica se il consenso dell'utente verrà bloccato quando viene rilevata una richiesta rischiosa. |

2. Sostituire il valore seguente nel passaggio 3:

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

[Informazioni su come gestire il consenso alle applicazioni e valutare le richieste di consenso](manage-consent-requests.md)

[Concedere il consenso dell'amministratore a livello di tenant a un'applicazione](grant-admin-consent.md)

[Autorizzazioni e consenso nella piattaforma di identità Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD in StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
