---
title: Deprecazione di TLS 1,0 e 1,1 nell'hub Internet Microsoft Docs
description: Linee guida relative alla deprecazione di TLS 1,0 e 1,1 e alle crittografie supportate nell'hub Internet.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381306"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Deprecazione di TLS 1,0 e 1,1 nell'hub Internet

Per fornire la crittografia migliore, l'hub Internet sta migrando a Transport Layer Security (TLS) 1,2 come meccanismo di crittografia scelto per i dispositivi e i servizi Internet. 

## <a name="timeline"></a>Sequenza temporale

L'hub Internet delle cose continuerà a supportare TLS 1.0/1.1 fino a quando non sarà più disponibile. Tuttavia, è consigliabile eseguire la migrazione di tutti i clienti a TLS 1,2 il prima possibile.

## <a name="supported-ciphers"></a>Crittografie supportate

La sequenza temporale per la disponibilità di varie crittografie usate nell'handshake TLS è la seguente:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (attualmente supportato)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (sarà supportato nella seconda metà del 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (sarà supportato nella seconda metà del 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (sarà supportato nella seconda metà del 2020)

## <a name="customer-feedback"></a>Feedback dei clienti

Sebbene l'imposizione di TLS 1,2 sia una scelta ottimale per la crittografia a livello di settore e verrà abilitata in base a quanto previsto, è comunque opportuno che i clienti riscontrino le loro specifiche distribuzioni e difficoltà ad adottare TLS 1,2. A questo scopo, è possibile inviare i commenti a [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
