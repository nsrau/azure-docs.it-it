---
title: Crittografia del servizio di riconoscimento form dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Modulo di crittografia dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: cafe170c4f4485791bbd65471a43d1d5f9726775
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202249"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Crittografia dei dati inattivi per il riconoscimento moduli

Il riconoscitore di form di Azure crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia del sistema di riconoscimento dei moduli consente di proteggere i dati e di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono solo risorse disponibili create dopo l'11 maggio 2020. Per usare CMK con il riconoscimento del modulo, è necessario creare una nuova risorsa di riconoscimento del modulo. Dopo aver creato la risorsa, è possibile usare Azure Key Vault per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta della chiave gestita dal cliente per il riconoscimento del modulo](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


