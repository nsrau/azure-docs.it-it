---
title: Informazioni sui moduli di Azure IoT Edge | Microsoft Docs
description: "Informazioni sui moduli di Azure IoT Edge e sulle relative modalità di configurazione"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0f3ce7496427b6975eb4ac476e7d1737321ed2e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="understand-azure-iot-edge-modules---preview"></a>Informazioni sui moduli di Azure IoT Edge: anteprima

Azure IoT Edge consente di distribuire e gestire la logica di business sui dispositivi perimetrali sotto forma di *moduli*. I moduli di Azure IoT Edge sono l'unità più piccola di calcolo gestita da IoT Edge e possono contenere i servizi di Azure, ad esempio Analisi di flusso di Azure, o il codice specifico per la soluzione. Per comprendere come i moduli vengono sviluppati, distribuiti e gestiti, è utile pensare ai quattro componenti concettuali del modulo:

* Un'**immagine del modulo** è un pacchetto che contiene il software che definisce il modulo.
* Un'**istanza del modulo** è l'unità di calcolo specifica che esegue l'immagine del modulo in un dispositivo di IoT Edge. L'istanza del modulo viene avviata dal runtime di IoT Edge.
* Un'**identità del modulo** è una parte di un'informazione, che include le credenziali di sicurezza, archiviata nell'hub IoT associata a ciascuna istanza del modulo.
* Un **modulo gemello** è un documento JSON archiviato nell'hub IoT, che contiene informazioni sullo stato di un'istanza del modulo, inclusi i metadati, le configurazioni e le condizioni. 

## <a name="module-images-and-instances"></a>Istanze e immagini del moduli

Le immagini del modulo di IoT Edge contengono applicazioni che sfruttano i vantaggi delle funzionalità di gestione, sicurezza e comunicazione del runtime di IoT Edge. È possibile sviluppare le proprie immagini di modulo o esportarne una da un servizio di Azure supportato, ad esempio Analisi di flusso di Azure.
Le immagini si trovano nel cloud e possono essere aggiornate, modificate e distribuite in diverse soluzioni. Ad esempio, un modulo che usa l'apprendimento automatico per stimare l'output della linea di produzione esiste come immagine separata di un modulo che usa la visione artificiale per controllare un drone. 

Ogni volta che un'immagine del modulo viene distribuita in un dispositivo e avviata dal runtime di IoT Edge, viene creata una nuova istanza del modulo. Due dispositivi in diverse parti del mondo possono usare la stessa immagine del modulo; tuttavia ognuno di essi ha la propria istanza del modulo quando il modulo viene avviato sul dispositivo. 

![Immagini del modulo nel cloud: istanze del modulo nei dispositivi][1]

Nell'implementazione, le immagini dei moduli esistono come immagini del contenitore in un repository e le istanze del modulo sono contenitori nei dispositivi. Man mano che i casi d'uso per Azure IoT Edge aumentano, vengono creati nuovi tipi di immagini e istanze del modulo. Ad esempio, i dispositivi con risorse limitate non possono eseguire i contenitori pertanto sono necessarie immagini di moduli disponibili come librerie a collegamento dinamico e istanze eseguibili. 

## <a name="module-identities"></a>Identità del modulo

Quando viene creata una nuova istanza del modulo dal runtime di IoT Edge, l'istanza viene associata a un'identità del modulo corrispondente. L'identità del modulo viene archiviata nell'hub IoT e viene usata come ambito di indirizzamento e sicurezza per tutti le comunicazioni cloud locali per un modulo specifico.
L'identità associata all'istanza del modulo dipende dall'identità del dispositivo su cui è in esecuzione l'istanza e dal nome specificato per il modulo nella soluzione. Ad esempio, un modulo che usa un Analisi di flusso di Azure se si chiama `insight` e lo si distribuisce in un dispositivo chiamato `Hannover01`, il runtime di IoT Edge crea un'identità del modulo corrispondente denominata `/devices/Hannover01/modules/insight`.

Chiaramente, negli scenari in cui è necessario distribuire un'immagine del modulo più volte sullo stesso dispositivo, è possibile distribuire la stessa immagine più volte con nomi diversi.

![Le identità del modulo sono univoche][2]

## <a name="module-twins"></a>Moduli gemelli

Ogni istanza del modulo ha anche un modulo gemello corrispondente che è possibile usare per configurare l'istanza del modulo. L'istanza e il gemello sono associati tra loro tramite l'identità del modulo. 

Un modulo gemello è un documento JSON che archivia le proprietà di configurazione e le informazioni del modulo. Questo concetto è parallelo a quello di [dispositivo gemello][lnk-device-twin] dell'hub IoT. La struttura di un modulo gemello è esattamente lo stessa di quella di un dispositivo gemello. Anche le API usate per interagire con entrambi i tipi di gemelli sono le stesse. L'unica differenza tra i due è l'identità usata per creare un'istanza dell'SDK client. 

```csharp
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="next-steps"></a>Passaggi successivi
 - [Informazioni sul runtime di Azure IoT Edge e la relativa architettura][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md