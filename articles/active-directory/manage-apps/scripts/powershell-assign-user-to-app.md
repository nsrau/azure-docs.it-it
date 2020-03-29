---
title: Esempio di PowerShell - Assegnare un utente a un'app proxy di applicazione
description: Esempio di PowerShell che assegna un utente a un'applicazione proxy di applicazione di Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 42667ebdfc37b679d56421e3a82d4a6aaaed8fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483336"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Assegnare un utente a un'applicazione proxy di applicazione di Azure AD specificaAssign a user to a specific Azure AD Application Proxy application application

Questo esempio di script di PowerShell consente di assegnare un utente a un'applicazione proxy di applicazione di Azure AD specifica.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Questo esempio richiede il [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o la versione di anteprima del modulo [AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
| [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | Assegna un utente a un ruolo applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere Panoramica del modulo Azure AD PowerShell.For more information on the Azure AD PowerShell module, see [Azure AD PowerShell module overview.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Per altri esempi di PowerShell per il proxy di applicazione, vedere Esempi di [Azure AD PowerShell per](../application-proxy-powershell-samples.md)il proxy di applicazione di Azure AD.