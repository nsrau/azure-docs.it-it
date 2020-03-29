---
title: Crittografia del servizio faccia le applicazioni
titleSuffix: Azure Cognitive Services
description: Faccia servizio di crittografia dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372216"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Crittografia del servizio faccia le applicazioni

Il servizio Face crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia del servizio Face protegge i tuoi dati e ti aiuta a rispettare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili solo nel piano tariffario E0. Per richiedere la possibilità di utilizzare chiavi gestite dal cliente, compilare e inviare il modulo di [richiesta della chiave gestita dal cliente Face Service](https://aka.ms/cogsvc-cmk). Ci vorranno circa 3-5 giorni lavorativi per riascoltare lo stato della tua richiesta. A seconda della richiesta, è possibile essere inseriti in una coda e approvati non appena lo spazio diventa disponibile. Una volta approvato per l'utilizzo di CMK con il servizio Face, è necessario creare una nuova risorsa Face e selezionare E0 come livello di prezzo. Dopo aver creato la risorsa Face con il piano tariffario E0, è possibile usare l'insieme di credenziali delle chiavi di Azure per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta chiave gestito dal cliente Face Service](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key VaultLearn more about Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


