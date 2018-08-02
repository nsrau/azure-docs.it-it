---
title: Come modificare le impostazioni predefinite per la durata del token per un'applicazione personalizzata | Microsoft Docs
description: Informazioni su come aggiornare i criteri di durata del token per l'applicazione che si sta sviluppando in Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 0ecb1f55309901abd2c623d2c3d23ef717fb176b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365103"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Come modificare le impostazioni predefinite per la durata del token per un'applicazione personalizzata

Azure AD Premium consente agli sviluppatori di app e agli amministratori di tenant di configurare la durata dei token emessi per i client non riservati. I criteri di durata dei token vengono impostati a livello di tenant o per le risorse a cui si accede.

 * Per impostare i criteri di durata del token, è necessario scaricare il [modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Eseguire il comando **Connect-AzureAD -Confirm**.

 * Un esempio di criteri che impostano il token di aggiornamento di un singolo fattore di età massima è riportato di seguito. Creare i criteri: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Vedere il documento [Configurazione della durata del token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) per scoprire come crearne altri personalizzati.

## <a name="next-steps"></a>Passaggi successivi
[Configurazione della durata del token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Riferimento al token Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

