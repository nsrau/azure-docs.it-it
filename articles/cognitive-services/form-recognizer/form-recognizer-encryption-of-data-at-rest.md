---
title: Crittografia del servizio di riconoscimento form dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Microsoft offre chiavi di crittografia gestite da Microsoft e consente inoltre di gestire le sottoscrizioni di servizi cognitivi con chiavi personalizzate, denominate chiavi gestite dal cliente (CMK). Questo articolo illustra la crittografia dei dati inattivi per il riconoscimento del modulo e come abilitare e gestire CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: c959231826125349ecd6a62afe529248f7ac2eec
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326797"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Crittografia dei dati inattivi per il riconoscimento moduli

Il riconoscitore di form di Azure crittografa automaticamente i dati in modo permanente nel cloud. La crittografia del sistema di riconoscimento dei moduli consente di proteggere i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono solo risorse disponibili create dopo l'11 maggio 2020. Per usare CMK con il riconoscimento del modulo, è necessario creare una nuova risorsa di riconoscimento del modulo. Dopo aver creato la risorsa, è possibile usare Azure Key Vault per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta della chiave gestita dal cliente per il riconoscimento del modulo](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)