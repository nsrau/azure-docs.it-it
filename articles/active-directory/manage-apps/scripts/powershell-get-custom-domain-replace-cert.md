---
title: Esempio di PowerShell - Sostituire il certificato nelle app proxy di applicazione
description: Esempio di PowerShell che sostituisce in blocco un certificato tra le applicazioni proxy di applicazione di Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 91aafe00856f37e9d7e6b3babafecd2cbdf5f856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75481997"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Pubblicare tutte le applicazioni proxy di applicazione con lo stesso certificato e sostituirlo

Questo esempio di script di PowerShell consente di sostituire il certificato in blocco per tutte le applicazioni proxy di applicazione di Azure Active Directory (Azure AD) pubblicate con lo stesso certificato.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Questo esempio richiede il [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o la versione di anteprima del modulo [AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Ottiene un'entità servizio. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Ottiene un'applicazione Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera un'applicazione configurata per il proxy di applicazione in Azure AD. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate?view=azureadps-2.0) | Assegna un certificato a un'applicazione configurata per il proxy di applicazione in Azure AD. Questo comando carica il certificato e consente all'applicazione di utilizzare domini personalizzati. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere Panoramica del modulo Azure AD PowerShell.For more information on the Azure AD PowerShell module, see [Azure AD PowerShell module overview.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Per altri esempi di PowerShell per il proxy di applicazione, vedere Esempi di [Azure AD PowerShell per](../application-proxy-powershell-samples.md)il proxy di applicazione di Azure AD.