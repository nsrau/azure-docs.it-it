---
title: Configurare le classificazioni delle autorizzazioni con Azure AD
description: Informazioni su come gestire le classificazioni delle autorizzazioni delegate.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0add5001e2e62e26d448b06927210c14f17729f1
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804314"
---
# <a name="configure-permission-classifications"></a>Configurare le classificazioni delle autorizzazioni

Le classificazioni delle autorizzazioni consentono di identificare l'effetto delle diverse autorizzazioni in base ai criteri dell'organizzazione e alle valutazioni dei rischi. È possibile ad esempio usare le classificazioni delle autorizzazioni nei criteri di consenso per identificare il set di autorizzazioni a cui gli utenti possono fornire il consenso.

## <a name="manage-permission-classifications"></a>Gestire le classificazioni delle autorizzazioni

Attualmente è supportata solo la classificazione delle autorizzazioni a "Impatto basso". Solo le autorizzazioni delegate che non richiedono il consenso dell'amministratore possono essere classificate come a "Impatto basso".

> [!TIP]
> Le autorizzazioni minime necessarie per eseguire l'accesso di base sono `openid` ,, `profile` `email` `User.Read` e `offline_access` , che sono tutte autorizzazioni delegate sul Microsoft Graph. Con queste autorizzazioni un'app può leggere i dettagli completi del profilo dell'utente che ha eseguito l'accesso e può gestire l'accesso anche quando l'utente non usa più l'app.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Seguire questa procedura per classificare le autorizzazioni usando il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Consenso e autorizzazioni** > **Permission classifications (Classificazioni autorizzazioni)** .
1. Scegliere **Aggiungi autorizzazioni** per classificare un'altra autorizzazione come a "Impatto basso".
1. Selezionare l'API e quindi selezionare le autorizzazioni delegate.

In questo esempio è stato classificato il set minimo di autorizzazioni necessarie per Single Sign-On:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Classificazioni delle autorizzazioni":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per classificare le autorizzazioni, è possibile usare il modulo Azure AD PowerShell Preview più recente, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true). Le classificazioni delle autorizzazioni vengono configurate nell'oggetto **ServicePrincipal** dell'API che pubblica le autorizzazioni.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Elencare le classificazioni delle autorizzazioni correnti per un'API

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

#### <a name="classify-a-permission-as-low-impact"></a>Classificazione di un'autorizzazione come "basso effetto"

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

#### <a name="remove-a-delegated-permission-classification"></a>Rimuovere una classificazione delle autorizzazioni delegate

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
