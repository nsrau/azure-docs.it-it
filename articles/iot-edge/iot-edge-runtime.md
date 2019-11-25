---
title: Informazioni su come il runtime gestisce i dispositivi - Azure IoT Edge | Microsoft Docs
description: Learn how the IoT Edge runtime manages modules, security, communication, and reporting on your devices
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6382159c2a9d73b9db21dd0467be0e68cf4b4c2d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456605"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura

The IoT Edge runtime is a collection of programs that turn a device into an IoT Edge device. Collectively, the IoT Edge runtime components enable IoT Edge devices to receive code to run at the edge and communicate the results. 

The IoT Edge runtime is responsible for the following functions on IoT Edge devices:

* Installare e aggiornare i carichi di lavoro nel dispositivo.
* Mantenere gli standard di sicurezza di Azure IoT Edge sul dispositivo.
* Ensure that [IoT Edge modules](iot-edge-modules.md) are always running.
* Segnalare l'integrità dei moduli al cloud per il monitoraggio remoto.
* Manage communication between downstream devices and IoT Edge devices.
* Manage communication between modules on the IoT Edge device.
* Manage communication between the IoT Edge device and the cloud.

![Il runtime comunica informazioni dettagliate e l'integrità del modulo all'hub IoT](./media/iot-edge-runtime/Pipeline.png)

Le responsabilità del runtime di IoT Edge rientrano in due categorie: comunicazione e gestione dei moduli. These two roles are performed by two components that are part of the IoT Edge runtime. The *IoT Edge hub* is responsible for communication, while the *IoT Edge agent* deploys and monitors the modules. 

Sia l'hub di IoT Edge che l'agente di IoT Edge sono moduli, proprio come gli altri moduli in esecuzione su un dispositivo IoT Edge. They're sometimes referred to as the *runtime modules*. 

## <a name="iot-edge-hub"></a>Hub di IoT Edge

L'hub di IoT Edge è uno dei due moduli che costituiscono il runtime di Azure IoT Edge. Opera come un proxy locale per l'hub IoT esponendo gli stessi endpoint del protocollo dell'hub IoT. Questa coerenza implica che i client, siano essi dispositivi o moduli, possono connettersi al runtime di IoT Edge esattamente come si connetterebbero all'hub IoT. 

>[!NOTE]
> IoT Edge hub supports clients that connect using MQTT or AMQP. Non supporta i client che usano HTTP. 

L'hub di IoT Edge non è una versione completa dell'hub IoT in esecuzione in locale. Esistono alcune operazioni che l'hub di IoT Edge delega automaticamente all'hub IoT. Ad esempio, l'hub di IoT Edge inoltra le richieste di autenticazione all'hub IoT quando un dispositivo prova a connettersi per la prima volta. Dopo aver stabilito la prima connessione, le informazioni di sicurezza vengono memorizzate nella cache locale dall'hub di IoT Edge. Le connessioni successive da tale dispositivo vengono consentite senza dover eseguire l'autenticazione nel cloud. 

Per ridurre la larghezza di banda usata dalla soluzione IoT Edge, l'hub di IoT Edge ottimizza il numero di connessioni effettivamente eseguite nel cloud. IoT Edge hub takes logical connections from clients like modules or downstream devices and combines them for a single physical connection to the cloud. I dettagli di questo processo sono trasparenti per il resto della soluzione. Ai client sembra di avere ognuno la propria la connessione al cloud ma in realtà usano tutti la stessa connessione. 

![L'hub di IoT Edge è un gateway tra dispositivi fisici e l'hub IoT](./media/iot-edge-runtime/Gateway.png)

L'hub di IoT Edge può determinare se è connesso all'hub IoT. Se la connessione viene persa, l'hub di IoT Edge salva i messaggi o gli aggiornamenti gemelli in locale. Quando viene ristabilita la connessione, tutti i dati vengono sincronizzati. Il percorso usato per questa cache temporanea è determinato da una proprietà del modulo gemello dell'hub di IoT Edge. Le dimensioni della cache non sono limitate e aumentano fino a quando il dispositivo ha capacità di archiviazione. For more information, see [Offline capabilities](offline-capabilities.md).

### <a name="module-communication"></a>Comunicazione del modulo

L'hub di IoT Edge facilita la comunicazione tra moduli. L'uso dell'hub di IoT Edge come broker di messaggi consente ai moduli di rimanere indipendenti. Per i moduli è sufficiente specificare gli input su cui accettano i messaggi e gli output su cui scrivono i messaggi. A solution developer can stitch these inputs and outputs together so that the modules process data in the order specific to that solution. 

![L'hub di IoT Edge facilita la comunicazione tra moduli](./media/iot-edge-runtime/module-endpoints.png)

Per inviare i dati all'hub di IoT Edge, un modulo chiama il metodo SendEventAsync. Il primo argomento specifica su quale output inviare il messaggio. The following pseudocode sends a message on **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

Per ricevere un messaggio, registrare un callback che elabori i messaggi in arrivo in un input specifico. The following pseudocode registers the function messageProcessor to be used for processing all messages received on **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

For more information about the ModuleClient class and its communication methods, see the API reference for your preferred SDK language: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Lo sviluppatore di soluzioni è responsabile della definizione delle regole che determinano il modo in cui l'hub di IoT Edge passa i messaggi tra i moduli. Routing rules are defined in the cloud and pushed down to IoT Edge hub in its module twin. La stessa sintassi per le route dell'hub IoT viene usata per definire le route tra i moduli di Azure IoT Edge. For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

![Le route tra i moduli passano attraverso l'hub di IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente di IoT Edge

L'agente di IoT Edge è l'altro modulo che costituisce il runtime di Azure IoT Edge. È responsabile della creazione di istanze per i moduli, ne garantisce la continua esecuzione e segnala lo stato dei moduli all'hub IoT. This configuration data is written as a property of the IoT Edge agent module twin. 

Il [daemon di sicurezza di IoT Edge](iot-edge-security-manager.md) avvia l'agente di IoT Edge all'avvio del dispositivo. L'agente recupera il modulo gemello dall'hub IoT e controlla il manifesto della distribuzione. Il manifesto della distribuzione è un file JSON che dichiara i moduli da avviare. 

Ogni elemento nel manifesto della distribuzione contiene informazioni specifiche su un modulo e viene usato dall'agente di IoT Edge per controllare il ciclo di vita del modulo. Di seguito sono riportate alcune delle proprietà più interessanti: 

* **settings.image**: l'immagine del contenitore usata dall'agente di IoT Edge per avviare il modulo. Se l'immagine è protetta da password, l'agente di IoT Edge deve essere configurato con le credenziali per il registro contenitori. È possibile configurare in remoto le credenziali per il registro contenitori usando il manifesto della distribuzione o aggiornando il file `config.yaml` nella cartella di programma IoT Edge del dispositivo IoT Edge.
* **settings.createOptions** – A string that is passed directly to the Moby container daemon when starting a module’s container. Adding options in this property allows for advanced configurations like port forwarding or mounting volumes into a module’s container.  
* **status**: lo stato in cui l'agente di IoT Edge inserisce il modulo. Usually, this value is set to *running* as most people want the IoT Edge agent to immediately start all modules on the device. È tuttavia possibile specificare lo stato iniziale di un modulo come stopped e attendere un secondo momento per indicare all'agente di IoT Edge di avviarlo. The IoT Edge agent reports the status of each module back to the cloud in the reported properties. Una differenza tra la proprietà desiderata e la proprietà segnalata è indicativa del comportamento errato di un dispositivo. Gli stati supportati sono:
   * Download in corso
   * Running
   * Non integro
   * Failed
   * Arrestata
* **restartPolicy**: la modalità in cui l'agente di IoT Edge riavvia un modulo. Possibili valori:
   * `never` – The IoT Edge agent never restarts the module.
   * `on-failure` - If the module crashes, the IoT Edge agent restarts it. Se il modulo viene chiuso correttamente, l'agente di IoT Edge non lo riavvia.
   * `on-unhealthy` - If the module crashes or is considered unhealthy, the IoT Edge agent restarts it.
   * `always` - If the module crashes, is considered unhealthy, or shuts down in any way, the IoT Edge agent restarts it. 
* **imagePullPolicy** - Whether the IoT Edge agent attempts to pull the latest image for a module automatically or not. If you don't specify a value, the default is *onCreate*. Possibili valori: 
   * `on-create` - When starting a module or updating a module based on a new deployment manifest, the IoT Edge agent will attempt to pull the module image from the container registry.
   * `never` - The IoT Edge agent will never attempt to pull the module image from the container registry. The expectation is that the module image is cached on the device, and any module image updates are made manually or managed by a third-party solution. 

L'agente di IoT Edge invia la risposta runtime all'hub IoT. Ecco un elenco di risposte possibili:
  * 200 - OK
  * 400 - La configurazione della distribuzione è in formato non corretto o non valida.
  * 417 - The device doesn't have a deployment configuration set.
  * 412 - La versione dello schema nella configurazione della distribuzione non è valida.
  * 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
  * 500 - Si è verificato un errore nel runtime di IoT Edge.

For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

### <a name="security"></a>Sicurezza

L'agente di IoT Edge svolge un ruolo fondamentale nella protezione di un dispositivo di IoT Edge. Ad esempio, esegue azioni come la verifica di un'immagine del modulo prima di avviarla. 

For more information about the Azure IoT Edge security framework, read about the [IoT Edge security manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md)
