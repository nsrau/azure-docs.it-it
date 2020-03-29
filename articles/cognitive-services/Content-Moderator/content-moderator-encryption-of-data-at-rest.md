---
title: Content Moderator crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Content Moderator crittografia dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372161"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator crittografia dei dati inattivi

Content Moderator crittografa automaticamente i dati quando vengono resi persistenti nel cloud, consentendo di soddisfare gli obiettivi di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili solo nel piano tariffario E0. Per richiedere la possibilità di utilizzare chiavi gestite dal cliente, compilare e inviare il modulo di richiesta della [chiave gestita dal cliente di Content Moderator](https://aka.ms/cogsvc-cmk). Ci vorranno circa 3-5 giorni lavorativi per riascoltare lo stato della tua richiesta. A seconda della richiesta, è possibile essere inseriti in una coda e approvati non appena lo spazio diventa disponibile. Una volta approvata l'utilizzo di CMK con il servizio Content Moderator, è necessario creare una nuova risorsa Content Moderator e selezionare E0 come livello di prezzo. Dopo aver creato la risorsa Content Moderator con il piano tariffario E0, è possibile usare l'insieme di credenziali delle chiavi di Azure per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Abilitare la crittografia dei dati per il team di Content Moderator

Per abilitare la crittografia dei dati per il team di revisione di Content Moderator, vedere la [Guida introduttiva: Provare Content Moderator sul Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Dovrai fornire un _ID risorsa_ con il piano tariffario Content Moderator E0.


## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta chiave gestita dal cliente di Content Moderator](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key VaultLearn more about Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
