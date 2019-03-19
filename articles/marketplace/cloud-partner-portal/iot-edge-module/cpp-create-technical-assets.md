---
title: Creare gli asset tecnici del modulo Azure IoT Edge | Microsoft Docs
description: Creare gli asset tecnici per un modulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ed4826eb47ab2fb13d312860475f9ec9b323bf7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884155"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Preparare gli asset tecnici del modulo IoT Edge

Questo articolo descrive i requisiti che devono soddisfare gli asset tecnici del modulo IoT Edge prima della pubblicazione in Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Informazioni sui moduli IoT Edge e introduzione

Un modulo IoT Edge è un contenitore compatibile con Docker che viene eseguito in un dispositivo IoT Edge.

- Per altre informazioni sui moduli Azure IoT Edge, vedere [Informazioni sui moduli Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Per iniziare a usare lo sviluppo dei moduli IoT Edge, vedere [requisiti e strumenti per sviluppare moduli IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Requisiti tecnici

Per la certificazione del modulo IoT Edge e la pubblicazione in Azure Marketplace, è necessario soddisfare i requisiti tecnici seguenti.

### <a name="platform-support"></a>Supporto delle piattaforme

Il modulo IoT Edge deve supportare una delle opzioni di piattaforma seguenti.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Piattaforme di livello 1 supportate da IoT Edge

Supporto per tutte le piattaforme di livello 1 supportate da IoT Edge (registrate nel [supporto di Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Questa è l'opzione consigliata, poiché offre un'esperienza utente di livello superiore. Vengono presentati i moduli che soddisfano questi criteri. Un modulo che usa questa opzione di piattaforma deve:

- Specificare un tag `latest` e un tag di versione (ad esempio, `1.0.1`), ovvero tag di manifesto creati con [manifest-tool](https://github.com/estesp/manifest-tool) di GitHub.
- Usare la [scheda Marketplace](./cpp-marketplace-tab.md) per aggiungere un collegamento ai [dispositivi con certificazione IoT Edge compatibili](https://aka.ms/iot-edge-certified). Questo collegamento viene risolto in `https://aka.ms/iot-edge-certified`, un sito Web in cui i clienti possono esplorare o cercare i dispositivi certificati. Questo sito Web è noto anche come catalogo dei dispositivi [con certificazione Azure IoT Edge](https://catalog.azureiotsolutions.com/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Subset di piattaforme di livello 1 supportate da IoT Edge
  
Supporto di almeno un subset di piattaforme di livello 1 supportate da IoT Edge (registrate nel [supporto di Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Un modulo che usa questa opzione di piattaforma deve:

- Specificare un tag `latest` e un tag di versione (ad esempio, `1.0.1`), ovvero tag di manifesto creati con [manifest-tool](https://github.com/estesp/manifest-tool) di GitHub se è supportata più di una piattaforma. I tag di manifesto sono facoltativi se è supportata una sola piattaforma.
- Usare la [scheda Marketplace](./cpp-marketplace-tab.md) per specificare un collegamento ad almeno un dispositivo IoT Edge nel catalogo dei dispositivi [con certificazione Azure IoT Edge](https://catalog.azureiotsolutions.com/).

### <a name="device-dimensions"></a>Dimensioni dei dispositivi

Le dimensioni del modulo IoT Edge (CPU/RAM/archiviazione/GPU ecc.) nei dispositivi IoT Edge di destinazione devono soddisfare i requisiti seguenti:

- Il modulo deve **essere compatibile con almeno un dispositivo con certificazione IoT Edge** nel catalogo dei dispositivi [con certificazione Azure IoT Edge](https://catalog.azureiotsolutions.com/).
- I **requisiti hardware minimi** devono essere documentati come paragrafo finale nella descrizione dell'offerta (nella [scheda Marketplace](./cpp-marketplace-tab.md)). È anche possibile elencare facoltativamente i requisiti hardware consigliati se differiscono in modo significativo. Aggiungere ad esempio la sezione seguente alla fine della descrizione dell'offerta:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Configurazione

Include anche le impostazioni di configurazione predefinite per rendere la distribuzione su un dispositivo IoT Edge quanto più semplice e immediata. Il contenitore può includere anche lo SDK di un modulo IoT Edge per consentire la comunicazione con l'edgeHub e hub IoT.

#### <a name="default-configuration"></a>Configurazione predefinita

I moduli IoT Edge devono iniziare con le impostazioni predefinite indicate nella [scheda SKU del portale Cloud Partner](./cpp-skus-tab.md). Sono disponibili le impostazioni predefinite seguenti:

- Default **routes** (Route predefinite)
- Default **twin desired properties** (Proprietà desiderate del dispositivo gemello predefinite)
- Default **environment variables** (Variabili di ambiente predefinite)
- Default **createOptions** (createOptions predefinito)

In uno scenario in cui un parametro obbligatorio per un valore predefinito non ha senso (ad esempio, l'indirizzo IP del server di un cliente), aggiungere un parametro come valore predefinito. Questo valore è racchiuso tra parentesi quadre e indicato in caratteri maiuscoli. Per questo esempio viene configurata la variabile di ambiente predefinita seguente:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentazione della configurazione

Tutte le impostazioni di configurazione di un modulo IoT Edge devono essere chiaramente documentate (come usare le route, proprietà desiderate del dispositivo gemello, variabili di ambiente, createOptions e così via). Specificare un collegamento alla documentazione o includerla nella descrizione dell'offerta o dello SKU.

### <a name="tags-and-versioning"></a>Tag e controllo delle versioni

I clienti devono essere in grado di distribuire facilmente un modulo e ottenere automaticamente aggiornamenti dal marketplace (in uno scenario di sviluppo). Devono anche poter usare e bloccare una versione esatta testata (in uno scenario di produzione.)

Per soddisfare queste aspettative del cliente ed essere pubblicati nel marketplace, i moduli IoT Edge devono soddisfare i requisiti seguenti:

- Includere un tag `latest` di manifesto, che punta alla versione più recente in tutte le piattaforme supportate.
- I tag di versione devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi.
- Includere un tag di versione, ad esempio `1.0.1`, che punta a una versione specifica in tutte le piattaforme supportate.
- Non aggiornare i tag di versione, ad esempio `1.0.1`, in quanto devono essere non modificabili.

>[!Note]
>Il controllo delle versioni può facoltativamente includere tag di versione di distribuzione, ad esempio `2.0` e `1.0`. Questa opzione supporta la gestione di più versioni principali in parallelo.

### <a name="telemetry"></a>Telemetria

I moduli che usano l'SDK dei moduli IoT devono impostare l'identificatore di modulo univoco su `PublisherId.OfferId.SkuId` ai fini della telemetria. Un identificatore univoco consente ad Azure Marketplace di identificare il numero di istanze di modulo in esecuzione.

 Usare i metodi seguenti degli SDK dei moduli IoT per impostare `ProductInfo` su questo identificatore:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Per i moduli che non usano l'SDK dei moduli IoT sono disponibili informazioni dettagliate meno precise nel portale Cloud Partner, ad esempio il numero di download.

### <a name="security"></a>Security

I moduli IoT Edge devono richiedere l'accesso con meno privilegi possibile all'host. È consigliabile evitare [moduli con privilegi](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities).

### <a name="module-iot-sdk"></a>SDK dei moduli IoT

L'inclusione dell'SDK dei moduli IoT non è un prerequisito per la certificazione, tuttavia può offrire un'esperienza utente migliore, ad esempio per il supporto del routing o l'invio di messaggi al cloud.

L'SDK dei moduli IoT è necessario per ottenere dati di telemetria sul numero di istanze di modulo in esecuzione.


## <a name="recertification-process"></a>Processo di ricertificazione

<!-- Add legal time windows-->
Ogni volta che viene apportata una modifica di rilievo che interessa i propri moduli, ad esempio, è possibile ricevere una notifica partner:

- Matrice di supporto dei sistemi operativi o dell'architettura di livello 1 supportata da IoT Edge
- SDK dei moduli IoT
- Runtime di IoT Edge
- Linee guida di certificazione del modulo IoT Edge

I partner devono aggiornare i moduli e ripetere la certificazione usando lo strumento del portale Cloud Partner.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Ospitare il modulo IoT Edge in un'istanza di Registro Azure Container

Per caricare il modulo IoT Edge nel portale Cloud Partner, è innanzitutto necessario ospitarlo in un'istanza di [Registro Azure Container](https://azure.microsoft.com/services/container-registry/). Il modulo deve includere tutti i tag che si intende pubblicare, compresi i tag di immagine referenziati da un tag di manifesto.


## <a name="next-steps"></a>Passaggi successivi

- [Create your IoT Edge module offer](./cpp-create-offer.md) (Creare un'offerta di modulo IoT Edge)
