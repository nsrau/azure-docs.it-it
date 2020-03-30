---
title: Supporto TLS dell'hub IoT di Azure
description: Procedure consigliate per l'utilizzo di connessioni TLS sicure per dispositivi e servizi che comunicano con l'hub IoTBest practices in using secure TLS connections for devices and services communicating with IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409498"
---
# <a name="tls-support-in-iot-hub"></a>Supporto TLS nell'hub IoT

L'Hub IoT utilizza TLS (Transport Layer Security) per proteggere le connessioni da dispositivi e servizi IoT. Sono attualmente supportate tre versioni del protocollo TLS, ovvero le versioni 1.0, 1.1 e 1.2.

TLS 1.0 e 1.1 sono considerati legacy e sono pianificati per la deprecazione. Per ulteriori informazioni, vedere [Deprecato TLS 1.0 e 1.1 per L'hub IoT](iot-hub-tls-deprecating-1-0-and-1-1.md). Si consiglia di utilizzare TLS 1.2 come versione TLS preferita per la connessione all'hub IoT.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Limitare le connessioni a TLS 1.2 nella risorsa dell'hub IoT

Per una maggiore sicurezza, si consiglia di configurare gli hub IoT in modo da consentire *solo* le connessioni client che utilizzano TLS versione 1.2 e di imporre l'utilizzo delle [crittografie consigliate.](#recommended-ciphers)

A tale scopo, eseguire il provisioning di un nuovo hub `minTlsVersion` IoT in una delle [aree supportate](#supported-regions) e impostare la proprietà su nella specifica delle risorse hub IoT del modello di Azure Resource Manager:For this purpose, provision a new IoT Hub in any of the supported regions and set the property to `1.2` in your Azure Resource Manager template's IoT hub resource specification:

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

La risorsa dell'hub IoT creata usando questa configurazione rifiuterà i client di dispositivi e servizi che tentano di connettersi usando TLS versioni 1.0 e 1.1.The created IoT Hub resource using this configuration will refuse device and service clients that attempt to connect using TLS versions 1.0 and 1.1. Analogamente, l'handshake TLS verrà rifiutato se il messaggio HELLO del client non elenca nessuna delle [crittografie consigliate.](#recommended-ciphers)

> [!NOTE]
> La `minTlsVersion` proprietà è di sola lettura e non può essere modificata dopo la creazione della risorsa dell'hub IoT.The property is read-only and cannot be changed once your IoT Hub resource is created. È quindi essenziale testare e convalidare correttamente che *tutti i* dispositivi e i servizi IoT siano compatibili con TLS 1.2 e i [cifrari consigliati](#recommended-ciphers) in anticipo.

### <a name="supported-regions"></a>Aree supportate

Gli hub IoT che richiedono l'uso di TLS 1.2 possono essere creati nelle seguenti aree:

* Stati Uniti orientali
* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2
* US Gov Arizona
* US Gov Virginia

> [!NOTE]
> Dopo i failover, la proprietà dell'hub `minTlsVersion` IoT rimarrà valida nell'area con associazione geografica dopo il failover.

### <a name="recommended-ciphers"></a>Cifrari consigliati

Gli hub IoT configurati per accettare solo TLS 1.2 imporranno anche l'uso delle seguenti crittografie consigliate:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Usare TLS 1.2 negli SDK dell'hub IoTUse TLS 1.2 in your IoT Hub SDKs

Usare i collegamenti seguenti per configurare TLS 1.2 e le crittografie consentite negli SDK del client Hub IoT.Use the links below to configure TLS 1.2 and allowed ciphers in IoT Hub client SDKs.

| Linguaggio | Versioni che supportano TLS 1.2 | Documentazione |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 o più recente            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versione 2.0.0 o successiva             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versione 1.21.4 o successiva            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versione 1.19.0 o successiva            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versione 1.12.2 o successiva            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Usare TLS 1.2 nella configurazione di IoT Edge

I dispositivi IoT Edge possono essere configurati per utilizzare TLS 1.2 durante la comunicazione con l'hub IoT. A tale scopo, utilizzare la pagina della [documentazione di IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).