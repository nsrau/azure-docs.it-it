---
title: Preparare le risorse tecniche del modulo IoT Edge - Azure MarketplacePrepare your IoT Edge module technical assets - Azure Marketplace
description: Informazioni sui requisiti tecnici e di configurazione che gli asset tecnici del modulo Edge di Internet of Things (IoT) devono soddisfare prima di poterli pubblicare in Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730706"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Preparare gli asset tecnici del modulo IoT Edge

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte dei moduli IoT Edge dal portale per i partner cloud al Centro per i partner. Fino alla migrazione delle offerte, seguire le istruzioni in Preparare le risorse tecniche del [modulo IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) per il portale Cloud Partner per gestire le offerte.

Questo articolo descrive i requisiti che gli asset tecnici del modulo Edge di Internet of Things (IoT) devono soddisfare prima di essere pubblicati in Azure Marketplace.

## <a name="get-started"></a>Introduzione

Un modulo IoT Edge è un contenitore compatibile con Docker che viene eseguito su un dispositivo IoT Edge.An IoT Edge module is a Docker-compatible container that runs on an IoT Edge device.

- Per altre informazioni sui moduli Azure IoT Edge, vedere [Informazioni sui moduli Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Per iniziare con lo sviluppo di moduli IoT Edge, consultate [Sviluppare i propri moduli IoT Edge.](https://docs.microsoft.com/azure/iot-edge/module-development)

## <a name="technical-requirements"></a>Requisiti tecnici

Il modulo IoT Edge deve soddisfare i requisiti tecnici seguenti per essere certificato e pubblicato in Azure Marketplace.

### <a name="platform-support"></a>Piattaforme supportate

Il modulo IoT Edge deve supportare una delle seguenti opzioni della piattaforma:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Piattaforme di livello 1 supportate da IoT Edge

Il modulo deve supportare tutte le piattaforme di livello 1 supportate da IoT Edge (come registrato nel supporto di [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Questa è l'opzione consigliata, poiché offre un'esperienza utente di livello superiore. Verranno presentati i moduli che soddisfano questi criteri. Un modulo che usa questa opzione di piattaforma deve:

- Fornire un tag più recente e un tag di versione (ad esempio, 1.0.1) che sono tag manifesto compilati con lo [strumento Manifesto GitHub](https://github.com/estesp/manifest-tool).

- Usare la scheda Dell'offerta nel [Centro per](https://partner.microsoft.com/dashboard/commercial-marketplace) i partner per aggiungere un collegamento nella sezione **Collegamenti utili** al catalogo dei dispositivi certificati [Azure IoT Edge.](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Subset di piattaforme di livello 1 supportate da IoT Edge

Il modulo deve supportare un sottoinsieme (almeno uno) di piattaforme di livello 1 supportate da IoT Edge (come registrato nel supporto di [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Un modulo che usa questa opzione di piattaforma deve:

- Fornire un tag più recente e un tag di versione (ad esempio, 1.0.1) che sono tag manifesto compilati con lo [strumento manifesto](https://github.com/estesp/manifest-tool) GitHub se sono supportate più piattaforme. I tag di manifesto sono facoltativi se è supportata una sola piattaforma.
- Usare la scheda Dell'offerta nel [Centro per](https://partner.microsoft.com/dashboard/commercial-marketplace) i partner per aggiungere un collegamento nella sezione **Collegamenti utili** ad almeno un dispositivo IoT Edge del catalogo dei dispositivi certificati Azure [IoT Edge.](https://catalog.azureiotsolutions.com/)

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Questa è un'immagine della sezione Offerta Listing all'interno del Centro per i partner":::

### <a name="device-dimensions"></a>Dimensioni dei dispositivi

Le dimensioni del modulo IoT Edge (ad esempio CPU, RAM, storage e GPU) nei dispositivi IoT Edge mirati devono soddisfare i requisiti seguenti:

- Il modulo deve funzionare con almeno un dispositivo IoT Edge dal catalogo dei dispositivi certificati Azure [IoT Edge](https://catalog.azureiotsolutions.com/).

- I requisiti hardware minimi devono essere documentati come ultimo paragrafo nella descrizione dell'offerta (nella scheda dell'offerta nel [Centro per i partner).](https://partner.microsoft.com/dashboard/commercial-marketplace) È anche possibile elencare facoltativamente i requisiti hardware consigliati se differiscono in modo significativo. Aggiungere ad esempio la sezione seguente alla fine della descrizione dell'offerta:

Copiare questo testo HTML o utilizzare le funzioni RTF corrispondenti nella finestra di modifica.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Configurazione

Il modulo deve includere le impostazioni di configurazione predefinite per rendere la distribuzione in un dispositivo IoT Edge il più semplice possibile. Queste informazioni possono essere fornite nella pagina **Configurazione tecnica** per il piano nel Centro per i [partner.](https://partner.microsoft.com/dashboard/commercial-marketplace) Il contenitore può anche includere l'SDK del modulo Edge IoT per abilitare la comunicazione con l'hub perimetrale e l'hub IoT.

#### <a name="default-configuration"></a>Configurazione predefinita

I moduli IoT Edge devono essere in grado di iniziare con le impostazioni predefinite fornite nella pagina **Configurazione tecnica** per il piano nel Centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace). Sono disponibili le impostazioni predefinite seguenti:

- Default **routes** (Route predefinite)
- Proprietà **desiderate del modulo gemello** del modulo predefinito
- Variabili **di ambiente** predefinite
- Opzioni di **creazione del contenitore** predefinito

In uno scenario in cui un parametro necessario per un valore predefinito non ha senso (ad esempio, l'indirizzo IP del server di un cliente), aggiungere un parametro come valore predefinito. Questo valore è maiuscolo e racchiuso tra parentesi quadre. Per questo esempio viene configurata la variabile di ambiente predefinita seguente:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentazione della configurazione

Tutte le impostazioni di configurazione di un modulo IoT Edge devono essere chiaramente documentate. Ad esempio, è necessario documentare come utilizzare le route, le proprietà desiderate gemelle, le variabili di ambiente, createOptions e così via. È necessario fornire un collegamento alla documentazione o renderla parte della descrizione dell'offerta o del piano. È possibile fornire queste informazioni nella pagina **Offerta** e **Profilo del piano** nel Centro per i [partner.](https://partner.microsoft.com/dashboard/commercial-marketplace)

#### <a name="tags-and-versioning"></a>Tag e controllo delle versioni

I clienti devono essere in grado di distribuire facilmente un modulo e ottenere automaticamente gli aggiornamenti dal marketplace (in uno scenario per sviluppatori). Devono anche essere in grado di utilizzare e bloccare una versione esatta che hanno testato (in uno scenario di produzione).

Per soddisfare queste aspettative dei clienti ed essere pubblicati sul mercato, i moduli IoT Edge devono soddisfare i seguenti requisiti

- Includi un tag di ultima generazione manifesto che punta alla versione più recente su tutte le piattaforme supportate.
- Creare i tag di versione nel formato X.Y. , dove X, Y e z sono numeri interi.
- Includere un tag "version", ad esempio 1.0.1, che punti a una versione specifica su tutte le piattaforme supportate.
- Non aggiornare i tag "version", ad esempio 1.0.1, perché non devono essere modificati.

> [!NOTE]
> Facoltativamente, il controllo delle versioni può includere tag di "versione in sequenza", ad esempio 2.0 e 1.0.Optionally, versioning can include "rolling version" tags, such as 2.0 and 1.0. Questa opzione supporta la gestione di più versioni principali in parallelo.

### <a name="telemetry"></a>Telemetria

I moduli che usano l'SDK del modulo IoT devono impostare l'identificatore univoco del modulo su PublisherId.OfferId.SkuId per scopi di telemetria. Un identificatore univoco consente ad Azure Marketplace di identificare il numero di istanze del modulo in esecuzione.

Utilizzare uno dei seguenti metodi dagli SDK del modulo IoT per impostare ProductInfo su questo identificatore:

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Per i moduli che non utilizzano l'SDK del modulo IoT, sono disponibili informazioni meno precise tramite il Centro per i partner, ad esempio il numero di download.

### <a name="security"></a>Sicurezza

I moduli IoT Edge devono evitare [moduli con privilegi.](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) Chiedere invece l'accesso con privilegi minimi possibile all'host.

### <a name="module-iot-sdk"></a>SDK dei moduli IoT

L'inclusione dell'SDK dei moduli IoT non è un prerequisito per la certificazione, tuttavia può offrire un'esperienza utente migliore, ad esempio per il supporto del routing o l'invio di messaggi al cloud.

L'SDK del modulo IoT è necessario per ottenere dati di telemetria sul numero di istanze del modulo in esecuzione.

## <a name="recertification-process"></a>Processo di ricertificazione

I partner ricevono una notifica ogni volta che si nota una modifica sostanziale che influisce sui loro moduli, ad esempio:

- Matrice di supporto del sistema operativo/arco di livello 1 supportata da IoT Edge
- SDK dei moduli IoT
- Runtime di IoT Edge
- Linee guida per la certificazione dei moduli IoT Edge

I partner devono aggiornare e ricertificare le offerte ripubblicandole nel Centro per i [partner.](https://partner.microsoft.com/dashboard/commercial-marketplace)

L'offerta verrà inoltre ricertificata se la aggiorni, ad esempio se aggiungi nuovi tag immagine.

## <a name="host-module-in-azure-container-registry"></a>Host module in Azure Container Registry

Per caricare il modulo IoT Edge in Azure Marketplace, è innanzitutto necessario ospitarlo in un [Registro contenitori](https://azure.microsoft.com/services/container-registry/) di Azure.To upload your IoT Edge module to Azure Marketplace, you first need to host it in an Azure Container Registry (ACR). Il modulo deve includere tutti i tag che si desidera pubblicare, inclusi i tag immagine a cui fa riferimento un tag di manifesto. Per altre informazioni, vedere l'esercitazione Creare un registro contenitori di Azure ed eseguire il push di [un'immagine del contenitore.](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'offerta di moduli IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)