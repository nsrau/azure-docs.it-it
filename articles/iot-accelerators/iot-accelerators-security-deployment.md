---
title: Proteggere la distribuzione di IoT (Internet delle cose) | Documentazione Microsoft
description: Questo articolo illustra come proteggere la distribuzione di IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 9422a4ce316053b6560f1e945de7d8018e52c15b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659437"
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>Pacchetti di crittografia degli acceleratori di soluzioni IoT

Gli acceleratori di soluzioni IoT supportano i pacchetti di crittografia seguenti, nell'ordine indicato.

| Pacchetto di crittografia | Length |
| --- | --- |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (uguale a RSA a 7680 bit) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (uguale a RSA a 3072 bit) FS |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (uguale a RSA a 7680 bit) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (uguale a RSA a 3072 bit) FS |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Vedere anche 
È anche possibile esplorare alcune altre funzionalità degli acceleratori di soluzioni IoT:

* [Panoramica dell'acceleratore di soluzioni di manutenzione predittiva][lnk-predictive-overview]
* [Domande frequenti sugli acceleratori di soluzioni IoT][lnk-faq]

Informazioni sulla protezione dell'hub IoT sono disponibili in [Controllare l'accesso all'hub IoT][lnk-devguide-security] nella guida per gli sviluppatori dell'hub IoT.

[lnk-predictive-overview]:iot-accelerators-predictive-overview.md
[lnk-faq]:iot-accelerators-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
