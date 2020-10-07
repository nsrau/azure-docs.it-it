---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: bed2a4ccbe87aef9afa395ed789da393e885cc89
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779824"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Oggetto `User Access Token` per abilitare il client di chiamata. Per ulteriori informazioni su [come ottenere un `User Access Token` ](../../access-tokens.md)
- Facoltativo: completare la Guida introduttiva per [iniziare ad aggiungere una chiamata all'applicazione](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurazione

### <a name="install-the-package"></a>Installare il pacchetto

<!-- TODO: update with instructions on how to download, install and add package to project -->
Individuare build.gradle a livello di progetto e assicurarsi di aggiungere `mavenCentral()` all'elenco dei repository in `buildscript` e `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Quindi, a livello di modulo Build. Gradle aggiungere le righe seguenti alla sezione dipendenze

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.2'
    ...
}

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client Chiamate di Servizi di comunicazione di Azure:

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient è il principale punto di ingresso alla libreria client Chiamate.|
| CallAgent | CallAgent si usa per avviare e gestire le chiamate. |
| CommunicationUserCredential | CommunicationUserCredential si usa come credenziale del token per creare un'istanza di CallAgent.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Inizializzare CallClient, creare un CallAgent e accedere a DeviceManager

Per creare un' `CallAgent` istanza è necessario chiamare il `createCallAgent` metodo su un' `CallClient` istanza di. Restituisce in modo asincrono un `CallAgent` oggetto istanza.
Il `createCallAgent` metodo accetta `CommunicationUserCredential` come argomento che incapsula un [token di accesso](../../access-tokens.md).
Per accedere a `DeviceManager` , è prima necessario creare un'istanza di callAgent, quindi è possibile usare il `CallClient.getDeviceManager` metodo per ottenere il devicemanager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```

## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Inserire una chiamata in uscita e partecipare a una chiamata di gruppo

Per creare e avviare una chiamata, è necessario chiamare il `CallAgent.call()` metodo e fornire l'oggetto `Identifier` del chiamato (s).
Per aggiungere una chiamata a un gruppo, è necessario chiamare il `CallAgent.join()` metodo e specificare GroupID. Gli ID gruppo devono essere in formato GUID o UUID.

La creazione e l'avvio di una chiamata sono sincrone. L'istanza di chiamata consente di sottoscrivere tutti gli eventi della chiamata.

### <a name="place-a-11-call-to-a-user"></a>Inserire una chiamata 1:1 a un utente
Per effettuare una chiamata a un altro utente di servizi di comunicazione, richiamare il `call` metodo su `callAgent` e passare un oggetto con la `communicationUserId` chiave.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUser acsUserId = new CommunicationUser(<USER_ID>);
CommunicationUser participants[] = new CommunicationUser[]{ acsUserId };
call oneToOneCall = callAgent.call(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Inserire una chiamata 1: n con gli utenti e la rete PSTN
> [!WARNING]
> Attualmente la chiamata PSTN non è disponibile per inserire una chiamata 1: n a un utente e un numero PSTN è necessario specificare il numero di telefono del chiamato.
La risorsa servizi di comunicazione deve essere configurata in modo da consentire la chiamata PSTN:
```java
CommunicationUser acsUser1 = new CommunicationUser(<USER_ID>);
PhoneNumber acsUser2 = new PhoneNumber("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.call(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Inserire una chiamata 1:1 con la videocamera video
> [!WARNING]
> Attualmente è supportato un solo flusso video locale in uscita per effettuare una chiamata con video. è necessario enumerare le telecamere locali usando l' `deviceManager` `getCameraList` API.
Dopo aver selezionato una fotocamera desiderata, usarla per creare un' `LocalVideoStream` istanza e passarla `videoOptions` come elemento della `localVideoStream` matrice a un `call` metodo.
Una volta che la chiamata si connette, inizierà automaticamente a inviare un flusso video dalla fotocamera selezionata ad altri partecipanti.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

CommunicationUser[] participants = new CommunicationUser[]{ new CommunicationUser("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.call(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Partecipare a una chiamata di gruppo
Per avviare una nuova chiamata di gruppo o partecipare a una chiamata di gruppo in corso è necessario chiamare il metodo ' Join ' e passare un oggetto con una `groupId` Proprietà. Il valore deve essere un GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallContext groupCallContext = new groupCallContext("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallContext, joinCallOptions);
```

## <a name="push-notifications"></a>Notifiche push

### <a name="overview"></a>Panoramica
Le notifiche push Mobile sono le notifiche popup visualizzate nei dispositivi mobili. Per chiamare, le notifiche push di VoIP (Voice over Internet Protocol) verranno concentrate su VoIP. Si registreranno le notifiche push, si gestiranno le notifiche push e quindi si annullano le notifiche push.

### <a name="prerequisites"></a>Prerequisiti

Per completare questa sezione, creare un account Firebase e abilitare il Cloud Messaging (FCM). Assicurarsi che la messaggistica cloud di Firebase sia connessa a un'istanza di hub di notifica di Azure (ANH). Per istruzioni, vedere [connettere Firebase ad Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started) .
Questa sezione presuppone inoltre che si stia usando Android Studio versione 3,6 o successiva per compilare l'applicazione.

Un set di autorizzazioni è necessario per l'applicazione Android per poter ricevere messaggi di notifica da Firebase Cloud Messaging. Nel `AndroidManifest.xml` file aggiungere il set di autorizzazioni seguente subito dopo il *<manifesto... >* o sotto il *</application>* tag

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registrarsi per le notifiche push

Per eseguire la registrazione per le notifiche push, l'applicazione deve chiamare `registerPushNotification()` su un'istanza di *CallAgent* con un token di registrazione del dispositivo.

Per ottenere il token di registrazione del dispositivo, aggiungere la libreria client Firebase al file *Build. Gradle* del modulo dell'applicazione aggiungendo le righe seguenti nella `dependencies` sezione, se non è già presente:

```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

Nel file *Build. Gradle* del livello di progetto aggiungere quanto segue nella sezione, `dependencies` se non è già presente:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Aggiungere il plug-in seguente all'inizio del file, se non è già presente:

```
apply plugin: 'com.google.gms.google-services'
```

Selezionare *Sincronizza ora* nella barra degli strumenti. Aggiungere il frammento di codice seguente per ottenere il token di registrazione del dispositivo generato dalla libreria client di messaggistica cloud Firebase per l'istanza dell'applicazione client, assicurarsi di aggiungere le importazioni seguenti all'intestazione dell'attività principale per l'istanza. È necessario che il frammento recuperi il token:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Aggiungere questo frammento di codice per recuperare il token:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w(TAG, "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d(TAG, "Device Registration token retrieved successfully");
                    }
                });
```
Registrare il token di registrazione del dispositivo con la libreria client dei servizi chiamante per le notifiche push delle chiamate in ingresso:

```java
String deviceRegistrationToken = "some_token";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Gestione delle notifiche push

Per ricevere le notifiche push delle chiamate in ingresso, chiamare *handlePushNotification ()* su un'istanza di *CallAgent* con un payload.

Per ottenere il payload da Firebase Cloud Messaging, iniziare creando un nuovo servizio (file > nuovo servizio > servizio >) che estende la classe della libreria client Firebase *FirebaseMessagingService* ed eseguire l'override del `onMessageReceived` metodo. Questo metodo è il gestore eventi chiamato quando Firebase Cloud Messaging recapita la notifica push all'applicazione.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageData;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageData = serializeDictionaryAsJson(remoteMessage.getData());
        }
    }
}
```
Aggiungere la definizione del servizio seguente al `AndroidManifest.xml` file, all'interno del <application> Tag:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

Una volta recuperato, il payload può essere passato alla libreria client dei servizi di comunicazione per essere gestito chiamando il `handlePushNotification` metodo su un' `CallAgent` istanza.

```java
java.util.Map<String, String> pushNotificationMessageDataFromFCM = remoteMessage.getData();
try {
    callAgent.handlePushNotification(pushNotificationMessageDataFromFCM).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Quando la gestione del messaggio di notifica push ha esito positivo e i gestori di tutti gli eventi vengono registrati correttamente, l'applicazione suonerà.

### <a name="unregister-push-notifications"></a>Annulla registrazione notifiche push

Le applicazioni possono annullare la registrazione della notifica push in qualsiasi momento. Chiamare il `unregisterPushNotification()` metodo su callAgent per annullare la registrazione.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Gestione chiamate
È possibile accedere alle proprietà delle chiamate ed eseguire varie operazioni durante una chiamata per gestire le impostazioni relative a video e audio.

### <a name="call-properties"></a>Proprietà della chiamata

Ottenere l'ID univoco per la chiamata:

```java
String callId = call.getCallId();
```

Per informazioni sugli altri partecipanti alla raccolta Call ispezionate `remoteParticipant` sull' `call` istanza:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

Identità del chiamante se la chiamata è in arrivo:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Ottenere lo stato della chiamata: 

```java
CallState callState = call.getState();
```

Restituisce una stringa che rappresenta lo stato corrente di una chiamata:
* ' None '-stato di chiamata iniziale
* ' In entrata ': indica che la chiamata è in ingresso, deve essere accettata o rifiutata
* ' Connecting '-stato di transizione iniziale dopo aver inserito o accettato la chiamata
* ' Squillo '-per una chiamata in uscita: indica che la chiamata è squillante per i partecipanti remoti, ma il suo lato è' in ingresso '
* ' EarlyMedia ': indica uno stato in cui viene riprodotto un annuncio prima della connessione della chiamata
* ' Connected ': la chiamata è connessa
* ' Trattieni ': la chiamata viene messa in attesa, nessun contenuto multimediale viene propagato tra l'endpoint locale e i partecipanti remoti
* ' Disconnecting '-lo stato della transizione prima della chiamata passa allo stato ' disconnected '
* ' Disconnected '-stato finale della chiamata


Per informazioni sul motivo per cui è stata terminata una chiamata, controllare la `callEndReason` Proprietà. Contiene codice/sottocodice: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Per verificare se la chiamata corrente è una chiamata in ingresso, controllare la `isIncoming` proprietà:

```java
boolean isIncoming = call.getIsIncoming();
```

Per verificare se il microfono corrente è disattivato, controllare la `muted` proprietà:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Per esaminare i flussi video attivi, controllare la `localVideoStreams` raccolta:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Mute e Unmute

Per disattivare o disattivare l'endpoint locale è possibile usare le `mute` `unmute` API asincrone e:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Avviare e arrestare l'invio del video locale

Per avviare un video, è necessario enumerare le fotocamere usando l' `getCameraList` API per l' `deviceManager` oggetto. Creare quindi una nuova istanza di `LocalVideoStream` passando la fotocamera desiderata e passarla nell' `startVideo` API come argomento:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
videoOptions = new VideoOptions(currentVideoStream);
Future startVideoFuture = call.startVideo(currentVideoStream);
startVideoFuture.get();
```

Una volta avviato correttamente l'invio del video, `LocalVideoStream` verrà aggiunta un'istanza alla `localVideoStreams` raccolta nell'istanza della chiamata.

```java
currentVideoStream == call.getLocalVideoStreams().get(0);
```

Per arrestare il video locale, passare l' `localVideoStream` istanza disponibile nella `localVideoStreams` raccolta:

```java
call.stopVideo(localVideoStream).get();
```

È possibile passare a un dispositivo della fotocamera diverso mentre è in corso l'invio del video richiamando `switchSource` in un' `localVideoStream` istanza:
```java
localVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Gestione dei partecipanti remoti

Tutti i partecipanti remoti sono rappresentati dal `RemoteParticipant` tipo e sono disponibili tramite la `remoteParticipants` raccolta in un'istanza di chiamata.

### <a name="list-participants-in-a-call"></a>Elenca i partecipanti a una chiamata
La `remoteParticipants` raccolta restituisce un elenco di partecipanti remoti nella chiamata specificata:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Proprietà del partecipante remoto
A un determinato partecipante remoto è associato un set di proprietà e raccolte:

* Ottiene l'identificatore per questo partecipante remoto.
Identity è uno dei tipi ' Identifier '
```java
CommunicationIdentifier participantIdentity = remoteParticipant.getIdentifier();
```

* Ottiene lo stato del partecipante remoto.
```java
ParticipantState state = remoteParticipant.getState();
```
Lo stato può essere uno dei
* ' Idle '-stato iniziale
* ' Connecting '-stato di transizione durante la connessione del partecipante alla chiamata
* ' Connected ': il partecipante è connesso alla chiamata
* ' Trattieni ': il partecipante è in attesa
* ' EarlyMedia '-l'annuncio viene riprodotto prima della connessione del partecipante alla chiamata
* ' Disconnected '-stato finale-partecipante disconnesso dalla chiamata


* Per informazioni sul motivo per cui un partecipante ha lasciato la chiamata, controllare la `callEndReason` proprietà:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Per verificare se il partecipante remoto è disattivato o meno, controllare la `isMuted` proprietà:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Per verificare se il partecipante remoto sta parlando o meno, controllare la `isSpeaking` proprietà:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Per controllare tutti i flussi video inviati da un determinato partecipante in questa chiamata, controllare la `videoStreams` raccolta:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Aggiungere un partecipante a una chiamata

Per aggiungere un partecipante a una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare `addParticipant` . In questo modo verrà restituita in modo sincrono l'istanza del partecipante remoto.

```java
const acsUser = new CommunicationUser("<acs user id>");
const acsPhone = new PhoneNumber("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone);
```

### <a name="remove-participant-from-a-call"></a>Rimuovi partecipante da una chiamata
Per rimuovere un partecipante da una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare `removeParticipant` .
Questa operazione verrà risolta in modo asincrono dopo la rimozione del partecipante dalla chiamata.
Il partecipante verrà rimosso anche dalla `remoteParticipants` raccolta.
```java
RemoteParticipant remoteParticipant = call.getParticipants().get(0);
call.removeParticipant(acsUser).get();
call.removeParticipant(acsPhone).get();
```

## <a name="render-remote-participant-video-streams"></a>Rendering dei flussi video del partecipante remoto
Per elencare i flussi video e i flussi di condivisione dello schermo dei partecipanti remoti, esaminare le `videoStreams` Raccolte:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Per eseguire il rendering di un `RemoteVideoStream` da un partecipante remoto, è necessario sottoscrivere un `OnVideoStreamsUpdated` evento.

All'interno dell'evento, la modifica della `isAvailable` proprietà su true indica che il partecipante remoto sta attualmente inviando un flusso. Una volta eseguita questa operazione, creare una nuova istanza di un `Renderer` , quindi creare un nuovo `RendererView` usando `createView` l'API asincrona e connettersi `view.target` in qualsiasi punto dell'interfaccia utente dell'applicazione.

Ogni volta che la disponibilità di un flusso remoto cambia è possibile scegliere di eliminare definitivamente l'intero renderer, un oggetto specifico `RendererView` o mantenerlo, ma ciò comporterà la visualizzazione di un frame video vuoto.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Proprietà del flusso video remoto
Il flusso video remoto ha un paio di proprietà

* `Id` -ID di un flusso video remoto
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -Può essere ' video ' o ' ScreenSharing '
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -Indica se l'endpoint del partecipante remoto sta inviando un flusso in modo attivo
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Metodi e proprietà del renderer
Oggetto renderer che segue le API

* Creare un' `RendererView` istanza che può essere collegata successivamente nell'interfaccia utente dell'applicazione per eseguire il rendering del flusso video remoto.
```java
// Create a view for a video stream
renderer.createView()
```
* Dispose renderer e tutti `RendererView` associati a questo renderer
```java
renderer.dispose()
```

* `StreamSize` -dimensione (larghezza/altezza) di un flusso video remoto
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>Metodi e proprietà di RendererView
Quando si crea un oggetto `RendererView` , è possibile specificare le `scalingMode` `mirrored` proprietà e che verranno applicate a questa visualizzazione: la modalità di ridimensionamento può essere ' Stretch ' | ' Crop ' | ' Fit ' se `mirrored` è impostato su `true` , il flusso di cui è stato eseguito il rendering verrà capovolto verticalmente.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

Il RendererView creato può quindi essere collegato all'interfaccia utente dell'applicazione usando il frammento di codice seguente:
```java
layout.addView(rendererView);
```

In seguito è possibile aggiornare la modalità di ridimensionamento richiamando `updateScalingMode` l'API nell'oggetto RendererView con una di ScalingMode. Stretch | ScalingMode. Crop | ScalingMode. Fit come argomento.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Gestione dei dispositivi

`DeviceManager` consente di enumerare i dispositivi locali che possono essere usati in una chiamata per trasmettere i flussi audio/video. Consente inoltre di richiedere all'utente l'autorizzazione per l'accesso al microfono e alla fotocamera mediante l'API del browser nativo.

È possibile accedere `deviceManager` chiamando il `callClient.getDeviceManager()` metodo.
> [!WARNING]
> Attualmente `callAgent` è necessario creare un'istanza di un oggetto per ottenere l'accesso a devicemanager

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Enumerare i dispositivi locali

Per accedere ai dispositivi locali, è possibile usare i metodi di enumerazione sul Device Manager. L'enumerazione è un'azione sincrona.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Imposta microfono/altoparlante predefinito

Gestione dispositivi consente di impostare un dispositivo predefinito che verrà usato all'avvio di una chiamata.
Se le impostazioni predefinite del client non sono impostate, i servizi di comunicazione eseguiranno il fallback alle impostazioni predefinite del sistema operativo.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophoneList().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakerList().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Anteprima della fotocamera locale

È possibile usare `DeviceManager` e `Renderer` per avviare il rendering dei flussi dalla fotocamera locale. Questo flusso non verrà inviato ad altri partecipanti; si tratta di un feed di anteprima locale. Si tratta di un'azione asincrona.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the renderingSurface to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Modello di eventi
È possibile sottoscrivere la maggior parte delle proprietà e delle raccolte per ricevere una notifica quando cambiano i valori.

### <a name="properties"></a>Proprietà
Per sottoscrivere `property changed` gli eventi:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnCallStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnCallStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Raccolte
Per sottoscrivere `collection updated` gli eventi:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
