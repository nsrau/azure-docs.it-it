---
title: Preparare le risorse tecniche del modulo IoT Edge-Azure Marketplace
description: Per informazioni sui requisiti tecnici e di configurazione, è necessario che gli asset tecnici del modulo perimetrale di Internet delle cose siano soddisfatti prima di poterli pubblicare in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: dec0711c4763983e520d247fd8b775b1810e0479
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324642"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Preparare gli asset tecnici del modulo IoT Edge

Questo articolo descrive i requisiti che gli asset tecnici del modulo perimetrale di Internet delle cose devono soddisfare prima di essere pubblicati in Azure Marketplace.

## <a name="get-started"></a>Operazioni preliminari

Un modulo IoT Edge è un contenitore compatibile con Docker che viene eseguito in un dispositivo IoT Edge.

- Per altre informazioni sui moduli Azure IoT Edge, vedere [Informazioni sui moduli Azure IoT Edge](../../iot-edge/iot-edge-modules.md).
- Per iniziare a usare lo sviluppo del modulo IoT Edge, vedere [sviluppare moduli IOT Edge personalizzati](../../iot-edge/module-development.md).

## <a name="technical-requirements"></a>Requisiti tecnici

Il modulo di IoT Edge deve soddisfare i requisiti tecnici seguenti per la certificazione e la pubblicazione in Azure Marketplace.

### <a name="platform-support"></a>Piattaforme supportate

Il modulo di IoT Edge deve supportare una delle opzioni di piattaforma seguenti:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Piattaforme di livello 1 supportate da IoT Edge

Il modulo deve supportare tutte le piattaforme di livello 1 supportate da IoT Edge (registrate nel [supporto Azure IOT Edge](../../iot-edge/support.md)). Questa è l'opzione consigliata, poiché offre un'esperienza utente di livello superiore. Verranno presentati i moduli che soddisfano questi criteri. Un modulo che usa questa opzione di piattaforma deve:

- Fornire un tag più recente e un tag di versione (ad esempio, 1.0.1) che sono tag del manifesto compilati con lo [strumento Manifesto di GitHub](https://github.com/estesp/manifest-tool).

- Usare la scheda elenco offerte del [centro](https://partner.microsoft.com/dashboard/commercial-marketplace) per i partner per aggiungere un collegamento nella sezione **collegamenti utili** all' [Azure IOT Edge catalogo dei dispositivi certificato](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Subset di piattaforme di livello 1 supportate da IoT Edge

Il modulo deve supportare un subset (almeno uno) di piattaforme di livello 1 supportate da IoT Edge (come registrato in [Azure IOT Edge supporto](../../iot-edge/support.md)). Un modulo che usa questa opzione di piattaforma deve:

- Fornire un tag più recente e un tag di versione (ad esempio, 1.0.1) che sono tag del manifesto compilati con lo [strumento Manifesto](https://github.com/estesp/manifest-tool) di GitHub se è supportata più di una piattaforma. I tag di manifesto sono facoltativi se è supportata una sola piattaforma.
- Usare la scheda elenco offerte del [centro](https://partner.microsoft.com/dashboard/commercial-marketplace) per i partner per aggiungere un collegamento nella sezione **collegamenti utili** ad almeno un dispositivo IOT Edge dal [Catalogo dei dispositivi Azure IOT Edge certificati](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Si tratta di un'immagine della sezione relativa all'elenco di offerte nel centro per i partner":::

### <a name="device-dimensions"></a>Dimensioni dei dispositivi

IoT Edge dimensioni del modulo, ad esempio CPU, RAM, archiviazione e GPU, nei dispositivi IoT Edge di destinazione devono soddisfare i requisiti seguenti:

- Il modulo deve funzionare con almeno un dispositivo IoT Edge dal catalogo di [dispositivi Azure IOT Edge certificati](https://catalog.azureiotsolutions.com/).

- I requisiti hardware minimi devono essere documentati come ultimo paragrafo della descrizione dell'offerta (nella scheda elenco offerte del centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace)). È anche possibile elencare facoltativamente i requisiti hardware consigliati se differiscono in modo significativo. Aggiungere ad esempio la sezione seguente alla fine della descrizione dell'offerta:

Copiare questo testo HTML o usare le funzioni di testo RTF corrispondenti nella finestra di modifica.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Configurazione

Il modulo deve includere le impostazioni di configurazione predefinite per rendere la distribuzione a un dispositivo IoT Edge il più semplice possibile. Queste informazioni possono essere fornite nella pagina **configurazione tecnica** per il piano nel centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace). Il contenitore può includere anche l'SDK del modulo IoT Edge per abilitare la comunicazione con l'Hub Edge e l'hub Internet.

#### <a name="default-configuration"></a>Configurazione predefinita

I moduli IoT Edge devono essere in grado di iniziare con le impostazioni predefinite specificate nella pagina **configurazione tecnica** per il piano nel centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace). Sono disponibili le impostazioni predefinite seguenti:

- **Route** predefinite
- **Proprietà desiderate del modulo gemello** predefinito
- **Variabili di ambiente** predefinite
- **Opzioni di creazione** predefinite del contenitore

In uno scenario in cui un parametro necessario per un valore predefinito non ha senso, ad esempio l'indirizzo IP del server del cliente, aggiungere un parametro come valore predefinito. Questo valore è maiuscolo e racchiuso tra parentesi quadre. Per questo esempio viene configurata la variabile di ambiente predefinita seguente:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentazione della configurazione

Tutte le impostazioni di configurazione di un modulo di IoT Edge devono essere documentate chiaramente. Ad esempio, è necessario documentare come usare le route, le proprietà desiderate del dispositivo gemello, le variabili di ambiente, createOptions e così via. È necessario fornire un collegamento alla documentazione o far parte dell'offerta o della descrizione del piano. È possibile fornire queste informazioni nella pagina **elenco offerte** e **elenco di piani** del [centro](https://partner.microsoft.com/dashboard/commercial-marketplace)per i partner.

#### <a name="tags-and-versioning"></a>Tag e controllo delle versioni

I clienti devono essere in grado di distribuire facilmente un modulo e ottenere automaticamente gli aggiornamenti dal Marketplace (in uno scenario di sviluppo). Devono inoltre essere in grado di utilizzare e bloccare una versione esatta testata (in uno scenario di produzione).

Per soddisfare queste aspettative dei clienti e pubblicarle nel Marketplace, i moduli IoT Edge devono soddisfare i requisiti seguenti

- Includere un tag più recente del manifesto che punta alla versione più recente su tutte le piattaforme supportate.
- Creare tag di versione nel formato X. Y. Z, dove X, Y e Z sono numeri interi.
- Includere un tag "Version", ad esempio 1.0.1, che punta a una versione specifica su tutte le piattaforme supportate.
- Non aggiornare i tag "Version", ad esempio 1.0.1, perché non devono essere modificati.

> [!NOTE]
> Facoltativamente, il controllo delle versioni può includere tag "versione in sequenza", ad esempio 2,0 e 1,0. Questa opzione supporta la gestione di più versioni principali in parallelo.

### <a name="telemetry"></a>Telemetria

I moduli che usano l'SDK del modulo Internet delle cose devono impostare l'identificatore univoco del modulo su PublisherId. IDOfferta. SkuId per finalità di telemetria. Un identificatore univoco consente ad Azure Marketplace di identificare il numero di istanze di modulo in esecuzione.

Usare uno dei metodi seguenti dagli SDK dei moduli di Internet delle cose per impostare ProductInfo su questo identificatore:

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Per i moduli che non usano l'SDK del modulo Internet, sono disponibili informazioni dettagliate meno precise tramite il centro per i partner, ad esempio il numero di download.

### <a name="security"></a>Sicurezza

IoT Edge moduli devono evitare i [moduli con privilegi](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). In alternativa, è possibile richiedere l'accesso con privilegi minimi all'host.

### <a name="module-iot-sdk"></a>SDK dei moduli IoT

L'inclusione dell'SDK dei moduli IoT non è un prerequisito per la certificazione, tuttavia può offrire un'esperienza utente migliore, ad esempio per il supporto del routing o l'invio di messaggi al cloud.

Per ottenere i dati di telemetria sul numero di istanze di modulo in esecuzione, è necessario l'SDK del modulo.

## <a name="recertification-process"></a>Processo di ricertificazione

I partner ricevono una notifica ogni volta che viene apportata una modifica sostanziale che interessa i moduli, ad esempio:

- Matrice del supporto del sistema operativo/Arch di livello 1 supportata da IoT Edge
- SDK dei moduli IoT
- Runtime di IoT Edge
- Linee guida per la certificazione del modulo IoT Edge

I partner devono aggiornare e ricertificare le offerte ripubblicando tali offerte nel centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace).

L'offerta verrà ricertificata anche se aggiornata, ad esempio aggiungendo nuovi tag di immagine.

## <a name="host-module-in-azure-container-registry"></a>Modulo host in Container Registry di Azure

Per caricare il modulo IoT Edge in Azure Marketplace, è necessario prima ospitarlo in una [container Registry di Azure](https://azure.microsoft.com/services/container-registry/) (ACR). Il modulo deve includere tutti i tag che si desidera pubblicare, inclusi i tag di immagine a cui fa riferimento un tag manifesto. Per altre informazioni, vedere l'esercitazione [creare un registro contenitori di Azure ed effettuare il push di un'immagine del contenitore](../../container-instances/container-instances-tutorial-prepare-acr.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'offerta di moduli IoT Edge](azure-iot-edge-module-creation.md)