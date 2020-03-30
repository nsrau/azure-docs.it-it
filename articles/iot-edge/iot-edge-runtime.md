---
title: Informazioni su come il runtime gestisce i dispositivi - Azure IoT Edge | Microsoft Docs
description: Scopri come il runtime di IoT Edge gestisce i moduli, la sicurezza, le comunicazioni e i report sui tuoi dispositivi
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284902"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura

Il runtime Azure IoT Edge è una raccolta di programmi che trasformano un dispositivo in un dispositivo IoT Edge. Collettivamente, i componenti di runtime di IoT Edge consentono ai dispositivi IoT Edge di ricevere codice per l'esecuzione e la comunicazione dei risultati.

Il runtime IoT Edge è responsabile delle seguenti funzioni nei dispositivi IoT Edge:

* Installare e aggiornare i carichi di lavoro nel dispositivo.
* Mantenere gli standard di sicurezza di Azure IoT Edge sul dispositivo.
* Assicurarsi che i [moduli IoT Edge](iot-edge-modules.md) siano sempre in esecuzione.
* Segnalare l'integrità dei moduli al cloud per il monitoraggio remoto.
* Gestire le comunicazioni tra dispositivi downstream e dispositivi IoT Edge.
* Gestire la comunicazione tra i moduli nel dispositivo IoT Edge.
* Gestire la comunicazione tra il dispositivo IoT Edge e il cloud.

![Il runtime comunica informazioni dettagliate e l'integrità del modulo all'hub IoT](./media/iot-edge-runtime/Pipeline.png)

Le responsabilità del runtime di IoT Edge rientrano in due categorie: comunicazione e gestione dei moduli. Questi due ruoli vengono eseguiti da due componenti che fanno parte del runtime di IoT Edge.These two roles are performed by two components that are part of the IoT Edge runtime.*L'hub IoT Edge* è responsabile della comunicazione, mentre l'agente *IoT Edge* distribuisce e monitora i moduli.

Sia l'hub di IoT Edge che l'agente di IoT Edge sono moduli, proprio come gli altri moduli in esecuzione su un dispositivo IoT Edge. A volte sono indicati come *i moduli*di runtime .

## <a name="iot-edge-hub"></a>Hub di IoT Edge

L'hub di IoT Edge è uno dei due moduli che costituiscono il runtime di Azure IoT Edge. Opera come un proxy locale per l'hub IoT esponendo gli stessi endpoint del protocollo dell'hub IoT. Questa coerenza implica che i client, siano essi dispositivi o moduli, possono connettersi al runtime di IoT Edge esattamente come si connetterebbero all'hub IoT.

>[!NOTE]
> L'hub IoT Edge supporta i client che si connettono tramite MQTT o AMQP. Non supporta i client che usano HTTP.

L'hub IoT Edge non è una versione completa dell'hub IoT in esecuzione in locale. IoT Edge hub silently delegates some tasks to IoT Hub. Ad esempio, l'hub di IoT Edge inoltra le richieste di autenticazione all'hub IoT quando un dispositivo prova a connettersi per la prima volta. Dopo aver stabilito la prima connessione, le informazioni di sicurezza vengono memorizzate nella cache locale dall'hub di IoT Edge. Le connessioni future da quel dispositivo sono consentite senza dover eseguire nuovamente l'autenticazione nel cloud.

Per ridurre la larghezza di banda utilizzata dalla soluzione IoT Edge, l'hub IoT Edge ottimizza il numero di connessioni effettive eseguite al cloud. L'hub IoT Edge accetta connessioni logiche da moduli o dispositivi downstream e le combina per una singola connessione fisica al cloud. I dettagli di questo processo sono trasparenti per il resto della soluzione. Ai client sembra di avere ognuno la propria la connessione al cloud ma in realtà usano tutti la stessa connessione.

![L'hub di IoT Edge è un gateway tra dispositivi fisici e l'hub IoT](./media/iot-edge-runtime/Gateway.png)

L'hub di IoT Edge può determinare se è connesso all'hub IoT. Se la connessione viene persa, l'hub di IoT Edge salva i messaggi o gli aggiornamenti gemelli in locale. Quando viene ristabilita la connessione, tutti i dati vengono sincronizzati. Il percorso usato per questa cache temporanea è determinato da una proprietà del modulo gemello dell'hub di IoT Edge. Le dimensioni della cache non sono limitate e aumentano fino a quando il dispositivo ha capacità di archiviazione.Per ulteriori informazioni, consultate [Funzionalità offline.](offline-capabilities.md)

### <a name="module-communication"></a>Comunicazione del modulo

L'hub di IoT Edge facilita la comunicazione tra moduli. L'uso dell'hub di IoT Edge come broker di messaggi consente ai moduli di rimanere indipendenti. Per i moduli è sufficiente specificare gli input su cui accettano i messaggi e gli output su cui scrivono i messaggi. Uno sviluppatore di soluzioni può unire questi input e output in modo che i moduli elaborino i dati nell'ordine specifico di tale soluzione.

![L'hub di IoT Edge facilita la comunicazione tra moduli](./media/iot-edge-runtime/module-endpoints.png)

Per inviare i dati all'hub di IoT Edge, un modulo chiama il metodo SendEventAsync. Il primo argomento specifica su quale output inviare il messaggio. Lo pseudocodice seguente invia un messaggio su **output1:**

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Per ricevere un messaggio, registrare un callback che elabori i messaggi in arrivo in un input specifico. Lo pseudocodice seguente registra la funzione messageProcessor da utilizzare per l'elaborazione di tutti i messaggi ricevuti in **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Per ulteriori informazioni sulla classe ModuleClient e sui relativi metodi di comunicazione, vedere le informazioni di riferimento sulle API per il linguaggio SDK preferito: [C'](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)o [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Lo sviluppatore di soluzioni è responsabile della definizione delle regole che determinano il modo in cui l'hub di IoT Edge passa i messaggi tra i moduli. Le regole di routing vengono definite nel cloud e inviate all'hub IoT Edge nel relativo modulo gemello. La stessa sintassi per le route dell'hub IoT viene usata per definire le route tra i moduli di Azure IoT Edge. Per ulteriori informazioni, consultate [Informazioni su come distribuire moduli e stabilire percorsi in IoT Edge.](module-composition.md)

![Le route tra i moduli passano attraverso l'hub di IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente di IoT Edge

L'agente di IoT Edge è l'altro modulo che costituisce il runtime di Azure IoT Edge. È responsabile della creazione di istanze per i moduli, ne garantisce la continua esecuzione e segnala lo stato dei moduli all'hub IoT. Questi dati di configurazione vengono scritti come proprietà del modulo gemello del modulo dell'agente IoT Edge.

Il [daemon di sicurezza di IoT Edge](iot-edge-security-manager.md) avvia l'agente di IoT Edge all'avvio del dispositivo. L'agente recupera il modulo gemello dall'hub IoT e controlla il manifesto della distribuzione. Il manifesto della distribuzione è un file JSON che dichiara i moduli da avviare.

Ogni elemento nel manifesto della distribuzione contiene informazioni specifiche su un modulo e viene usato dall'agente di IoT Edge per controllare il ciclo di vita del modulo. Di seguito sono riportate alcune delle proprietà più interessanti:

* **settings.image**: l'immagine del contenitore usata dall'agente di IoT Edge per avviare il modulo. Se l'immagine è protetta da password, l'agente di IoT Edge deve essere configurato con le credenziali per il registro contenitori. È possibile configurare in remoto le credenziali per il registro contenitori usando il manifesto della distribuzione o aggiornando il file `config.yaml` nella cartella di programma IoT Edge del dispositivo IoT Edge.
* **settings.createOptions** – Una stringa che viene passata direttamente al daemon contenitore Moby quando si avvia il contenitore di un modulo. L'aggiunta di opzioni in questa proprietà consente configurazioni avanzate come port forwarding o mounting volumes nel contenitore di un modulo.  
* **status**: lo stato in cui l'agente di IoT Edge inserisce il modulo. In genere, questo valore è impostato su *in esecuzione* poiché la maggior parte delle persone desidera che l'agente IoT Edge avvii immediatamente tutti i moduli nel dispositivo. È tuttavia possibile specificare lo stato iniziale di un modulo come stopped e attendere un secondo momento per indicare all'agente di IoT Edge di avviarlo.L'agente di IoT Edge segnala lo stato di ogni modulo al cloud nelle proprietà segnalate. Una differenza tra la proprietà desiderata e la proprietà segnalata è indicativa del comportamento errato di un dispositivo. Gli stati supportati sono:

  * Download in corso
  * In esecuzione
  * Non integro
  * Operazione non riuscita
  * Arrestato

* **restartPolicy**: la modalità in cui l'agente di IoT Edge riavvia un modulo. I valori possibili sono:
  
  * `never`– L'agente IoT Edge non riavvia mai il modulo.
  * `on-failure`- Se il modulo si arresta in modo anomalo, l'agente Edge IoT lo riavvia. Se il modulo si arresta correttamente, l'agente IoT Edge non lo riavvia.
  * `on-unhealthy`- Se il modulo si arresta in modo anomalo o è considerato non integro, l'agente Edge IoT lo riavvia.
  * `always`- Se il modulo si arresta in modo anomalo, è considerato non integro o si arresta in alcun modo, l'agente IoT Edge lo riavvia.

* **imagePullPolicy** - Indica se l'agente IoT Edge tenta di estrarre automaticamente l'immagine più recente per un modulo. Se non si specifica un valore, il valore predefinito è *onCreate*. I valori possibili sono:

  * `on-create`- Quando si avvia un modulo o si aggiorna un modulo in base a un nuovo manifesto di distribuzione, l'agente IoT Edge tenterà di estrarre l'immagine del modulo dal Registro di sistema del contenitore.- When starting a module or updating a module based on a new deployment manifest, the IoT Edge agent will attempt to pull the module image from the container registry.
  * `never`- L'agente IoT Edge non tenterà mai di estrarre l'immagine del modulo dal Registro di sistema del contenitore.- The IoT Edge agent will never attempt to pull the module image from the container registry. Con questa configurazione, allora sei responsabile per ottenere l'immagine del modulo sul dispositivo e la gestione di eventuali aggiornamenti delle immagini.

L'agente di IoT Edge invia la risposta runtime all'hub IoT. Ecco un elenco di risposte possibili:
  
* 200 - OK
* 400 - La configurazione della distribuzione è in formato non corretto o non valida.
* 417 - Il dispositivo non dispone di un set di configurazione di distribuzione.
* 412 - La versione dello schema nella configurazione della distribuzione non è valida.
* 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
* 500 - Si è verificato un errore nel runtime di IoT Edge.

Per ulteriori informazioni, consultate [Informazioni su come distribuire moduli e stabilire percorsi in IoT Edge.](module-composition.md)

### <a name="security"></a>Security

L'agente di IoT Edge svolge un ruolo fondamentale nella protezione di un dispositivo di IoT Edge. Ad esempio, esegue azioni come la verifica di un'immagine del modulo prima di avviarla.

Per altre informazioni sul framework di sicurezza di Azure IoT Edge, vedere [Gestione sicurezza IoT Edge.](iot-edge-security-manager.md)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md)
