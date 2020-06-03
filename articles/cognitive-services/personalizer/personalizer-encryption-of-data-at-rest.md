---
title: Crittografia del servizio di personalizzazione dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Crittografia dei dati inattivi del servizio di personalizzazione.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 10eb627a340b45c93b2cfb2973e294d8d5d7c7e5
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307818"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Crittografia del servizio di personalizzazione dei dati inattivi

Il servizio di personalizzazione esegue automaticamente la crittografia dei dati quando vengono salvati in modo permanente nel cloud. La crittografia del servizio di personalizzazione protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili solo nel piano tariffario di E0. Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [modulo di richiesta della chiave gestita dal cliente del servizio di personalizzazione](https://aka.ms/cogsvc-cmk). Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvate per l'uso di CMK con il servizio di personalizzazione, sarà necessario creare una nuova risorsa di personalizzazione, quindi selezionare E0 come piano tariffario. Una volta creata la risorsa di personalizzazione con il piano tariffario E0, è possibile usare Azure Key Vault per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta della chiave gestita del cliente del servizio di personalizzazione](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
