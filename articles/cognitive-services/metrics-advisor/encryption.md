---
title: Crittografia del servizio di Advisor di metrica
titleSuffix: Azure Cognitive Services
description: Crittografia del servizio di Advisor delle metriche dei dati inattivi.
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 5d41500a9c53e38cd36f0feba602e0e1baa5da2c
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909744"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Crittografia del servizio di Advisor delle metriche dei dati inattivi

Il servizio metrica Advisor crittografa automaticamente i dati in caso di salvataggio permanente nel cloud. La crittografia del servizio metrica Advisor protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili solo nel piano tariffario di E0. Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [servizio Advisor di metrica Customer-Managed modulo della richiesta di chiave](https://aka.ms/cogsvc-cmk). Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvate per l'uso di CMK con il servizio metrica Advisor, sarà necessario creare una nuova risorsa Advisor di metrica e selezionare E0 come piano tariffario. Una volta creata la risorsa Advisor per le metriche con il piano tariffario E0, è possibile usare Azure Key Vault per configurare l'identità gestita.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta della chiave Customer-Managed servizio di Advisor metrica](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key Vault](../../key-vault/general/overview.md)