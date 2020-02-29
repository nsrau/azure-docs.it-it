---
title: Deprecazione di TLS 1,0 e 1,1 nell'hub Internet e nel servizio Device provisioning (DPS) | Microsoft Docs
description: Linee guida relative alla deprecazione di TLS 1,0 e 1,1 e alle crittografie supportate nell'hub e nei DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912155"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Deprecazione di TLS 1,0 e 1,1 nell'hub e nel servizio Device provisioning

Per fornire la crittografia migliore, l'hub Internet e il servizio Device provisioning (DPS) passano a Transport Layer Security (TLS) 1,2 come meccanismo di crittografia scelto per i dispositivi e i servizi Internet. Di conseguenza, il supporto legacy per TLS 1,0 e TLS 1,1, oltre a diverse crittografie legacy non consigliate, sarà deprecato nel **1 ° luglio 2020**.


## <a name="impact"></a>Impatto
In base alle circostanze e alle configurazioni specifiche dei clienti, la deprecazione di TLS 1,0 e 1,1 e le crittografie legacy non consigliate possono costituire un cambiamento significativo per i dispositivi e i servizi Internet che comunicano con l'hub o il DPS. In alcuni casi, i dispositivi e i servizi che non sono compatibili con queste modifiche non saranno in grado di connettersi all'hub Internet delle cose o al DPS dopo la data di fine.


## <a name="supported-ciphers"></a>Crittografie supportate

La sequenza temporale per la disponibilità di varie crittografie usate nell'handshake TLS è la seguente:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (attualmente supportato)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (sarà supportato nella seconda metà del 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (sarà supportato nella seconda metà del 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (sarà supportato nella seconda metà del 2020)


## <a name="customer-feedback"></a>Commenti e suggerimenti degli utenti

Sebbene l'imposizione di TLS 1,2 sia una scelta ottimale per la crittografia a livello di settore e verrà abilitata in base a quanto previsto, è comunque opportuno che i clienti riscontrino le loro specifiche distribuzioni e difficoltà ad adottare TLS 1,2. A questo scopo, è possibile inviare i commenti a [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
