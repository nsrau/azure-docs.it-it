---
title: Deprecazione di TLS 1.0 e 1.1 nell’hub IoT | Microsoft Docs
description: Linee guida relative alla deprecazione di TLS 1.0 e 1.1 e alle crittografie supportate nell'hub IoT.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849519"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Deprecazione di TLS 1.0 e 1.1 nell'hub IoT

Per fornire una crittografia ottimale, l'hub IoT sta adottando Transport Layer Security (TLS) 1.2 come meccanismo di crittografia scelto per i dispositivi e i servizi IoT. 

## <a name="timeline"></a>Sequenza temporale

L'hub IoT continuerà a supportare TLS 1.0/1.1 fino a nuovo avviso. Tuttavia, si consiglia a tutti i clienti di eseguire la migrazione a TLS 1.2 il prima possibile.

## <a name="deprecating-tls-11-ciphers"></a>Deprecazione di crittografie TLS 1.1

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Deprecazione di crittografie TLS 1.0

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>Crittografie TLS 1.2

Vedere [Crittografie consigliate TLS 1.2 per l’hub IoT](iot-hub-tls-support.md#recommended-ciphers).
 
## <a name="customer-feedback"></a>Commenti e suggerimenti degli utenti

Sebbene l'imposizione di TLS 1.2 sia una scelta ottimale per la crittografia a livello di settore e verrà abilitata come previsto, invitiamo comunque i clienti a inviarci commenti sulle distribuzioni specifiche e sulle difficoltà di adozione di TLS 1.2. A tale scopo, è possibile inviare i commenti a [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
