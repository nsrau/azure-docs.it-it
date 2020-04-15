---
title: Deprecazione di TLS 1.0 e 1.1 nell'hub IoT Documenti Microsoft
description: Linee guida per la deprecazione di TLS 1.0 e 1.1 e le crittografie supportate nell'hub IoT.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381306"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Deprecazione di TLS 1.0 e 1.1 nell'hub IoT

Per fornire la crittografia best-in-class, l'hub IoT si sta spostando a Transport Layer Security (TLS) 1.2 come meccanismo di crittografia preferito per i dispositivi e i servizi IoT. 

## <a name="timeline"></a>Sequenza temporale

L'hub IoT continuerà a supportare TLS 1.0/1.1 fino a nuovo avviso. Tuttavia, è consigliabile che tutti i clienti migrano a TLS 1.2 il prima possibile.

## <a name="supported-ciphers"></a>Cifrari supportati

La sequenza temporale per la disponibilità di varie crittografie utilizzate nell'handshake TLS è la seguente:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (attualmente supportato)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (saranno sostenuti nella seconda metà del 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (saranno sostenuti nella seconda metà del 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (saranno sostenuti nella seconda metà del 2020)

## <a name="customer-feedback"></a>Commenti e suggerimenti degli utenti

Mentre l'applicazione TLS 1.2 è una scelta di crittografia migliore a livello di settore e sarà abilitata come previsto, vorremmo ancora sentire dai clienti per quanto riguarda le loro distribuzioni specifiche e le difficoltà di adozione di TLS 1.2. A tale scopo, è possibile [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)inviare i commenti a .
