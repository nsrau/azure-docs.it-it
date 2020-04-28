---
title: Esempio di PowerShell-sostituire il certificato nelle app del proxy di applicazione
description: Esempio di PowerShell che sostituisce in blocco un certificato nelle applicazioni proxy di applicazione Azure Active Directory (Azure AD).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75481997"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Ottenere tutte le applicazioni proxy di applicazione pubblicate con il certificato identico e sostituirlo

Questo esempio di script di PowerShell consente di sostituire il certificato in blocco per tutte le applicazioni proxy di applicazione Azure Active Directory (Azure AD) pubblicate con lo stesso certificato.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Per questo esempio è necessario [AzureAD V2 PowerShell for Graph Module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o [AzureAD V2 PowerShell for Graph Module preview version](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

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

Per altre informazioni sul modulo Azure AD PowerShell, vedere [Cenni preliminari sui moduli Azure ad PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Per altri esempi di PowerShell per il proxy di applicazione, vedere [Azure ad esempi di PowerShell per Azure ad proxy di applicazione](../application-proxy-powershell-samples.md).