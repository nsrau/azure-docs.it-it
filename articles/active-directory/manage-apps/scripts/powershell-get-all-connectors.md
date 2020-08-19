---
title: Esempio di PowerShell - Elencare tutti i gruppi di connettori di Application Proxy
description: Esempio di PowerShell che elenca tutti i connettori e gruppi di connettori di Azure Active Directory (Azure AD) Application Proxy presenti nella directory.
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
ms.openlocfilehash: f7d665ddf7b1902275eebd74bcbf148426ac6ddd
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506873"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Ottenere tutti i gruppi di connettori e i connettori nella directory

Questo script di esempio di PowerShell elenca tutti i connettori e gruppi di connettori di Azure Active Directory (Azure AD) Application Proxy presenti nella directory.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Recupera un elenco di tutti i gruppi di connettori oppure, se specificati, i dettagli del gruppo di connettori indicato. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Recupera tutti i connettori di Application Proxy associati a ogni gruppo di connettori.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Per altri esempi di PowerShell relativi ad Application Proxy, vedere [Esempi di Azure AD PowerShell per Azure AD Application Proxy](../application-proxy-powershell-samples.md).