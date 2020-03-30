---
title: Esempio di PowerShell- Elencare tutte le app proxy di applicazione con un criterioPowerShell sample - List all Application Proxy apps with a policy
description: Esempio di PowerShell in cui sono elencate tutte le applicazioni proxy di applicazione di Azure Active Directory (Azure AD) nella directory che dispongono di criteri token di durata.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2ad7b769dc890917e5364fe57582acdfe16acac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482127"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Ottenere tutte le app proxy di applicazione con criteri di durata dei tokenGet all Application Proxy apps with a token lifetime policy

Questo esempio di script di PowerShell elenca tutte le applicazioni proxy di applicazione di Azure Active Directory (Azure AD) nella directory che dispongono di criteri di durata del token e elencano i dettagli sui criteri.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Questo esempio richiede la versione di anteprima del [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Ottiene un'entità servizio. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Ottiene un'applicazione Azure AD. |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Ottiene un criterio in Azure AD. |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Ottiene i criteri di un'entità servizio in Azure AD. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere Panoramica del modulo Azure AD PowerShell.For more information on the Azure AD PowerShell module, see [Azure AD PowerShell module overview.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Per altri esempi di PowerShell per il proxy di applicazione, vedere Esempi di [Azure AD PowerShell per](../application-proxy-powershell-samples.md)il proxy di applicazione di Azure AD.