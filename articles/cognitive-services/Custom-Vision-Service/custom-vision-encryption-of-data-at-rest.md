---
title: Visione personalizzata la crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Microsoft offre chiavi di crittografia gestite da Microsoft e consente inoltre di gestire le sottoscrizioni di servizi cognitivi con chiavi personalizzate, denominate chiavi gestite dal cliente (CMK). Questo articolo illustra la crittografia dei dati inattivi per Visione personalizzata e come abilitare e gestire CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 6c65f28c040b15aaa2ec8f3425209351e4b60486
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616213"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Visione personalizzata la crittografia dei dati inattivi

Azure Visione personalizzata crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. Visione personalizzata crittografia consente di proteggere i dati e di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono solo risorse disponibili create dopo l'11 maggio 2020. Per usare CMK con Visione personalizzata, sarà necessario creare una nuova risorsa Visione personalizzata. Dopo aver creato la risorsa, è possibile usare Azure Key Vault per configurare l'identità gestita.

## <a name="regional-availability"></a>Disponibilità a livello di area

Le chiavi gestite dal cliente sono attualmente disponibili nelle aree seguenti:

* Stati Uniti centro-meridionali
* West US 2
* Stati Uniti orientali
* US Gov Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco completo dei servizi che supportano CMK, vedere [chiavi gestite dal cliente per servizi cognitivi](../encryption/cognitive-services-encryption-keys-portal.md)
* [Che cos'è Azure Key Vault](../../key-vault/general/overview.md)?
* [Modulo di richiesta chiave Customer-Managed di servizi cognitivi](https://aka.ms/cogsvc-cmk)