---
title: Informazioni su come eseguire la logica dei moduli nei dispositivi - Azure IoT Edge | Microsoft Docs
description: I moduli di Azure IoT Edge sono inclusi in contenitori di unità di logica che possono essere distribuiti e gestiti in remoto in modo che sia possibile eseguire la logica di business in dispositivi perimetrali IoT
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 90fb6eadb2edb92d4516d8565d8c2c2bd5120c05
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094186"
---
# <a name="understand-azure-iot-edge-modules"></a>Informazioni sui moduli Azure IoT Edge

Azure IoT Edge consente di distribuire e gestire la logica di business sui dispositivi perimetrali sotto forma di *moduli*. I moduli di Azure IoT Edge sono l'unità più piccola di calcolo gestita da IoT Edge e possono contenere i servizi di Azure, ad esempio Analisi di flusso di Azure, o il codice specifico per la soluzione. Per comprendere come i moduli vengono sviluppati, distribuiti e gestiti, è utile pensare ai quattro componenti concettuali del modulo:

* Un'**immagine del modulo** è un pacchetto che contiene il software che definisce il modulo.
* Un'**istanza del modulo** è l'unità di calcolo specifica che esegue l'immagine del modulo in un dispositivo di IoT Edge. L'istanza del modulo viene avviata dal runtime di IoT Edge.
* Un'**identità del modulo** è una parte di un'informazione, che include le credenziali di sicurezza, archiviata nell'hub IoT associata a ciascuna istanza del modulo.
* Un **modulo gemello** è un documento JSON archiviato nell'hub IoT, che contiene informazioni sullo stato di un'istanza del modulo, inclusi i metadati, le configurazioni e le condizioni. 

## <a name="module-images-and-instances"></a>Istanze e immagini del moduli

Le immagini del modulo di IoT Edge contengono applicazioni che sfruttano i vantaggi delle funzionalità di gestione, sicurezza e comunicazione del runtime di IoT Edge. È possibile sviluppare le proprie immagini di modulo o esportarne una da un servizio di Azure supportato, ad esempio Analisi di flusso di Azure.
Le immagini si trovano nel cloud e possono essere aggiornate, modificate e distribuite in diverse soluzioni. Ad esempio, un modulo che usa l'apprendimento automatico per stimare l'output della linea di produzione esiste come immagine separata di un modulo che usa la visione artificiale per controllare un drone. 

Ogni volta che un'immagine del modulo viene distribuita in un dispositivo e avviata dal runtime di IoT Edge, viene creata una nuova istanza del modulo. Due dispositivi in diverse parti del mondo possono usare la stessa immagine del modulo; tuttavia ognuno di essi ha la propria istanza del modulo quando il modulo viene avviato sul dispositivo. 

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

Un modulo gemello è un documento JSON che archivia le proprietà di configurazione e le informazioni del modulo. Questo concetto è parallelo a quello di [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md) dell'hub IoT. La struttura di un modulo gemello è esattamente lo stessa di quella di un dispositivo gemello. Anche le API usate per interagire con entrambi i tipi di gemelli sono le stesse. L'unica differenza tra i due è l'identità usata per creare un'istanza dell'SDK client. 

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

Azure IoT Edge supporta le operazioni offline sui dispositivi IoT Edge. Queste funzionalità sono limitate per il momento. 

I moduli IoT Edge possono essere offline per periodi prolungati purché siano soddisfatti i requisiti seguenti: 

* **La durata (TTL) del messaggio non è trascorsa**. Il valore predefinito per la durata (TTL) del messaggio è due ore, ma è possibile impostare un valore maggiore o minore nell'archivio e inoltrare la configurazione nelle impostazioni dell'hub di IoT Edge. 
* **I moduli non devono ripetere l'autenticazione con l'hub di IoT Edge se in modalità offline**. I moduli possono eseguire l'autenticazione solo con gli hub di Edge che dispongono di una connessione attiva con un hub IoT. I moduli devono essere autenticati di nuovo se vengono riavviati per qualsiasi motivo. I moduli possono comunque inviare i messaggi all'hub di Edge dopo che il token di firma di accesso condiviso è scaduto. Quando si riattiva la connettività, l'hub di Edge richiede un nuovo token dal modulo e la convalida con l'hub IoT. Se l'operazione ha esito positivo, l'hub di Edge inoltra i messaggi del modulo che ha archiviato, anche i messaggi inviati mentre il token del modulo era scaduto. 
* **Il modulo che ha inviato i messaggi mentre è offline funziona ancora quando viene riattivata la connettività**. Al momento della riconnessione all'hub IoT, l'hub di Edge deve convalidare un nuovo token di modulo (se il precedente è scaduto) prima di poter inoltrare i messaggi del modulo. Se il modulo non è disponibile per fornire un nuovo token, l'hub di Edge non può eseguire operazioni sui messaggi archiviati del modulo. 
* **L'hub di Edge dispone di spazio su disco per archiviare i messaggi**. Per impostazione predefinita, i messaggi vengono archiviati nel filesystem del contenitore dell'hub di Edge. È disponibile un'opzione di configurazione per specificare invece un volume montato per l'archiviazione dei messaggi. In entrambi i casi è necessario che sia disponibile spazio per archiviare i messaggi per il recapito posticipato all'hub IoT.  

Altre funzionalità offline sono disponibili in anteprima pubblica. Per altre informazioni, vedere [Informazioni sulle funzionalità per periodi offline prolungati per i dispositivi IoT Edge, i moduli e i dispositivi figlio](offline-capabilities.md).

## <a name="next-steps"></a>Passaggi successivi
 - [Informazioni sui requisiti e sugli strumenti per lo sviluppo di moduli IoT Edge](module-development.md)
 - [Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura](iot-edge-runtime.md)

<!-- Images -->
[2]: ./media/iot-edge-modules/identity.png
