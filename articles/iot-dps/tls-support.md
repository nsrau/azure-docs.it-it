---
title: Supporto TLS per il servizio Device provisioning (DPS) di Azure
description: Procedure consigliate per l'uso di connessioni TLS sicure per dispositivi e servizi che comunicano con il servizio Device provisioning (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984850"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Supporto di TLS nel servizio Device provisioning in hub Azure

DPS USA Transport Layer Security (TLS) per proteggere le connessioni dai dispositivi Internet delle cose. Sono attualmente supportate tre versioni del protocollo TLS, ovvero le versioni 1,0, 1,1 e 1,2.

TLS 1,0 e 1,1 sono considerati legacy e sono pianificati per la deprecazione. Per altre informazioni, vedere [deprecazione di TLS 1,0 e 1,1 per l'hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)Internet. Si consiglia di usare TLS 1,2 come versione preferita di TLS quando ci si connette a DPS.

## <a name="restrict-connections-to-tls-12"></a>Limitare le connessioni a TLS 1,2

Per una maggiore sicurezza, è consigliabile configurare le istanze di DPS in modo da consentire *solo* le connessioni client del dispositivo che usano TLS versione 1,2 e per applicare l'uso di [crittografie consigliate](#recommended-ciphers).

A tale scopo, effettuare il provisioning di una nuova risorsa DPS in una delle [aree supportate](#supported-regions) e impostare la `minTlsVersion` proprietà su `1.2` nella specifica della risorsa DPS del modello di Azure Resource Manager. Il modello JSON di esempio seguente specifica `minTlsVersion` la proprietà per una nuova istanza di DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

È possibile distribuire il modello con il seguente comando dell'interfaccia della riga di comando di Azure. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Per altre informazioni sulla creazione di risorse DPS con Gestione risorse modelli, vedere [configurare DPS con un modello di Azure Resource Manager](quick-setup-auto-provision-rm.md).

La risorsa DPS creata con questa configurazione rifiuterà i dispositivi che tentano di connettersi usando le versioni di TLS 1,0 e 1,1. Analogamente, l'handshake TLS verrà rifiutato se il messaggio HELLo del client del dispositivo non elenca alcuna [crittografia consigliata](#recommended-ciphers).

> [!NOTE]
> La `minTlsVersion` proprietà è di sola lettura e non può essere modificata dopo la creazione della risorsa DPS. È pertanto essenziale verificare e convalidare in modo corretto che *tutti* i dispositivi Internet delle cose siano compatibili con TLS 1,2 e le [crittografie consigliate](#recommended-ciphers) in anticipo.

## <a name="supported-regions"></a>Aree supportate

È possibile creare istanze DPS che richiedono l'uso di TLS 1,2 nelle aree seguenti:

* US Gov Arizona
* US Gov Virginia

> [!NOTE]
> Al momento del failover, `minTlsVersion` la proprietà del DPS rimarrà valida nell'area geografica abbinata dopo il failover.

## <a name="recommended-ciphers"></a>Crittografie consigliate

Le istanze di DPS configurate per accettare solo TLS 1,2 applicheranno anche l'uso delle seguenti crittografie consigliate:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>Usare TLS 1,2 negli SDK di Internet delle cose

Usare i collegamenti seguenti per configurare TLS 1,2 e le crittografie consentite negli SDK del client Azure.

| Linguaggio | Versioni che supportano TLS 1,2 | Documentazione |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 o versione successiva            | [Collegamento](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versione 2.0.0 o successiva             | [Collegamento](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versione 1.21.4 o successiva            | [Collegamento](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versione 1.19.0 o successiva            | [Collegamento](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versione 1.12.2 o successiva            | [Collegamento](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-with-iot-edge"></a>Usare TLS 1,2 con IoT Edge

I dispositivi IoT Edge possono essere configurati per l'uso di TLS 1,2 durante la comunicazione con l'hub e il DPS. A tale scopo, utilizzare la [pagina della documentazione IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).