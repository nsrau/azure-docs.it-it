---
title: Esempio di PowerShell-app proxy di applicazione con certificati identici
description: Esempio di PowerShell che elenca tutte le applicazioni proxy di applicazione Azure Active Directory (Azure AD) pubblicate con lo stesso certificato.
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
ms.openlocfilehash: ef654226d041a7602c03e1ce4670d2f8eea0c2e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482010"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>Ottenere tutte le app dell'applicazione proxy Azure AD pubblicate con il certificato identico

Questo esempio di script di PowerShell elenca tutte le applicazioni proxy di applicazione Azure Active Directory (Azure AD) pubblicate con lo stesso certificato.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Per questo esempio è necessario [AzureAD V2 PowerShell for Graph Module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o [AzureAD V2 PowerShell for Graph Module preview version](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Ottiene un'entità servizio. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Ottiene un'applicazione Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera un'applicazione configurata per il proxy di applicazione in Azure AD. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere [Cenni preliminari sui moduli Azure ad PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Per altri esempi di PowerShell per il proxy di applicazione, vedere [Azure ad esempi di PowerShell per Azure ad proxy di applicazione](../application-proxy-powershell-samples.md).