---
title: Esempio di PowerShell- Elenca le informazioni di base per le app del proxy di applicazione
description: Esempio di PowerShell in cui sono elencate le applicazioni proxy di applicazione di Azure Active Directory (Azure AD) con l'ID applicazione (AppId), il nome (DisplayName) e l'ID oggetto (ObjId).
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
ms.openlocfilehash: 009aabb8b742a752a29d775efbd11b153588c09b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482218"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Ottenere tutte le app del proxy di applicazione ed elencare le informazioni di baseGet all Application Proxy apps and list basic information

Questo esempio di script di PowerShell elenca informazioni su tutte le applicazioni proxy di applicazione di Azure Active Directory (Azure AD), inclusi l'ID applicazione (AppId), il nome (DisplayName) e l'ID oggetto (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Questo esempio richiede il [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o la versione di anteprima del modulo [AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).
## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Ottiene un'entità servizio. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere Panoramica del modulo Azure AD PowerShell.For more information on the Azure AD PowerShell module, see [Azure AD PowerShell module overview.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Per altri esempi di PowerShell per il proxy di applicazione, vedere Esempi di [Azure AD PowerShell per](../application-proxy-powershell-samples.md)il proxy di applicazione di Azure AD.