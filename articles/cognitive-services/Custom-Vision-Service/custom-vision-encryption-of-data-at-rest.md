---
title: Visione personalizzata la crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Visione personalizzata la crittografia dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202271"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Visione personalizzata la crittografia dei dati inattivi

Azure Visione personalizzata crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. Visione personalizzata crittografia consente di proteggere i dati e di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono solo risorse disponibili create dopo l'11 maggio 2020. Per usare CMK con Visione personalizzata, sarà necessario creare una nuova risorsa Visione personalizzata. Dopo aver creato la risorsa, è possibile usare Azure Key Vault per configurare l'identità gestita.

### <a name="regional-availability"></a>Disponibilità a livello di area

Le chiavi gestite dal cliente sono attualmente disponibili nelle aree seguenti:

* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2
* Stati Uniti orientali
* US Gov Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Visione personalizzata modulo di richiesta della chiave gestita dal cliente](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


