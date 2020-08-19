---
title: Esempio di PowerShell - Assegnare un utente a un'app di Application Proxy
description: Esempio di PowerShell che assegna un utente a un'applicazione di Azure Active Directory (Azure AD) Application Proxy.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: addc3f091a2699079b0463b0d6e4d0cf097f3573
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507077"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Assegnare un utente a un'applicazione specifica di Azure AD Application Proxy

Questo script di esempio di PowerShell consente di assegnare un utente a un'applicazione specifica di Azure AD Application Proxy.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
| [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | Assegna un utente a un ruolo applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Per altri esempi di PowerShell relativi ad Application Proxy, vedere [Esempi di Azure AD PowerShell per Azure AD Application Proxy](../application-proxy-powershell-samples.md).