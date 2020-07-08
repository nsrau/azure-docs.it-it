---
title: Configurare la modalità con cui gli utenti finali forniscono il consenso alle applicazioni usando Azure AD
description: Di seguito viene descritto come gestire la modalità e le tempistiche con cui gli utenti possono fornire il consenso alle applicazioni che avranno accesso ai dati dell'organizzazione.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763466"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurare la modalità con cui gli utenti finali forniscono il consenso alle applicazioni

È possibile integrare le applicazioni con Microsoft Identity Platform per consentire agli utenti di accedere con il proprio account aziendale o dell'istituto di istruzione e consultare i dati dell'organizzazione per offrire esperienze avanzate basate sui dati.

Prima che un'applicazione possa accedere ai dati dell'organizzazione, un utente deve concedere le autorizzazioni dell'applicazione a tale scopo. Autorizzazioni diverse consentono livelli di accesso diversi. Per impostazione predefinita, tutti gli utenti possono fornire il consenso alle applicazioni per le autorizzazioni che non richiedono il consenso dell'amministratore. Per impostazione predefinita, ad esempio, un utente può fornire il consenso in modo che un'app acceda alla cassetta postale ma non può consentire a un'app di accedere senza limitazioni per la lettura e la scrittura di tutti i file dell'organizzazione.

Con la possibilità di concedere alle app l'accesso ai dati, gli utenti possono acquisire facilmente applicazioni utili ed essere produttivi. Tuttavia, in alcune situazioni questa configurazione può rappresentare un rischio se non viene monitorata e controllata con attenzione.

## <a name="user-consent-settings"></a>Impostazioni per il consenso utente

Per controllare i casi in cui gli utenti possono fornire il consenso alle applicazioni, scegliere i criteri di consenso applicabili a tutti gli utenti. Di seguito sono riportate le tre opzioni dei criteri di consenso:

* **Disable user consent** (Disabilita consenso utente): gli utenti non possono concedere le autorizzazioni alle applicazioni. Gli utenti possono continuare ad accedere alle app a cui hanno precedentemente fornito il consenso o che sono autorizzate dagli amministratori per loro conto, ma non potranno fornire autonomamente il consenso a nuove autorizzazioni o a nuove app. Solo gli utenti a cui è stato concesso un ruolo della directory che include l'autorizzazione per fornire il consenso saranno in grado di consentire nuove autorizzazioni o nuove app.

* **Users can consent to apps from verified publishers, but only for permissions you select (preview)** (Gli utenti possono fornire il consenso alle app di editori verificati, ma solo per le autorizzazioni selezionate (anteprima)): tutti gli utenti possono fornire il consenso solo alle app pubblicate da un [editore verificato](../develop/publisher-verification-overview.md) e alle app registrate nel tenant. Gli utenti possono solo fornire il consenso alle autorizzazioni classificate come a "Impatto basso".

  Assicurarsi di [classificare le autorizzazioni](#configure-permission-classifications-preview) per selezionare le autorizzazioni a cui gli utenti possono fornire il consenso.

* **Users can consent to all apps** (Gli utenti possono fornire il consenso a tutte le app): questa opzione consente a tutti gli utenti di fornire il consenso a qualsiasi autorizzazione, che non richiede il consenso dell'amministratore, per qualsiasi applicazione. 

   Per ridurre il rischio di applicazioni dannose che tentano di ingannare gli utenti per ottenere l'accesso ai dati dell'organizzazione, è consigliabile fornire il consenso utente solo alle applicazioni pubblicate da un [editore verificato](../develop/publisher-verification-overview.md).

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Configurare le impostazioni di consenso utente dal portale di Azure

Per configurare le impostazioni di consenso utente tramite il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Consenso e autorizzazioni** > **Impostazioni per il consenso utente**.
1. In **User consent for applications** (Consenso utente per le applicazioni) selezionare l'impostazione di consenso che si intende configurare per tutti gli utenti.
1. Per salvare le impostazioni, fare clic su **Save** (Salva).

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Impostazioni per il consenso utente":::

> [!TIP]
> Si consideri [l'abilitazione del flusso di lavoro di consenso amministratore](configure-admin-consent-workflow.md) per consentire agli utenti di richiedere all'amministratore la revisione e l'approvazione di un'applicazione per la quale l'utente non è autorizzato a fornire il consenso, ad esempio quando il consenso dell'utente è stato disabilitato o quando un'applicazione richiede le autorizzazioni che l'utente non è autorizzato a concedere.

### <a name="configure-user-consent-settings-using-powershell"></a>Configurare le impostazioni di consenso utente tramite PowerShell

È possibile usare il modulo Azure AD PowerShell Preview più recente, [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), per scegliere i criteri di consenso che governano il consenso dell'utente per le applicazioni.

* **Disable user consent** (Disabilita consenso utente): per disabilitare il consenso dell'utente, impostare i criteri che regolano il consenso dell'utente come vuoti:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Allow user consent for apps from verified publishers, for selected permissions (preview)** (Abilita il consenso utente per app di editori verificati, per autorizzazioni selezionate (anteprima)): per abilitare il consenso utente limitato ad app di editori verificati e app registrate nel tenant e solo per le autorizzazioni classificate come a "Impatto basso", configurare i criteri di consenso predefiniti denominati `microsoft-user-default-low`:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Assicurarsi di [classificare le autorizzazioni](#configure-permission-classifications-preview) per selezionare le autorizzazioni a cui gli utenti possono fornire il consenso.

* **Allow user consent for all apps** (Abilita consenso utente per tutte le app): per abilitare il consenso dell'utente per tutte le app:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Questa opzione consente a tutti gli utenti di fornire il consenso a qualsiasi autorizzazione, che non richiede il consenso dell'amministratore, per qualsiasi applicazione. È consigliabile abilitare il consenso utente solo per le app di editori verificati.

## <a name="configure-permission-classifications-preview"></a>Configurare le classificazioni delle autorizzazioni (anteprima)

Le classificazioni delle autorizzazioni consentono di identificare l'effetto delle diverse autorizzazioni in base ai criteri dell'organizzazione e alle valutazioni dei rischi. È possibile ad esempio usare le classificazioni delle autorizzazioni nei criteri di consenso per identificare il set di autorizzazioni a cui gli utenti possono fornire il consenso.

> [!NOTE]
> Attualmente è supportata solo la classificazione delle autorizzazioni a "Impatto basso". Solo le autorizzazioni delegate che non richiedono il consenso dell'amministratore possono essere classificate come a "Impatto basso".

### <a name="classify-permissions-using-the-azure-portal"></a>Classificare le autorizzazioni tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Consenso e autorizzazioni** > **Permission classifications (Classificazioni autorizzazioni)** .
1. Scegliere **Aggiungi autorizzazioni** per classificare un'altra autorizzazione come a "Impatto basso". 
1. Selezionare l'API e quindi selezionare le autorizzazioni delegate.

In questo esempio è stato classificato il set minimo di autorizzazioni necessarie per Single Sign-On:

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Classificazioni delle autorizzazioni":::

> [!TIP]
> Per l'API Microsoft Graph, le autorizzazioni minime necessarie per eseguire Single Sign-on di base sono `openid`, `profile`, `User.Read` e `offline_access`. Con queste autorizzazioni un'app può leggere i dettagli del profilo dell'utente che ha eseguito l'accesso e può gestire l'accesso anche quando l'utente non usa più l'app.

### <a name="classify-permissions-using-powershell"></a>Classificare le autorizzazioni tramite PowerShell

Per classificare le autorizzazioni, è possibile usare il modulo Azure AD PowerShell Preview più recente, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview). Le classificazioni delle autorizzazioni vengono configurate nell'oggetto **ServicePrincipal** dell'API che pubblica le autorizzazioni.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Per leggere le classificazioni delle autorizzazioni correnti di un'API:

1. Recuperare l'oggetto **ServicePrincipal** dell'API. Qui viene recuperato l'oggetto ServicePrincipal dell'API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Leggere le classificazioni delle autorizzazioni delegate per l'API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Per classificare un'autorizzazione come a "Impatto basso":

1. Recuperare l'oggetto **ServicePrincipal** dell'API. Qui viene recuperato l'oggetto ServicePrincipal dell'API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Individuare l'autorizzazione delegata che si vuole classificare:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Impostare la classificazione delle autorizzazioni usando il nome e l'ID dell'autorizzazione:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Per rimuovere la classificazione delle autorizzazioni delegate:

1. Recuperare l'oggetto **ServicePrincipal** dell'API. Qui viene recuperato l'oggetto ServicePrincipal dell'API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Individuare la classificazione delle autorizzazioni delegate che si vuole rimuovere:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Eliminare la classificazione delle autorizzazioni:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configurare il consenso del proprietario del gruppo per le app che accedono ai dati di gruppo

I proprietari dei gruppi possono autorizzare le applicazioni, ad esempio quelle pubblicate da fornitori di terze parti, ad accedere ai dati dell'organizzazione associati a un gruppo. Ad esempio, il proprietario di un team in Microsoft Teams può consentire a un'app di leggere tutti i messaggi del team oppure elencare il profilo di base dei membri di un gruppo.

È possibile configurare gli utenti che possono fornire il consenso alle app che accedono ai dati dei gruppi oppure disabilitare questa funzionalità.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Configurare il consenso del proprietario del gruppo usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Consenso e autorizzazioni** > **Impostazioni per il consenso utente**.
3. In **Group owner consent for apps accessing data** (Consenso del proprietario del gruppo per le app che accedono ai dati) selezionare l'opzione che si vuole abilitare.
4. Per salvare le impostazioni, fare clic su **Save** (Salva).

In questo esempio, tutti i proprietari del gruppo possono fornire il consenso alle app che accedono ai dati dei gruppi:

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Impostazioni di consenso del proprietario del gruppo":::

### <a name="configure-group-owner-consent-using-powershell"></a>Configurare il consenso del proprietario del gruppo usando PowerShell

È possibile usare il modulo Azure AD PowerShell Preview, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), per abilitare o disabilitare la capacità dei proprietari dei gruppi di fornire il consenso alle applicazioni che accedono ai dati dell'organizzazione per i gruppi di cui sono proprietari.

1. Accertarsi di usare il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview). Questo passaggio è importante se sono stati installati sia il modulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) sia il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Connettersi ad Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Recuperare il valore corrente per le impostazioni della directory **Consent Policy Settings** (Impostazioni criteri di consenso) nel tenant. È necessario controllare se sono state create le impostazioni della directory per questa funzionalità e, in caso contrario, usare i valori del modello di impostazioni di directory corrispondente.

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

1. Esaminare i valori delle impostazioni. Sono disponibili due valori delle impostazioni che definiscono gli utenti che possono consentire a un'app di accedere ai dati del gruppo:

    | Impostazione       | Type         | Descrizione  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean | Flag che indica se i proprietari dei gruppi possono concedere autorizzazioni specifiche del gruppo. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Se _EnableGroupSpecificConsent_ è impostato su "True" e questo valore è impostato sull'ID oggetto di un gruppo, i membri del gruppo identificato saranno autorizzati a concedere autorizzazioni specifiche ai gruppi di cui sono proprietari. |

1. Aggiornare i valori delle impostazioni per la configurazione desiderata:

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

1. Salvare le impostazioni.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Configurare il consenso incrementale basato sul rischio

Il consenso incrementale basato sul rischio consente di ridurre l'esposizione degli utenti ad app dannose che effettuano [richieste di consenso illecite](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Se Microsoft rileva una richiesta di consenso dell'utente finale rischiosa, sarà necessario un "passaggio" al consenso dell'amministratore. Questa funzionalità è abilitata per impostazione predefinita, ma comporterà una modifica del comportamento solo quando è abilitato il consenso dell'utente finale.

Quando viene rilevata una situazione di rischio, nella richiesta di consenso viene visualizzato un messaggio che indica che è necessaria l'approvazione dell'amministratore. Se [il flusso di lavoro della richiesta di consenso dell'amministratore](configure-admin-consent-workflow.md) è abilitato, l'utente può inviare la richiesta a un amministratore per un'ulteriore verifica direttamente dalla richiesta di consenso. In caso contrario, verrà visualizzato il messaggio seguente:

* **AADSTS90094:** &lt;NomeVisualizzatoAppClient&gt; richiede l'autorizzazione per accedere alle risorse dell'organizzazione che solo un amministratore può concedere. Prima di usarla, è necessario chiedere a un amministratore di concedere l'autorizzazione a quest'app.

In questo caso, verrà registrato anche un evento di controllo con una categoria "ApplicationManagement", un tipo di attività di "Consent to application" (Consenso per l'applicazione) e il motivo dello stato "Risky application detected" (Applicazione rischiosa rilevata).

> [!IMPORTANT]
> Gli amministratori devono [valutare attentamente tutte le richieste di consenso](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) prima di approvarne una, soprattutto quando Microsoft ha rilevato il rischio.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Disabilitare o riabilitare il consenso incrementale basato sul rischio usando PowerShell

È possibile usare il modulo Azure AD PowerShell Preview, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), per disabilitare il passaggio al consenso amministratore necessario nei casi in cui Microsoft rilevi un rischio oppure riabilitarlo se è stato disabilitato in precedenza.

È possibile eseguire questa operazione usando la stessa procedura illustrata in precedenza per la [configurazione del consenso del proprietario del gruppo tramite PowerShell](#configure-group-owner-consent-using-powershell), ma sostituendo un valore delle impostazioni diverso. Esistono tre differenze nella procedura: 

1. Esaminare i valori delle impostazioni per il consenso incrementale basato sul rischio:

    | Impostazione       | Type         | Descrizione  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Flag che indica se il consenso dell'utente verrà bloccato quando viene rilevata una richiesta rischiosa. |

1. Sostituire il valore seguente nel passaggio 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Sostituire uno dei seguenti elementi nel passaggio 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni:

* [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)
* [Informazioni su come gestire il consenso alle applicazioni e valutare le richieste di consenso](manage-consent-requests.md)
* [Concedere a un'applicazione il consenso amministratore a livello di tenant](grant-admin-consent.md)
* [Autorizzazioni e consenso in Microsoft Identity Platform](../develop/active-directory-v2-scopes.md)

Per ottenere assistenza o trovare risposte alle domande:
* [Azure AD in StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
