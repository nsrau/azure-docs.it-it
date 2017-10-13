---
title: Come modificare le impostazioni predefinite per la durata del token per un'applicazione personalizzata | Microsoft Docs
description: Informazioni su come aggiornare i criteri di durata del token per l'applicazione che si sta sviluppando in Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: a28eacd820ed28a6470992ce86b060e886c00bcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Come modificare le impostazioni predefinite per la durata del token per un'applicazione personalizzata

Azure AD Premium consente agli sviluppatori di app e agli amministratori di tenant di configurare la durata dei token emessi per i client non riservati. I criteri di durata dei token vengono impostati a livello di tenant o per le risorse a cui si accede.

 * Per impostare i criteri di durata del token, è necessario scaricare il [modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Eseguire il comando **Connect-AzureAD -Confirm**.

 * Un esempio di criteri che impostano il token di aggiornamento di un singolo fattore di età massima è riportato di seguito. Creare i criteri: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Vedere il documento [Configurazione della durata del token](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes) per scoprire come crearne altri personalizzati.

## <a name="next-steps"></a>Passaggi successivi
[Configurazione della durata del token](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Riferimento al token Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)

