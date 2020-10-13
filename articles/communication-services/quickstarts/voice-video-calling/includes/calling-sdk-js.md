---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: eaa7efe761490a639acabd9fd6d91378e1259a67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779191"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Oggetto `User Access Token` per abilitare il client di chiamata. Per ulteriori informazioni su [come ottenere un `User Access Token` ](../../access-tokens.md)
- Facoltativo: completare la Guida introduttiva per [iniziare ad aggiungere una chiamata all'applicazione](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Usare il `npm install` comando per installare i servizi di comunicazione di Azure che chiamano e le librerie client comuni per JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client Chiamate di Servizi di comunicazione di Azure:

| Nome                             | Descrizione                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient è il principale punto di ingresso alla libreria client Chiamate.                                                                       |
| CallAgent                        | CallAgent si usa per avviare e gestire le chiamate.                                                                                            |
| AzureCommunicationUserCredential | La classe AzureCommunicationUserCredential implementa l'interfaccia CommunicationUserCredential usata per creare un'istanza di CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Inizializzare CallClient, creare CallAgent e accedere a DeviceManager

Creare un'istanza di una nuova `CallClient` istanza. È possibile configurarla con opzioni personalizzate come un'istanza del logger.
Una volta `CallClient` creata un'istanza di, è possibile creare un' `CallAgent` istanza chiamando il `createCallAgent` metodo sull' `CallClient` istanza. Restituisce in modo asincrono un `CallAgent` oggetto istanza.
Il `createCallAgent` metodo accetta `CommunicationUserCredential` come argomento, che accetta un token di [accesso utente](../../access-tokens.md).
Per accedere a `DeviceManager` un'istanza di callAgent, è necessario creare prima di tutto. È quindi possibile usare il `getDeviceManager` metodo nell' `CallClient` istanza per ottenere devicemanager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationUserCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential);
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Inserire una chiamata in uscita

Per creare e avviare una chiamata, è necessario usare una delle API in CallAgent e fornire un utente creato tramite la libreria client di amministrazione dei servizi di comunicazione.

La creazione e l'avvio di una chiamata sono sincrone. L'istanza di chiamata consente di sottoscrivere gli eventi di chiamata.

## <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Inserire una chiamata 1:1 a un utente o una chiamata 1: n con gli utenti e la rete PSTN

Per effettuare una chiamata a un altro utente di servizi di comunicazione, richiamare il `call` metodo su `callAgent` e passare la CommunicationUser [creata con la libreria di amministrazione dei servizi di comunicazione](../../access-tokens.md).

```js
const oneToOneCall = callAgent.call([CommunicationUser]);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Inserire una chiamata 1: n con gli utenti e la rete PSTN

Per effettuare una chiamata 1: n a un utente e a un numero PSTN, è necessario specificare un CommunicationUser e un numero di telefono per entrambi i chiamanti.

La risorsa di servizi di comunicazione deve essere configurata in modo da consentire la chiamata PSTN.
```js

const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const groupCall = callAgent.call([userCallee, pstnCallee], placeCallOptions);

```

### <a name="place-a-11-call-with-video-camera"></a>Inserire una chiamata 1:1 con la videocamera video
> [!WARNING]
> Attualmente non è possibile avere più di un flusso video locale in uscita.
Per effettuare una chiamata video, è necessario enumerare le telecamere locali usando l' `getCameraList` API deviceManager.
Dopo aver selezionato la fotocamera desiderata, usarla per creare un' `LocalVideoStream` istanza e passarla `videoOptions` come elemento all'interno della `localVideoStream` matrice al `call` metodo.
Una volta che la chiamata si connette, inizierà automaticamente a inviare un flusso video dalla fotocamera selezionata agli altri partecipanti
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Partecipare a una chiamata di gruppo
Per avviare una nuova chiamata di gruppo o partecipare a una chiamata di gruppo in corso, usare il metodo ' Join ' e passare un oggetto con una `groupId` Proprietà. Il valore deve essere un GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

## <a name="call-management"></a>Gestione chiamate

È possibile accedere alle proprietà delle chiamate ed eseguire varie operazioni durante una chiamata per gestire le impostazioni relative a video e audio.

### <a name="call-properties"></a>Proprietà della chiamata
* Ottiene l'ID univoco (stringa) per questa chiamata.
```js

const callId: string = call.id;

```

* Per informazioni sugli altri partecipanti alla chiamata, controllare la `remoteParticipant` raccolta nell'istanza di `call` . Matrice contenente `RemoteParticipant` oggetti elenco
```js
const remoteParticipants = call.remoteParticipants;
```

* Identità del chiamante se la chiamata è in arrivo. Identity è uno dei `Identifier` tipi
```js

const callerIdentity = call.callerIdentity;

```

* Ottiene lo stato della chiamata.
```js

const callState = call.state;

```
Restituisce una stringa che rappresenta lo stato corrente di una chiamata:
* ' None '-stato di chiamata iniziale
* ' In entrata ': indica che una chiamata è in ingresso, deve essere accettata o rifiutata
* ' Connecting '-stato di transizione iniziale dopo aver inserito o accettato la chiamata
* ' Squillo '-per una chiamata in uscita: indica che la chiamata è squillante per i partecipanti remoti, ma è "in ingresso" sul lato
* ' EarlyMedia ': indica uno stato in cui viene riprodotto un annuncio prima della connessione della chiamata
* ' Connected ': la chiamata è connessa
* ' Trattieni ': la chiamata viene messa in attesa, nessun contenuto multimediale viene propagato tra l'endpoint locale e i partecipanti remoti
* ' Disconnecting '-stato di transizione prima della chiamata allo stato ' disconnected '
* ' Disconnected '-stato finale della chiamata


* Per individuare il motivo per cui una determinata chiamata è terminata, controllare la `callEndReason` Proprietà.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Per sapere se la chiamata corrente è una chiamata in ingresso, controllare la `isIncoming` proprietà, che restituisce `Boolean` .
```js
const isIncoming = call.isIncoming;
```

*  Per verificare se il microfono corrente è disattivato, controllare la `muted` proprietà, che restituisce `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Per verificare se il flusso di condivisione dello schermo viene inviato da un determinato endpoint, controllare la `isScreenSharingOn` proprietà, restituisce `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Per esaminare i flussi video attivi, controllare la `localVideoStreams` raccolta, che contiene `LocalVideoStream` gli oggetti
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="mute-and-unmute"></a>Mute e Unmute

Per disattivare o disattivare l'endpoint locale è possibile usare le `mute` `unmute` API asincrone e:

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Avviare e arrestare l'invio del video locale


Per avviare un video, è necessario enumerare le fotocamere usando il `getCameraList` metodo nell' `deviceManager` oggetto. Creare quindi una nuova istanza di `LocalVideoStream` passando la fotocamera desiderata al `startVideo` metodo come argomento:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Una volta avviato correttamente l'invio del video, un' `LocalVideoStream` istanza di verrà aggiunta alla `localVideoStreams` raccolta in un'istanza di chiamata.

```js

call.localVideoStreams[0] === localVideoStream;

```

Per arrestare il video locale, passare l' `localVideoStream` istanza disponibile nella `localVideoStreams` raccolta:

```js

await call.stopVideo(localVideoStream);

```

È possibile passare a un dispositivo della fotocamera diverso mentre è in corso l'invio del video richiamando `switchSource` in un' `localVideoStream` istanza:

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>Gestione dei partecipanti remoti

Tutti i partecipanti remoti sono rappresentati dal `RemoteParticipant` tipo e disponibili tramite `remoteParticipants` la raccolta in un'istanza di chiamata.

### <a name="list-participants-in-a-call"></a>Elenca i partecipanti a una chiamata
La `remoteParticipants` raccolta restituisce un elenco di partecipanti remoti nella chiamata specificata:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Proprietà del partecipante remoto
Al partecipante remoto è associato un set di proprietà e raccolte

* Ottiene l'identificatore per questo partecipante remoto.
Identity è uno dei tipi ' Identifier ':
```js
const identifier = remoteParticipant.identifier;
//It can be one of:
// { communicationUserId: '<ACS_USER_ID'> } - object representing ACS User
// { phoneNumber: '<E.164>' } - object representing phone number in E.164 format
```

* Ottiene lo stato del partecipante remoto.
```js

const state = remoteParticipant.state;
```
Lo stato può essere uno dei
* ' Idle '-stato iniziale
* ' Connecting '-stato di transizione durante la connessione del partecipante alla chiamata
* ' Connected ': il partecipante è connesso alla chiamata
* ' Trattieni ': il partecipante è in attesa
* ' EarlyMedia '-l'annuncio viene riprodotto prima della connessione del partecipante alla chiamata
* ' Disconnected '-stato finale-partecipante disconnesso dalla chiamata

Per informazioni sul motivo per cui il partecipante ha lasciato la chiamata, controllare la `callEndReason` proprietà:
```js

const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Per verificare se il partecipante remoto è disattivato o meno, controllare la `isMuted` proprietà, restituisce `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```

* Per verificare se il partecipante remoto sta parlando o meno, controllare la `isSpeaking` proprietà restituita `Boolean`
```js

const isSpeaking = remoteParticipant.isSpeaking;

```

* Per controllare tutti i flussi video che un determinato partecipante sta inviando in questa chiamata, controllare la `videoStreams` raccolta, contiene `RemoteVideoStream` oggetti
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Aggiungere un partecipante a una chiamata

Per aggiungere un partecipante a una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare `addParticipant` .
Specificare uno dei tipi "Identifier".
In questo modo verrà restituita in modo sincrono l'istanza del partecipante remoto.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier);
```

### <a name="remove-participant-from-a-call"></a>Rimuovi partecipante da una chiamata

Per rimuovere un partecipante da una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare `removeParticipant` .
È necessario passare uno dei tipi ' Identifier ', che verrà risolto in modo asincrono dopo la rimozione del partecipante dalla chiamata.
Il partecipante verrà rimosso anche dalla `remoteParticipants` raccolta.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Rendering dei flussi video del partecipante remoto

Per elencare i flussi video e i flussi di condivisione dello schermo dei partecipanti remoti, esaminare le `videoStreams` Raccolte:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
Per eseguire il rendering di un `RemoteVideoStream` , è necessario sottoscrivere un `isAvailableChanged` evento.
Se la `isAvailable` proprietà viene modificata in `true` , un partecipante remoto sta inviando un flusso.
Una volta eseguita questa operazione, creare una nuova istanza di `Renderer` e quindi creare una nuova `RendererView` istanza di utilizzando il `createView` metodo asincrono.  È quindi possibile connettersi `view.target` a qualsiasi elemento dell'interfaccia utente.
Ogni volta che la disponibilità di un flusso remoto cambia è possibile scegliere di eliminare definitivamente l'intero renderer, un oggetto specifico `RendererView` o mantenerlo, ma ciò comporterà la visualizzazione di un frame video vuoto.

```js
let renderer: Renderer;
const displayVideo = () => {
    renderer = new Renderer(remoteParticipantStream);
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Proprietà del flusso video remoto
I flussi video remoti hanno le proprietà seguenti:

* `Id` -ID di un flusso video remoto
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -dimensione (larghezza/altezza) di un flusso video remoto
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -può essere ' video ' o ' ScreenSharing '
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -Indica se l'endpoint del partecipante remoto sta inviando un flusso in modo attivo
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Metodi e proprietà del renderer

* Creare un' `RendererView` istanza che può essere collegata successivamente nell'interfaccia utente dell'applicazione per eseguire il rendering del flusso video remoto.
```js
renderer.createView()
```

* Eliminare il renderer e tutte le `RendererView` istanze associate.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Metodi e proprietà di RendererView
Quando si crea un oggetto `RendererView` , è possibile specificare `scalingMode` le `mirrored` proprietà e.
La modalità di ridimensionamento può essere ' Stretch ',' crop ' o ' Fit ' se `Mirrored` è specificato, il flusso di cui è stato eseguito il rendering viene capovolto verticalmente.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Una determinata `RendererView` istanza ha una `target` proprietà che rappresenta la superficie di rendering. Questo deve essere collegato nell'interfaccia utente dell'applicazione:
```js
document.body.appendChild(rendererView.target);
```

In un secondo momento è possibile aggiornare la modalità di ridimensionamento richiamando il `updateScalingMode` metodo.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gestione dei dispositivi

`DeviceManager` consente di enumerare i dispositivi locali che possono essere usati in una chiamata per trasmettere i flussi audio/video. Consente inoltre di richiedere all'utente l'autorizzazione per l'accesso al microfono e alla fotocamera mediante l'API del browser nativo.

È possibile accedere al chiamando il `deviceManager` `callClient.getDeviceManager()` metodo.
> [!WARNING]
> Attualmente `callAgent` è necessario creare un'istanza di un oggetto per ottenere l'accesso a devicemanager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Enumerare i dispositivi locali

Per accedere ai dispositivi locali, è possibile usare i metodi di enumerazione sul Device Manager. L'enumerazione è un'azione sincrona.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Imposta microfono/altoparlante predefinito

Gestione dispositivi consente di impostare un dispositivo predefinito che verrà usato all'avvio di una chiamata.
Se le impostazioni predefinite del client non sono impostate, i servizi di comunicazione eseguiranno il fallback alle impostazioni predefinite del sistema operativo.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceMicrophone.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Anteprima della fotocamera locale

È possibile usare `DeviceManager` e `Renderer` per avviare il rendering dei flussi dalla fotocamera locale. Questo flusso non verrà inviato ad altri partecipanti; si tratta di un feed di anteprima locale. Si tratta di un'azione asincrona.

```js
const localVideoDevice = deviceManager().getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Richiedi autorizzazione a fotocamera/microfono

Chiedere all'utente di concedere le autorizzazioni per la fotocamera/microfono con quanto segue:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Questa operazione verrà risolta in modo asincrono con un oggetto che indica se `audio` `video` sono state concesse le autorizzazioni e:
```js
console.log(result.audio);
console.log(result.video);
```

È possibile controllare lo stato di autorizzazione corrente per un tipo specificato chiamando `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="eventing-model"></a>Modello di eventi

È possibile sottoscrivere la maggior parte delle proprietà e delle raccolte per ricevere una notifica quando cambiano i valori.

### <a name="properties"></a>Proprietà
Per sottoscrivere `property changed` gli eventi:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Raccolte
Per sottoscrivere `collection updated` gli eventi:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
