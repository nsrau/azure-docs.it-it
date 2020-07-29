---
title: Esempio di PowerShell-spostare le app del proxy di applicazione in un altro gruppo
description: Azure Active Directory (Azure AD) proxy di applicazione PowerShell esempio usato per spostare tutte le applicazioni attualmente assegnate a un gruppo di connettori a un gruppo di connettori diverso.
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
ms.openlocfilehash: f6dccdaa96dadb061b168bbdf6148ed4d759776c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75481984"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Spostare tutte le app assegnate a un gruppo di connettori in un altro gruppo di connettori

Questo esempio di script di PowerShell sposta tutte le applicazioni proxy di applicazione Azure Active Directory (Azure AD) attualmente assegnate a un gruppo di connettori a un gruppo di connettori diverso.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Per questo esempio è necessario [AzureAD V2 PowerShell for Graph Module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o [AzureAD V2 PowerShell for Graph Module preview version](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Ottiene un'entità servizio. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Ottiene un'applicazione Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Recupera un elenco di tutti i gruppi di connettori oppure, se specificati, i dettagli del gruppo di connettori specificato. |
| [Set-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | Assegna il gruppo di connettori specificato a un'applicazione specificata.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere [Cenni preliminari sui moduli Azure ad PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Per altri esempi di PowerShell per il proxy di applicazione, vedere [Azure ad esempi di PowerShell per Azure ad proxy di applicazione](../application-proxy-powershell-samples.md).