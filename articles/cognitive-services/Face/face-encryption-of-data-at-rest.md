---
title: Crittografia del servizio viso dei dati inattivi
titleSuffix: Azure Cognitive Services
description: La crittografia dei dati inattivi per il servizio Face.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309034"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Crittografia del servizio viso dei dati inattivi

Il servizio viso crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia del servizio viso protegge i dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili solo nel piano tariffario di E0. Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [modulo di richiesta della chiave gestita dal cliente del servizio viso](https://aka.ms/cogsvc-cmk). Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvate per l'uso di CMK con il servizio face, sarà necessario creare una nuova risorsa viso e selezionare E0 come piano tariffario. Una volta creata la risorsa face con il piano tariffario E0, è possibile usare Azure Key Vault per configurare l'identità gestita.

## <a name="regional-availability"></a>Disponibilità a livello di area

Le chiavi gestite dal cliente sono attualmente disponibili nelle aree seguenti:

* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2
* Stati Uniti orientali
* US Gov Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco completo dei servizi che supportano CMK, vedere [chiavi gestite dal cliente per servizi cognitivi](../encryption/cognitive-services-encryption-keys-portal.md)
* [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Modulo di richiesta chiave gestita dal cliente di servizi cognitivi](https://aka.ms/cogsvc-cmk)
