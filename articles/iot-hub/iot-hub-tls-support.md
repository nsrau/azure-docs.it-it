---
title: Supporto TLS per l'hub IoT di Azure
description: Procedure consigliate per l'uso di connessioni TLS sicure per i dispositivi e i servizi che comunicano con l'hub IoT
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: jlian
ms.openlocfilehash: 8c52037684215d1672ed813389d0bbace9a03e42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080622"
---
# <a name="tls-support-in-iot-hub"></a>Supporto TLS nell'hub IoT

L'hub IoT usa il protocollo Transport Layer Security (TLS) per la protezione delle connessioni dai dispositivi e dai servizi IoT. Attualmente sono supportate tre versioni del protocollo TLS, ovvero le versioni 1.0, 1.1 e 1.2.

Le versioni 1.0 e 1.1 di TLS sono considerate legacy e ne è prevista la deprecazione. Per altre informazioni, vedere [Deprecazione di TLS 1.0 e 1.1 nell'hub IoT](iot-hub-tls-deprecating-1-0-and-1-1.md). È consigliabile usare TLS 1.2 come versione preferita di TLS durante la connessione all'hub IoT.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Imposizione di TLS 1,2 disponibile in aree selezionate

Per una maggiore sicurezza, configurare gli hub Internet delle cose in modo da consentire *solo* le connessioni client che usano TLS versione 1,2 e per applicare l'uso di [crittografie consigliate](#recommended-ciphers). Questa funzionalità è supportata solo nelle aree geografiche seguenti:

* Stati Uniti orientali
* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2
* US Gov Arizona
* US Gov Virginia

A questo scopo, eseguire il provisioning di un nuovo hub IoT in una delle aree supportate e impostare la proprietà `minTlsVersion` su `1.2` nella specifica della risorsa dell'hub IoT del modello di Azure Resource Manager:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

La risorsa dell'hub IoT creata con questa configurazione rifiuterà i client dei dispositivi e dei servizi che tentano di connettersi usando le versioni 1.0 e 1.1 di TLS. Analogamente, l'handshake TLS verrà rifiutato se nel messaggio HELLO del client non è indicata alcuna delle [crittografie consigliate](#recommended-ciphers).

> [!NOTE]
> La proprietà `minTlsVersion` è di sola lettura e non è possibile modificarla in seguito alla creazione della risorsa dell'hub IoT. È pertanto essenziale testare e convalidare anticipatamente e in modo adeguato la compatibilità di *tutti* i dispositivi e i servizi IoT con TLS 1.2 e con le [crittografie consigliate](#recommended-ciphers).
> 
> Al momento del failover, la proprietà `minTlsVersion` dell'hub IoT rimarrà valida nell'area abbinata in seguito al failover.

## <a name="recommended-ciphers"></a>Crittografie consigliate

Gli hub IoT configurati per accettare solo TLS 1.2 imporranno anche l'uso delle crittografie consigliate seguenti:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Per gli hub IoT non configurati per l'applicazione di TLS 1.2, quest'ultimo funziona comunque con le crittografie seguenti:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Usare TLS 1.2 negli SDK dell'hub IoT

Usare i collegamenti seguenti per configurare TLS 1.2 e le crittografie consentite negli SDK del client dell'hub IoT.

| Linguaggio | Versioni che supportano TLS 1.2 | Documentazione |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 o versioni successive            | [Collegamento](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versione 2.0.0 o successive             | [Collegamento](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versione 1.21.4 o successive            | [Collegamento](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versione 1.19.0 o successive            | [Collegamento](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versione 1.12.2 o successive            | [Collegamento](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Usare TLS 1.2 nella configurazione di IoT Edge

È possibile configurare l'uso di TLS 1.2 da parte dei dispositivi IoT Edge durante la comunicazione con l'hub IoT. A tale scopo, vedere la [pagina della documentazione di IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).