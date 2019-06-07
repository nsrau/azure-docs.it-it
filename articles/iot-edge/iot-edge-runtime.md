---
title: Informazioni su come il runtime gestisce i dispositivi - Azure IoT Edge | Microsoft Docs
description: Informazioni su come i moduli, la sicurezza, le comunicazioni e la creazione di report per i dispositivi vengono gestiti dal runtime di Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 423825540c02d2788de7a6148ddcec3c654fd450
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754796"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura

Il runtime di IoT Edge è una raccolta di programmi che trasforma un dispositivo in un dispositivo IoT Edge. I componenti di runtime di IoT Edge collettivamente, consentono ai dispositivi IoT Edge di ricezione del codice da eseguire nei dispositivi perimetrali e comunicano i risultati. 

Nei dispositivi IoT Edge, il runtime di IoT Edge esegue le funzioni seguenti:

* Installare e aggiornare i carichi di lavoro nel dispositivo.
* Mantenere gli standard di sicurezza di Azure IoT Edge sul dispositivo.
* Assicurarsi che [moduli di IoT Edge](iot-edge-modules.md) siano sempre in esecuzione.
* Segnalare l'integrità dei moduli al cloud per il monitoraggio remoto.
* Facilitano la comunicazione tra i dispositivi foglia downstream e i dispositivi IoT Edge.
* Semplificare le comunicazioni tra moduli nel dispositivo IoT Edge.
* Facilitare la comunicazione tra il dispositivo IoT Edge e il cloud.

![Il runtime comunica informazioni dettagliate e l'integrità del modulo all'hub IoT](./media/iot-edge-runtime/Pipeline.png)

Le responsabilità del runtime di IoT Edge rientrano in due categorie: comunicazione e gestione dei moduli. Questi due ruoli vengono eseguiti da due componenti che fanno parte del runtime di IoT Edge. Il *hub di IoT Edge* ha la responsabilità per la comunicazione, mentre il *agente IoT Edge* consente di distribuire e monitorare i moduli. 

Sia l'hub di IoT Edge che l'agente di IoT Edge sono moduli, proprio come gli altri moduli in esecuzione su un dispositivo IoT Edge. 

## <a name="iot-edge-hub"></a>Hub di IoT Edge

L'hub di IoT Edge è uno dei due moduli che costituiscono il runtime di Azure IoT Edge. Opera come un proxy locale per l'hub IoT esponendo gli stessi endpoint del protocollo dell'hub IoT. Questa coerenza implica che i client, siano essi dispositivi o moduli, possono connettersi al runtime di IoT Edge esattamente come si connetterebbero all'hub IoT. 

>[!NOTE]
> Hub di IoT Edge supporta i client che si connettono tramite MQTT o AMQP. Non supporta i client che usano HTTP. 

L'hub di IoT Edge non è una versione completa dell'hub IoT in esecuzione in locale. Esistono alcune operazioni che l'hub di IoT Edge delega automaticamente all'hub IoT. Ad esempio, l'hub di IoT Edge inoltra le richieste di autenticazione all'hub IoT quando un dispositivo prova a connettersi per la prima volta. Dopo aver stabilito la prima connessione, le informazioni di sicurezza vengono memorizzate nella cache locale dall'hub di IoT Edge. Le connessioni successive da tale dispositivo vengono consentite senza dover eseguire l'autenticazione nel cloud. 

Per ridurre la larghezza di banda usata dalla soluzione IoT Edge, l'hub di IoT Edge ottimizza il numero di connessioni effettivamente eseguite nel cloud. L'hub di IoT Edge accetta le connessioni logiche dai client quali moduli o dispositivi foglia e le combina per avere un'unica connessione fisica nel cloud. I dettagli di questo processo sono trasparenti per il resto della soluzione. Ai client sembra di avere ognuno la propria la connessione al cloud ma in realtà usano tutti la stessa connessione. 

![L'hub di IoT Edge è un gateway tra dispositivi fisici e l'hub IoT](./media/iot-edge-runtime/Gateway.png)

L'hub di IoT Edge può determinare se è connesso all'hub IoT. Se la connessione viene persa, l'hub di IoT Edge salva i messaggi o gli aggiornamenti gemelli in locale. Quando viene ristabilita la connessione, tutti i dati vengono sincronizzati. Il percorso usato per questa cache temporanea è determinato da una proprietà del modulo gemello dell'hub di IoT Edge. Le dimensioni della cache non sono limitate e aumentano fino a quando il dispositivo ha capacità di archiviazione. Per altre informazioni, vedere [le capacità Offline](offline-capabilities.md).

### <a name="module-communication"></a>Comunicazione del modulo

L'hub di IoT Edge facilita la comunicazione tra moduli. L'uso dell'hub di IoT Edge come broker di messaggi consente ai moduli di rimanere indipendenti. Per i moduli è sufficiente specificare gli input su cui accettano i messaggi e gli output su cui scrivono i messaggi. Gli sviluppatori di soluzioni possono unire questi input e output in modo che i moduli elaborino i dati nell'ordine specifico per tale soluzione. 

![L'hub di IoT Edge facilita la comunicazione tra moduli](./media/iot-edge-runtime/module-endpoints.png)

Per inviare i dati all'hub di IoT Edge, un modulo chiama il metodo SendEventAsync. Il primo argomento specifica su quale output inviare il messaggio. Lo pseudocodice seguente invia un messaggio sul **output1**:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Per ricevere un messaggio, registrare un callback che elabori i messaggi in arrivo in un input specifico. Lo pseudocodice seguente registra la funzione messageProcessor da usare per l'elaborazione di tutti i messaggi ricevuti in **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Per altre informazioni sulla classe ModuleClient e i relativi metodi di comunicazione, vedere le informazioni si riferimento sull'API per il linguaggio preferito per l'SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C e Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) o [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Lo sviluppatore di soluzioni è responsabile della definizione delle regole che determinano il modo in cui l'hub di IoT Edge passa i messaggi tra i moduli. Le regole di routing sono definite nel cloud e propagate all'hub di IoT Edge nel dispositivo gemello. La stessa sintassi per le route dell'hub IoT viene usata per definire le route tra i moduli di Azure IoT Edge. Per altre informazioni, vedere [informazioni su come distribuire i moduli e stabilire le route in IoT Edge](module-composition.md).   

![Le route tra i moduli passano attraverso l'hub di IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente di IoT Edge

L'agente di IoT Edge è l'altro modulo che costituisce il runtime di Azure IoT Edge. È responsabile della creazione di istanze per i moduli, ne garantisce la continua esecuzione e segnala lo stato dei moduli all'hub IoT. Analogamente a qualsiasi altro modulo, l'agente di IoT Edge usa il proprio modulo gemello per archiviare questi dati di configurazione. 

Il [daemon di sicurezza di IoT Edge](iot-edge-security-manager.md) avvia l'agente di IoT Edge all'avvio del dispositivo. L'agente recupera il modulo gemello dall'hub IoT e controlla il manifesto della distribuzione. Il manifesto della distribuzione è un file JSON che dichiara i moduli da avviare. 

Ogni elemento nel manifesto della distribuzione contiene informazioni specifiche su un modulo e viene usato dall'agente di IoT Edge per controllare il ciclo di vita del modulo. Di seguito sono riportate alcune delle proprietà più interessanti: 

* **settings.image**: l'immagine del contenitore usata dall'agente di IoT Edge per avviare il modulo. Se l'immagine è protetta da password, l'agente di IoT Edge deve essere configurato con le credenziali per il registro contenitori. È possibile configurare in remoto le credenziali per il registro contenitori usando il manifesto della distribuzione o aggiornando il file `config.yaml` nella cartella di programma IoT Edge del dispositivo IoT Edge.
* **createOptions** : una stringa che viene passata direttamente al daemon Moby contenitore quando si avvia il contenitore di un modulo. L'aggiunta di opzioni in questa proprietà consente configurazioni avanzate, ad esempio porte di inoltro o il montaggio di volumi nel contenitore di un modulo.  
* **status**: lo stato in cui l'agente di IoT Edge inserisce il modulo. In genere, questo valore è impostato su *in esecuzione* come la maggior parte degli utenti desidera che l'agente di IoT Edge per iniziare immediatamente a tutti i moduli nel dispositivo. È tuttavia possibile specificare lo stato iniziale di un modulo come stopped e attendere un secondo momento per indicare all'agente di IoT Edge di avviarlo. L'agente di IoT Edge segnala lo stato di ogni modulo al cloud nelle proprietà segnalate. Una differenza tra la proprietà desiderata e la proprietà segnalata è indicativa del comportamento errato di un dispositivo. Gli stati supportati sono:
   * Download in corso
   * In esecuzione
   * Non integro
   * Failed
   * Arrestato
* **restartPolicy**: la modalità in cui l'agente di IoT Edge riavvia un modulo. I valori possibili sono:
   * `never` : L'agente di IoT Edge non riavvia mai il modulo.
   * `on-failure` -Se il modulo si blocca, l'agente di IoT Edge lo riavvia. Se il modulo viene chiuso correttamente, l'agente di IoT Edge non lo riavvia.
   * `on-unhealthy` -Se il modulo si blocca o viene considerato non integro, l'agente di IoT Edge lo riavvia.
   * `always` -Se il modulo si blocca, viene considerato non integro o si arresta in alcun modo, l'agente di IoT Edge lo riavvia. 

L'agente di IoT Edge invia la risposta runtime all'hub IoT. Ecco un elenco di risposte possibili:
  * 200 - OK
  * 400 - La configurazione della distribuzione è in formato non corretto o non valida.
  * 417 - il dispositivo non ha una configurazione di distribuzione impostato.
  * 412 - La versione dello schema nella configurazione della distribuzione non è valida.
  * 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
  * 500 - Si è verificato un errore nel runtime di IoT Edge.

Per altre informazioni, vedere [informazioni su come distribuire i moduli e stabilire le route in IoT Edge](module-composition.md).   

### <a name="security"></a>Security

L'agente di IoT Edge svolge un ruolo fondamentale nella protezione di un dispositivo di IoT Edge. Ad esempio, esegue azioni come la verifica di un'immagine del modulo prima di avviarla. 

Per altre informazioni sul framework di sicurezza di Azure IoT Edge, vedere la [manager di sicurezza IoT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md)
