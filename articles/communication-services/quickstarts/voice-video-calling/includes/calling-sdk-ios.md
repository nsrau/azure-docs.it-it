---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: a9af249aac18c847bf353f22b23ee67ab6e264c4
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915261"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un `User Access Token` per abilitare il client di chiamata. Per altre informazioni, vedere [come ottenere un `User Access Token`](../../access-tokens.md)
- Facoltativo: completare la Guida introduttiva per [iniziare ad aggiungere una chiamata all'applicazione](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurazione

### <a name="creating-the-xcode-project"></a>Creazione del progetto Xcode

In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application**. Questa Guida introduttiva usa il [Framework SwiftUI](https://developer.apple.com/xcode/swiftui/), quindi è necessario impostare la **lingua** su **Swift** e l' **interfaccia utente** su **SwiftUI**. Non verranno creati unit test o test dell'interfaccia utente durante questa Guida introduttiva. È possibile deselezionare Includi **unit test** e deselezionare **Includi test dell'interfaccia utente**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot che mostra la finestra per la creazione di un nuovo progetto in Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installare il pacchetto e le dipendenze con CocoaPods

1. Creare un Podfile per l'applicazione, come segue:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.5'
     pod 'AzureCommunication', '~> 1.0.0-beta.5'
     pod 'AzureCore', '~> 1.0.0-beta.5'
   end
   ```

2. Eseguire `pod install`.
3. Aprire `.xcworkspace` con Xcode.

### <a name="request-access-to-the-microphone"></a>Richiedere l'accesso al microfono

Per accedere al microfono del dispositivo, è necessario aggiornare il file Information Property List dell'app con `NSMicrophoneUsageDescription`. Impostare il valore associato su un oggetto `string` che verrà incluso nella finestra di dialogo usata dal sistema per richiedere l'accesso dell'utente.

Fare clic con il pulsante destro del mouse sulla voce `Info.plist` dell'albero del progetto e scegliere **Open As** > **Source Code** (Apri come > Codice sorgente). Aggiungere le righe seguenti nella sezione `<dict>` di primo livello e quindi salvare il file.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Aprire il file **ContentView.swift** del progetto e aggiungere una dichiarazione `import` all'inizio del file per importare `AzureCommunicationCalling library`. Inoltre, l'importazione `AVFoundation` sarà necessaria per la richiesta di autorizzazione audio nel codice.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dei servizi di comunicazione di Azure che chiamano la libreria client per iOS.


| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | ACSCallClient è il punto di ingresso principale della libreria client chiamante.|
| ACSCallAgent | ACSCallAgent viene utilizzato per avviare e gestire le chiamate. |
| CommunicationUserCredential | CommunicationUserCredential si usa come credenziale del token per creare un'istanza di CallAgent.| 
| CommunicationIndentifier | CommunicationIndentifier si usa per rappresentare l'identità dell'utente, che può essere una delle seguenti: CommunicationUser/PhoneNumber/CallingApplication. |

> [!NOTE]
> Quando si implementano delegati di eventi, l'applicazione deve mantenere un riferimento sicuro agli oggetti che richiedono sottoscrizioni di eventi. Ad esempio, quando un `ACSRemoteParticipant` oggetto viene restituito quando si richiama il `call.addParticipant` metodo e l'applicazione imposta il delegato per l'ascolto `ACSRemoteParticipantDelegate` , l'applicazione deve mantenere un riferimento sicuro all' `ACSRemoteParticipant` oggetto. In caso contrario, se l'oggetto viene raccolto, il delegato genererà un'eccezione irreversibile quando l'SDK chiamante tenterà di richiamare l'oggetto.

## <a name="initialize-the-acscallagent"></a>Inizializzare ACSCallAgent

Per creare un' `ACSCallAgent` istanza di da `ACSCallClient` , è necessario usare il `callClient.createCallAgent` metodo che restituisce in modo asincrono un `ACSCallAgent` oggetto dopo che è stato inizializzato

Per creare un client di chiamata, è necessario passare un `CommunicationUserCredential` oggetto.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Passa l'oggetto CommunicationUserCredential creato in precedenza a ACSCallClient

```swift

callClient = CallClient()
callClient?.createCallAgent(with: userCredential!,
    completionHandler: { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Inserire una chiamata in uscita

Per creare e avviare una chiamata, è necessario chiamare una delle API in `ACSCallAgent` e fornire l'identità dei servizi di comunicazione di un utente di cui è stato effettuato il provisioning tramite la libreria client di gestione dei servizi di comunicazione.

La creazione e l'avvio di una chiamata sono sincrone. Si riceverà un'istanza di chiamata che consente di sottoscrivere tutti gli eventi della chiamata.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Inserire una chiamata 1:1 a un utente o una chiamata 1: n con gli utenti e la rete PSTN

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Inserire una chiamata 1: n con gli utenti e la rete PSTN
Per inserire la chiamata a PSTN è necessario specificare il numero di telefono acquisito con servizi di comunicazione
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Inserire una chiamata 1:1 con con video
Per ottenere un'istanza di gestione dispositivi, vedere [qui](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Partecipare a una chiamata di gruppo
Per partecipare a una chiamata è necessario chiamare una delle API in *CallAgent*

```swift

let groupCallContext = GroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: JoinCallOptions())

```

## <a name="push-notification"></a>Notifica push

La notifica push Mobile è la notifica popup che si ottiene nel dispositivo mobile. Per chiamare, verranno concentrate le notifiche push VoIP (Voice over Internet Protocol). Verranno offerte le funzionalità per la registrazione per la notifica push, per la gestione delle notifiche push e per l'annullamento della registrazione della notifica push.

### <a name="prerequisite"></a>Prerequisito

- Passaggio 1: Xcode-> firma & funzionalità-> aggiungere funzionalità-> "notifiche push"
- Passaggio 2: Xcode-> firma & funzionalità-> aggiungere funzionalità-> "modalità in background"
- Passaggio 3: "modalità in background"-> selezionare "Voice over IP" e "Remote Notifications"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Screenshot che illustra come aggiungere funzionalità in Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registrarsi per le notifiche push

Per eseguire la registrazione per la notifica push, chiamare registerPushNotification () in un'istanza di *CallAgent* con un token di registrazione del dispositivo.

La registrazione per la notifica push deve essere chiamata dopo l'inizializzazione riuscita. Quando l' `callAgent` oggetto viene eliminato definitivamente, `logout` viene chiamato il metodo che consente di annullare automaticamente la registrazione delle notifiche push.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken,
                completionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Gestione delle notifiche push
Per ricevere le notifiche push delle chiamate in ingresso, chiamare *handlePushNotification ()* su un'istanza di *CallAgent* con un payload del dizionario.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(payload: dictionaryPayload, completionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Annulla registrazione notifiche push

Le applicazioni possono annullare la registrazione della notifica push in qualsiasi momento. È sufficiente chiamare il `unRegisterPushNotification` metodo in *CallAgent*.
> [!NOTE]
> Le applicazioni non vengono annullate automaticamente dalla notifica push durante la disconnessione.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Operazioni di chiamata intermedia

È possibile eseguire diverse operazioni durante una chiamata per gestire le impostazioni relative a video e audio.

### <a name="mute-and-unmute"></a>Mute e Unmute

Per disattivare o disattivare l'endpoint locale è possibile usare le `mute` `unmute` API asincrone e:

```swift
call!.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

Asincrona Riattivazione locale

```swift
call!.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Avviare e arrestare l'invio del video locale

Per iniziare a inviare il video locale ad altri partecipanti nella chiamata, usare l' `startVideo` API e passare `localVideoStream` con `camera`

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

Una volta avviato l'invio di video, l' `ACSLocalVideoStream` istanza di viene aggiunta alla `localVideoStreams` raccolta in un'istanza di chiamata:

```swift

call.localVideoStreams[0]

```

Asincrona Per arrestare il video locale, passare l'oggetto `localVideoStream` restituito dalla chiamata di `call.startVideo` :

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Gestione dei partecipanti remoti

Tutti i partecipanti remoti sono rappresentati dal `ACSRemoteParticipant` tipo e sono disponibili tramite la `remoteParticipants` raccolta in un'istanza di chiamata:

### <a name="list-participants-in-a-call"></a>Elenca i partecipanti a una chiamata

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Proprietà del partecipante remoto

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Aggiungere un partecipante a una chiamata

Per aggiungere un partecipante a una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare `addParticipant` . In questo modo verrà restituita in modo sincrono un'istanza del partecipante remoto.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Rimuovere un partecipante da una chiamata
Per rimuovere un partecipante da una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare l'  `removeParticipant` API. Questa operazione verrà risolta in modo asincrono.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Rendering dei flussi video del partecipante remoto

I partecipanti remoti possono avviare la condivisione video o schermo durante una chiamata.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Gestire i flussi video e condivisione schermo del partecipante remoto

Per elencare i flussi di partecipanti remoti, esaminare le `videoStreams` Raccolte:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Proprietà del flusso video remoto

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Render flusso del partecipante remoto

Per avviare il rendering di flussi del partecipante remoto:

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Metodi e proprietà del renderer video remoto

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Gestione dei dispositivi

`DeviceManager` consente di enumerare i dispositivi locali che possono essere usati in una chiamata per trasmettere flussi audio/video. Consente inoltre di richiedere l'autorizzazione da un utente per accedere al microfono/fotocamera. È possibile accedere `deviceManager` all' `callClient` oggetto:

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Enumerare i dispositivi locali

Per accedere ai dispositivi locali, è possibile usare i metodi di enumerazione sul Device Manager. L'enumerazione è un'azione sincrona.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Imposta microfono/altoparlante predefinito

Gestione dispositivi consente di impostare un dispositivo predefinito che verrà usato all'avvio di una chiamata. Se le impostazioni predefinite dello stack non sono impostate, i servizi di comunicazione eseguiranno il fallback alle impostazioni predefinite del sistema operativo.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>Anteprima della fotocamera locale

È possibile usare `ACSRenderer` per avviare il rendering di un flusso dalla fotocamera locale. Questo flusso non verrà inviato ad altri partecipanti; si tratta di un feed di anteprima locale. Si tratta di un'azione asincrona.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Proprietà di anteprima della fotocamera locale

Il renderer dispone di un set di proprietà e metodi che consentono di controllare il rendering:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Modello di eventi

È possibile sottoscrivere la maggior parte delle proprietà e delle raccolte per ricevere una notifica quando cambiano i valori.

### <a name="properties"></a>Proprietà
Per sottoscrivere `property changed` gli eventi:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Raccolte
Per sottoscrivere `collection updated` gli eventi:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
