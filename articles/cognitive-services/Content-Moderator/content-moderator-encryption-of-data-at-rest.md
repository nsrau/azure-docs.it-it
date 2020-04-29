---
title: Content Moderator la crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Content Moderator la crittografia dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372161"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator la crittografia dei dati inattivi

Content Moderator crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud, contribuendo a soddisfare gli obiettivi di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili solo nel piano tariffario di E0. Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [modulo di richiesta della chiave gestita dal cliente content moderator](https://aka.ms/cogsvc-cmk). Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvate per l'uso di CMK con il servizio Content Moderator, sarà necessario creare una nuova risorsa Content Moderator e selezionare E0 come piano tariffario. Una volta creata la risorsa di Content Moderator con il piano tariffario E0, è possibile usare Azure Key Vault per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Abilitare la crittografia dei dati per il team di Content Moderator

Per abilitare la crittografia dei dati per il team di Content Moderator Review, vedere la [Guida introduttiva: provare content moderator sul Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> È necessario specificare un _ID risorsa_ con il piano tariffario content moderator E0.


## <a name="next-steps"></a>Passaggi successivi

* [Content Moderator modulo di richiesta della chiave gestita dal cliente](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
