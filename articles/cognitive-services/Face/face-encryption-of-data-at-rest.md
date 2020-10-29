---
title: Crittografia del servizio viso dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Microsoft offre chiavi di crittografia gestite da Microsoft e consente inoltre di gestire le sottoscrizioni di servizi cognitivi con chiavi personalizzate, denominate chiavi gestite dal cliente (CMK). Questo articolo illustra la crittografia dei dati inattivi per il viso e come abilitare e gestire CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 8c6f4f7db312355b719deb434bf6a46fa55eec9d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912787"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Crittografia del servizio viso dei dati inattivi

Il servizio viso crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia del servizio viso protegge i dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili solo nel piano tariffario di E0. Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [servizio Face Customer-Managed modulo della richiesta di chiave](https://aka.ms/cogsvc-cmk). Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvate per l'uso di CMK con il servizio face, sarà necessario creare una nuova risorsa viso e selezionare E0 come piano tariffario. Una volta creata la risorsa face con il piano tariffario E0, è possibile usare Azure Key Vault per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco completo dei servizi che supportano CMK, vedere [chiavi gestite dal cliente per servizi cognitivi](../encryption/cognitive-services-encryption-keys-portal.md)
* [Che cos'è Azure Key Vault](../../key-vault/general/overview.md)?
* [Modulo di richiesta chiave Customer-Managed di servizi cognitivi](https://aka.ms/cogsvc-cmk)