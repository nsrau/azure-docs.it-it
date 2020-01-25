---
title: Supporto TLS per hub Azure
description: Procedure consigliate per l'uso di connessioni TLS sicure per dispositivi e servizi che comunicano con l'hub Internet
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: ce8ef987adc0cec4fcd8acef4cc075d50c92d62a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722889"
---
# <a name="tls-support-in-iot-hub"></a>Supporto di TLS nell'hub Internet

Hub Internet usa Transport Layer Security (TLS) per proteggere le connessioni da dispositivi e servizi Internet. Sono attualmente supportate tre versioni del protocollo TLS, ovvero le versioni 1,0, 1,1 e 1,2.

TLS 1,0 e 1,1 sono considerati legacy e sono pianificati per la deprecazione. Per altre informazioni, vedere [deprecazione di TLS 1,0 e 1,1 per l'hub](iot-hub-tls-deprecating-1-0-and-1-1.md)Internet. Si consiglia di usare TLS 1,2 come versione preferita di TLS quando ci si connette all'hub Internet.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Limitare le connessioni a TLS 1,2 nella risorsa dell'hub Internet delle cose

Per una maggiore sicurezza, è consigliabile configurare gli hub di Internet delle cose in modo da consentire *solo* connessioni client che usano TLS versione 1,2 e per applicare l'uso di [crittografie consigliate](#recommended-ciphers).

A questo scopo, effettuare il provisioning di un nuovo hub Internet in una delle [aree supportate](#supported-regions) e impostare la proprietà `minTlsVersion` su `1.2` nella specifica della risorsa dell'hub Internet del modello di Azure Resource Manager:

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

La risorsa dell'hub Internet delle cose creata con questa configurazione rifiuterà ai client dei dispositivi e dei servizi che tentano di connettersi usando le versioni di TLS 1,0 e 1,1. Analogamente, l'handshake TLS verrà rifiutato se il messaggio HELLo del client non elenca alcuna [crittografia consigliata](#recommended-ciphers).

> [!NOTE]
> La proprietà `minTlsVersion` è di sola lettura e non può essere modificata dopo la creazione della risorsa hub Internet. È pertanto essenziale verificare e convalidare in modo corretto che *tutti* i dispositivi e i servizi Internet siano compatibili con TLS 1,2 e le [crittografie consigliate](#recommended-ciphers) in anticipo.

### <a name="supported-regions"></a>Aree supportate

Gli hub Internet delle cose che richiedono l'uso di TLS 1,2 possono essere creati nelle aree seguenti:

* Stati Uniti orientali
* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2

> [!NOTE]
> Al momento del failover, la proprietà `minTlsVersion` dell'hub Internet Rest rimarrà valida nell'area geografica abbinata dopo il failover.

### <a name="recommended-ciphers"></a>Crittografie consigliate

Gli hub Internet delle cose configurati per accettare solo TLS 1,2 applicheranno anche l'uso delle seguenti crittografie consigliate:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Usare TLS 1,2 negli SDK dell'hub Internet delle cose

Usare i collegamenti seguenti per configurare TLS 1,2 e le crittografie consentite negli SDK client dell'hub Internet.

| Lingua | TLS 1,2 supportato | Documentazione |
|----------|-------------------|---------------|
| C        | Sì               | [Collegamento](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Sì               | [Collegamento](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Sì               | [Collegamento](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Sì               | [Collegamento](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Sì               | [Collegamento](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Usare TLS 1,2 nell'installazione di IoT Edge

I dispositivi IoT Edge possono essere configurati per l'uso di TLS 1,2 durante la comunicazione con l'hub Internet. A tale scopo, utilizzare la [pagina della documentazione IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).