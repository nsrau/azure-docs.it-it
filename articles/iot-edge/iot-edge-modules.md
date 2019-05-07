---
title: Informazioni su come eseguire la logica dei moduli nei dispositivi - Azure IoT Edge | Microsoft Docs
description: I moduli di Azure IoT Edge sono inclusi in contenitori di unità di logica che possono essere distribuiti e gestiti in remoto in modo che sia possibile eseguire la logica di business in dispositivi perimetrali IoT
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 65cac484a9395aca47a38e2ba430b80c868267f5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152658"
---
# <a name="understand-azure-iot-edge-modules"></a>Informazioni sui moduli Azure IoT Edge

Azure IoT Edge consente di distribuire e gestire la logica di business sui dispositivi perimetrali sotto forma di *moduli*. I moduli di Azure IoT Edge sono l'unità più piccola di calcolo gestita da IoT Edge e possono contenere i servizi di Azure, ad esempio Analisi di flusso di Azure, o il codice specifico per la soluzione. Per comprendere i moduli vengono sviluppati, distribuiti e gestiti, è utile considerare i quattro elementi concettuali di un modulo:

* Un'**immagine del modulo** è un pacchetto che contiene il software che definisce il modulo.
* Un'**istanza del modulo** è l'unità di calcolo specifica che esegue l'immagine del modulo in un dispositivo di IoT Edge. L'istanza del modulo viene avviata dal runtime di IoT Edge.
* Un'**identità del modulo** è una parte di un'informazione, che include le credenziali di sicurezza, archiviata nell'hub IoT associata a ciascuna istanza del modulo.
* Un **modulo gemello** è un documento JSON archiviato nell'hub IoT, che contiene informazioni sullo stato di un'istanza del modulo, inclusi i metadati, le configurazioni e le condizioni. 

## <a name="module-images-and-instances"></a>Istanze e immagini del moduli

Le immagini del modulo di IoT Edge contengono applicazioni che sfruttano i vantaggi delle funzionalità di gestione, sicurezza e comunicazione del runtime di IoT Edge. È possibile sviluppare le proprie immagini di modulo o esportarne una da un servizio di Azure supportato, ad esempio Analisi di flusso di Azure.
Le immagini si trovano nel cloud e possono essere aggiornate, modificate e distribuite in diverse soluzioni. Ad esempio, un modulo che usa l'apprendimento automatico per stimare l'output della linea di produzione esiste come immagine separata di un modulo che usa la visione artificiale per controllare un drone. 

Ogni volta che un'immagine del modulo viene distribuita in un dispositivo e avviata dal runtime di IoT Edge, viene creata una nuova istanza del modulo. Due dispositivi in diverse parti del mondo possono usare la stessa immagine del modulo. Tuttavia, ogni dispositivo avrebbe la propria istanza del modulo quando il modulo viene avviato sul dispositivo. 

![Diagramma - Immagini del modulo nel cloud, istanze del modulo nei dispositivi](./media/iot-edge-modules/image_instance.png)

Nell'implementazione, le immagini dei moduli esistono come immagini del contenitore in un repository e le istanze del modulo sono contenitori nei dispositivi. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identità del modulo

Quando viene creata una nuova istanza del modulo dal runtime di IoT Edge, l'istanza viene associata a un'identità del modulo corrispondente. L'identità del modulo viene archiviata nell'hub IoT e viene usata come ambito di indirizzamento e sicurezza per tutti le comunicazioni cloud locali per un modulo specifico.

L'identità associata all'istanza del modulo dipende dall'identità del dispositivo su cui è in esecuzione l'istanza e dal nome specificato per il modulo nella soluzione. Ad esempio, un modulo che usa un Analisi di flusso di Azure se si chiama `insight` e lo si distribuisce in un dispositivo chiamato `Hannover01`, il runtime di IoT Edge crea un'identità del modulo corrispondente denominata `/devices/Hannover01/modules/insight`.

Chiaramente, negli scenari in cui è necessario distribuire un'immagine del modulo più volte sullo stesso dispositivo, è possibile distribuire la stessa immagine più volte con nomi diversi.

![Diagramma - Le identità del modulo sono univoche all'interno dei dispositivi e tra dispositivi diversi](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Moduli gemelli

Ogni istanza del modulo ha anche un modulo gemello corrispondente che è possibile usare per configurare l'istanza del modulo. L'istanza e il gemello sono associati tra loro tramite l'identità del modulo. 

Un modulo gemello è un documento JSON che archivia le proprietà di configurazione e le informazioni del modulo. Questo concetto è parallelo a quello di [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md) dell'hub IoT. La struttura di un modulo gemello è esattamente la stessa di quella di un dispositivo gemello. Anche le API usate per interagire con entrambi i tipi di gemelli sono le stesse. L'unica differenza tra i due è l'identità usata per creare un'istanza dell'SDK client. 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Funzionalità offline

I moduli di Azure IoT Edge possono operare in modalità offline per un periodo illimitato dopo la sincronizzazione con l'IoT Hub almeno una volta. Dispositivi IoT Edge è anche possono estendere questa funzionalità non in linea con gli altri dispositivi IoT. Per altre informazioni, vedere [Informazioni sulle funzionalità per periodi offline prolungati per i dispositivi IoT Edge, i moduli e i dispositivi figlio](offline-capabilities.md).

## <a name="next-steps"></a>Passaggi successivi
 - [Informazioni sui requisiti e sugli strumenti per lo sviluppo di moduli IoT Edge](module-development.md)
 - [Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura](iot-edge-runtime.md)

