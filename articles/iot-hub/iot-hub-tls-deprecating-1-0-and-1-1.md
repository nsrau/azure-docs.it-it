---
title: Deprecazione di TLS 1.0 e 1.1 nell'hub IoT e nel servizio di provisioning dei dispositivi (DPS) Documenti Microsoft
description: Linee guida per la deprecazione di TLS 1.0 e 1.1 e le crittografie supportate nell'hub IoT e DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402799"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Deprecazione di TLS 1.0 e 1.1 nel servizio IoT Hub and Device Provisioning

Per fornire la crittografia best-in-class, L'Hub IoT e il servizio di provisioning dei dispositivi (DPS) passano a Transport Layer Security (TLS) 1.2 come meccanismo di crittografia preferito per i dispositivi e i servizi IoT. 

## <a name="supported-ciphers"></a>Cifrari supportati

La sequenza temporale per la disponibilità di varie crittografie utilizzate nell'handshake TLS è la seguente:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (attualmente supportato)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (saranno sostenuti nella seconda metà del 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (saranno sostenuti nella seconda metà del 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (saranno sostenuti nella seconda metà del 2020)


## <a name="customer-feedback"></a>Commenti e suggerimenti degli utenti

Mentre l'applicazione TLS 1.2 è una scelta di crittografia migliore a livello di settore e sarà abilitata come previsto, vorremmo ancora sentire dai clienti per quanto riguarda le loro distribuzioni specifiche e le difficoltà di adozione di TLS 1.2. A tale scopo, è possibile [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)inviare i commenti a .
