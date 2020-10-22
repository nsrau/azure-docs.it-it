---
title: Configurare il consenso del proprietario del gruppo per le app che accedono ai dati di gruppo usando Azure AD
description: Informazioni su come gestire se i proprietari di gruppi e team possono dare il consenso alle applicazioni che avranno accesso ai dati del gruppo o del team.
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
ms.openlocfilehash: c4203c572ed040b37a377e8d4a6b1bc82c883ebd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368503"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configurare il consenso del proprietario del gruppo per le app che accedono ai dati di gruppo

I proprietari di gruppi e team possono autorizzare le applicazioni, ad esempio le applicazioni pubblicate da fornitori di terze parti, ad accedere ai dati dell'organizzazione associati a un gruppo. Ad esempio, il proprietario di un team in Microsoft Teams può consentire a un'app di leggere tutti i messaggi del team oppure elencare il profilo di base dei membri di un gruppo. Per altre informazioni, vedere il [consenso specifico per le risorse in Microsoft teams](https://docs.microsoft.com/microsoftteams/resource-specific-consent) .

## <a name="manage-group-owner-consent-to-apps"></a>Gestire il consenso del proprietario del gruppo alle app

È possibile configurare quali utenti possono concedere il consenso alle app che accedono ai dati dei gruppi o dei team oppure è possibile disabilitare questa operazione per tutti gli utenti.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Seguire questa procedura per gestire il consenso del proprietario del gruppo alle app che accedono ai dati di gruppo:

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../roles/permissions-reference.md#global-administrator--company-administrator).
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Consenso e autorizzazioni** > **Impostazioni per il consenso utente**.
3. In **Group owner consent for apps accessing data** (Consenso del proprietario del gruppo per le app che accedono ai dati) selezionare l'opzione che si vuole abilitare.
4. Per salvare le impostazioni, fare clic su **Save** (Salva).

In questo esempio, tutti i proprietari del gruppo possono fornire il consenso alle app che accedono ai dati dei gruppi:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Impostazioni di consenso del proprietario del gruppo":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

È possibile usare il modulo Azure AD PowerShell Preview, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true), per abilitare o disabilitare la capacità dei proprietari dei gruppi di fornire il consenso alle applicazioni che accedono ai dati dell'organizzazione per i gruppi di cui sono proprietari.

1. Accertarsi di usare il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true). Questo passaggio è importante se sono stati installati sia il modulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) sia il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)).

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

    | Impostazione       | Tipo         | Descrizione  |
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

---

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni:

* [Configurare le impostazioni di consenso dell'utente](configure-user-consent.md)
* [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)
* [Informazioni su come gestire il consenso alle applicazioni e valutare le richieste di consenso](manage-consent-requests.md)
* [Concedere a un'applicazione il consenso amministratore a livello di tenant](grant-admin-consent.md)
* [Autorizzazioni e consenso in Microsoft Identity Platform](../develop/active-directory-v2-scopes.md)

Per ottenere assistenza o trovare risposte alle domande:
* [Azure AD in StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
