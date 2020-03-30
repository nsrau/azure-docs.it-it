---
title: Esempio di PowerShell - Elenca tutti i gruppi di connettori proxy di applicazione
description: Esempio di PowerShell in cui sono elencati tutti i connettori e i connettori del proxy di applicazione di Azure Active Directory (Azure AD) nella directory.
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
ms.openlocfilehash: 53af7f3f9049edc23ed4226f43e7fe67398c29f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482114"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Ottenere tutti i gruppi di connettori e i connettori nella directoryGet all connector groups and connectors in the directory

Questo esempio di script di PowerShell elenca tutti i gruppi di connettori e i connettori del proxy di applicazione di Azure Active Directory (Azure AD) nella directory.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Questo esempio richiede il [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o la versione di anteprima del modulo [AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Recupera un elenco di tutti i gruppi di connettori o, se specificato, i dettagli del gruppo di connettori specificato. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Ottiene tutti i connettori proxy di applicazione associati a ogni gruppo di connettori.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere Panoramica del modulo Azure AD PowerShell.For more information on the Azure AD PowerShell module, see [Azure AD PowerShell module overview.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Per altri esempi di PowerShell per il proxy di applicazione, vedere Esempi di [Azure AD PowerShell per](../application-proxy-powershell-samples.md)il proxy di applicazione di Azure AD.