---
title: Novità Aggiornamento Plug and Play IoT (anteprima) | Microsoft Docs
description: Informazioni sulle novità della release di aggiornamento di Plug and Play IoT (anteprima).
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: c2dcba05e3b1fc5cfc32d6f1b38c22b811b73626
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352721"
---
# <a name="iot-plug-and-play-preview-refresh"></a>Aggiornamento di Plug and Play IoT (anteprima)

Questo articolo illustra le modifiche principali apportate a SDK, librerie, strumenti e servizi nella release di aggiornamento di Plug and Play IoT (anteprima) nel mese di luglio 2020. La release precedente di Plug and Play IoT (anteprima) è stata rilasciata nel mese di agosto 2019.

## <a name="digital-twins-definition-language-dtdl"></a>DTDL (Digital Twins Definition Language)

Questa release aggiunge supporto per [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) e depreca [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview).

L'elenco seguente mostra le differenze principali tra DTDL v1 e DTDL v2. In DTDL v2:

- Gli ID modello hanno il prefisso `dtmi` invece di `urn`. Gli identificatori dei modelli di gemelli digitali sostituiscono gli ID con prefisso `urn` dei modelli digitali usati in DTDL v1. La versione è ora preceduta da `;`. Ad esempio, mentre in precedenza si usava `urn:CompanyName:Model:1`, si usa ora `dtmi:CompanyName:Model;1`.
- Impostare `@context` su `dtmi:dtdl:context;2` invece di `http://azureiot.com/v1/contexts/IoTModel.json`.
- Usare il tipo **Interface** invece dei tipi **CapabilityModel** per modellare un dispositivo.
- Il valore **Components** sostituisce le istanze di **Interface**. È possibile definire **Relationships** e **Components** come parte dei contenuti di **Interface**.
- Un tipo **Interface** può ereditare da un altro tipo **Interface**.
- È possibile estendere DTDL usando _tipi semantici estendibili_. Questo sistema di tipi estendibili offre maggiore flessibilità rispetto ai tipi semantici hardcoded, ad esempio i tipi relativi a temperatura e umidità in DTDL v1.
- La proprietà **displayUnit** è stata rimossa.
- Non è possibile usare caratteri di sottolineatura iniziali o finali in un nome. I caratteri di sottolineatura iniziali in un nome sono riservati all'uso da parte del sistema.

Per usare DTDL v1, è necessario usare la versione precedente dell'SDK e Azure IoT Explorer 0.10.x. Per usare DTDL v2, è necessaria la versione più recente dell'SDK e Azure IoT Explorer 0.11.x.

## <a name="no-component-and-multiple-component-implementations"></a>Implementazioni senza componenti e con più componenti

I dispositivi semplici che usano pochi dati di telemetria, pochi comandi o poche proprietà possono essere descritti in una singola interfaccia senza usare i componenti. Qualsiasi dispositivo esistente può diventare un Plug and Play IoT annunciando l'**ID modello** senza modifiche all'implementazione esistente del dispositivo.

È possibile che i dispositivi più complessi raggruppino dati di telemetria, comandi e proprietà in diversi interfacce per gestire la complessità e consentire il riutilizzo tra i dispositivi. È necessario aggiornare questi dispositivi in modo che seguano un set di semplici regole definite nelle [convenzioni dei messaggi di Plug and Play IoT (anteprima)](concepts-convention.md).

## <a name="registration-and-discovery"></a>Registrazione e individuazione

In questa release i dispositivi annunciano il rispettivo **ID modello** con l'hub IoT a ogni connessione. L'hub IoT memorizza nella cache l'**ID modello** consentendo quindi alle soluzioni back-end di recuperare l'**ID modello** usando la proprietà `modelId` del dispositivo gemello. L'**ID modello** può essere recuperato anche da `$metadata.$model` nel gemello digitale.

## <a name="microsoft-defined-interfaces"></a>Interfacce definite da Microsoft

Le interfacce seguenti definite da Microsoft sono deprecate e non vengono pubblicate nel nuovo repository di modelli:

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

L'interfaccia seguente viene pubblicata nel nuovo repository di modelli, `dtmi:azure:DeviceManagement:DeviceInformation;1`, disponibile nell'URL [https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview).

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

La struttura di eventi di **DigitalTwinChangeEvents** come [origine evento](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) è stata modificata in modo da usare il formato **JSON-Patch**. Si tratta di una modifica che causa un'interruzione, senza supporto per la compatibilità con le versioni precedenti.

## <a name="message-routing"></a>Routing dei messaggi

I messaggi di telemetria presentano le modifiche seguenti nella raccolta [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md).

La raccolta include ora una proprietà **dt-dataschema** che corrisponde all'**ID modello** registrato dal dispositivo.

La proprietà **dt-subject** rappresenta il componente che invia il messaggio di telemetria.

La proprietà **iothub-interface-name** è deprecata.

## <a name="device-and-service-sdks"></a>SDK di servizi e dispositivi

Non è disponibile la compatibilità con le versioni precedenti per le versioni di anteprima precedenti degli SDK. Se si passa alla versione di anteprima più recente di un SDK, sarà necessario modificare il codice.

Grazie all'approccio basato sulle convenzioni, non sono necessari SDK client separati. Le librerie **DigitalTwinClient** esistenti vengono deprecate in tutti i linguaggi da questa release di anteprima. Gli SDK client del dispositivo dell'hub IoT sono stati invece aggiornati in modo da includere un'opzione per l'annuncio dell'**ID modello**.

I dispositivi che non usano componenti necessitano di modifiche minime al codice, ovvero il semplice annuncio dell'**ID modello**. I dispositivi più complessi che usano più componenti potrebbero richiedere alcune funzioni riutilizzabili per implementare le [convenzioni](concepts-convention.md). Gli esempi di dispositivi includono un set di funzioni che possono essere riutilizzate nell'implementazione del dispositivo.

### <a name="service-sdks"></a>SDK per servizi

L'SDK del servizio è disponibile in [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) e [Python](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md).

## <a name="vs-code-extension"></a>Estensione di Visual Studio Code

L'estensione [Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) fornisce il supporto per la creazione per DTDL v1, l'integrazione con la versione precedente del repository di modelli e la generazione del codice.

Se si necessita di supporto per la creazione di DTDL v2 in Visual Studio Code, installare la nuova [estensione DTDL](https://github.com/azure/vscode-dtdl) in Visual Studio Code. L'estensione non fornisce l'integrazione con il repository di modelli o la generazione del codice. La gestione dei modelli nel repository viene ora eseguita tramite un'[interfaccia utente Web](https://aka.ms/iotmodelrepo).

## <a name="digital-twin-service-side-rest-apis"></a>API REST lato servizio di gemelli digitali

Sono state apportate modifiche alle [API REST lato servizio di gemelli digitali](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) e ai payload. Le API supportate sono:

- Recuperare un gemello digitale.
- Chiamare un comando.
- Aggiornare un gemello digitale con il payload **JSON-Patch**.

Le API REST esistenti continuano a essere supportate in questa release.

## <a name="model-repository"></a>Repository modelli

È ora disponibile un singolo repository di modelli che contiene i modelli pubblicati pubblici e i modelli aziendali privati protetti tramite Controllo degli accessi in base al ruolo. Tutti i modelli hanno un identificatore univoco e non sono modificabili dopo la creazione.

I repository di modelli aziendali esistenti della release precedente non sono supportati in questa release. È possibile continuare a usare il sito Web di [Azure Certified per IoT](https://preview.catalog.azureiotsolutions.com/products) per gestire i modelli di DTDL v1 precedenti. Non è tuttavia più possibile usare questo sito Web per registrare, testare e certificare i dispositivi.

L'estensione di Azure IoT per l'interfaccia della riga di comando di Azure non supporta il nuovo repository di modelli. I comandi `az iot pnp` funzionano solo nei repository di modelli della release precedente.

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central è attualmente in fase di aggiornamento per supportare la release di aggiornamento di Plug and Play IoT (anteprima).
