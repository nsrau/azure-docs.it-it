---
title: Come modificare le impostazioni predefinite per la durata del token per un'applicazione personalizzata | Microsoft Docs
description: Informazioni su come aggiornare i criteri di durata del token per l'applicazione che si sta sviluppando in Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3d11d282a2405d37614bfac41dd3f7ad49353d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545518"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Come modificare le impostazioni predefinite per la durata del token per un'applicazione personalizzata

Questo articolo illustra come usare Azure AD PowerShell per impostare un criterio di durata dei token. Azure AD Premium consente agli sviluppatori di app e agli amministratori di tenant di configurare la durata dei token emessi per i client non riservati. I criteri di durata dei token vengono impostati a livello di tenant o per le risorse a cui si accede.

1. Per impostare i criteri di durata del token, è necessario scaricare il [modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Eseguire il comando **Connect-AzureAD -Confirm**.

    Un esempio di criteri che impostano il token di aggiornamento di un singolo fattore di età massima è riportato di seguito. Creare i criteri: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Durate dei token configurabile in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) per informazioni su come configurare la durata dei token emessi da Azure AD, tra cui come impostare la durata dei token per tutte le app di un'organizzazione, per un'applicazione multi-tenant o per un'entità servizio specifica in un'organizzazione. 
* [Riferimento al token Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

