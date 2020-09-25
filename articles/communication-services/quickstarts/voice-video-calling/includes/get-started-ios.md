---
title: "Avvio rapido: Aggiungere chiamate a un'app iOS con Servizi di comunicazione di Azure"
description: Questa guida di avvio rapido illustra come usare la libreria client Chiamate di Servizi di comunicazione di Azure per iOS.
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c67440453e5ca8395464369d75bfac418a564764
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944800"
---
Questa guida di avvio rapido illustra come avviare una chiamata con la libreria client Chiamate di Servizi di comunicazione di Azure per iOS.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione sono necessari i prerequisiti seguenti:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un Mac che esegue [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), insieme a un certificato dello sviluppatore valido installato nel portachiavi.
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un [token di accesso utente](../../access-tokens.md) per il servizio di comunicazione di Azure.

## <a name="setting-up"></a>Configurazione

### <a name="creating-the-xcode-project"></a>Creazione del progetto Xcode

In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application**. Questa esercitazione usa il [framework SwiftUI](https://developer.apple.com/xcode/swiftui/), quindi è necessario impostare **Linguaggio** su **Swift** e **Interfaccia utente** su **SwiftUI**. In questa guida di avvio rapido non verranno creati unit test o test dell'interfaccia utente. È possibile deselezionare **Include Unit Tests** (Includi unit test) e anche **Include UI Tests** (Includi test interfaccia utente).

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot che mostra la finestra per la creazione di un nuovo progetto in Xcode.":::

### <a name="install-the-package"></a>Installare il pacchetto

Aggiungere la libreria client Chiamate di Servizi di comunicazione di Azure e le relative dipendenze (AzureCore.framework e AzureCommunication.framework) al progetto.

> [!NOTE]
> Con la versione di AzureCommunicationCalling SDK è possibile trovare uno script bash `BuildAzurePackages.sh`. Quando viene eseguito, lo script `sh ./BuildAzurePackages.sh` fornisce il percorso dei pacchetti di framework generati che è necessario importare nell'app di esempio nel passaggio successivo. Si noti che è necessario configurare gli strumenti da riga di comando di Xcode se non è già stato fatto prima di eseguire lo script: Avviare Xcode, selezionare "Preferences -> Locations" (Preferenze -> Percorsi). Selezionare la versione di Xcode per gli strumenti da riga di comando.

1. Scaricare la libreria client Chiamate di Servizi di comunicazione di Azure per iOS.
2. In Xcode fare clic sul file di progetto e selezionare la destinazione di compilazione per aprire l'editor delle impostazioni.
3. Nella scheda **General** (Generale) scorrere fino alla sezione **Frameworks, Libraries, and Embedded Content** (Framework, librerie e contenuto incorporato) e fare clic sull'icona **"+"** .
4. In basso a sinistra nella finestra di dialogo scegliere **Add Files** (Aggiungi file) e passare alla directory **AzureCommunicationCalling.framework** del pacchetto della libreria client decompresso.
    1. Ripetere l'ultimo passaggio per aggiungere **AzureCore.framework** e **AzureCommunication.framework**.
5. Aprire la scheda **Build Settings** (Impostazioni di compilazione) dell'editor di impostazioni del progetto e scorrere fino alla sezione **Search Paths** (Percorsi di ricerca). Aggiungere una nuova voce **Framework Search Paths** per la directory contenente **AzureCommunicationCalling.framework**.
    1. Aggiungere un'altra voce nei percorsi di ricerca di framework che punta alla cartella contenente le dipendenze.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Screenshot che illustra l'aggiornamento dei percorsi di ricerca di framework all'interno di XCode.":::

### <a name="request-access-to-the-microphone"></a>Richiedere l'accesso al microfono

Per accedere al microfono del dispositivo, è necessario aggiornare il file Information Property List dell'app con `NSMicrophoneUsageDescription`. Impostare il valore associato su un oggetto `string` che verrà incluso nella finestra di dialogo usata dal sistema per richiedere l'accesso dell'utente.

Fare clic con il pulsante destro del mouse sulla voce `Info.plist` dell'albero del progetto e scegliere **Open As** > **Source Code** (Apri come > Codice sorgente). Aggiungere le righe seguenti nella sezione `<dict>` di primo livello e quindi salvare il file.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Aprire il file **ContentView.swift** del progetto e aggiungere una dichiarazione `import` all'inizio del file per importare `AzureCommunicationCalling library`. Inoltre, importare `AVFoundation`, che è necessario per la richiesta di autorizzazione audio nel codice.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Sostituire l'implementazione dello struct `ContentView` con alcuni semplici controlli dell'interfaccia utente che consentano a un utente di avviare e terminare una chiamata. In questa guida di avvio rapido verrà collegata logica di business a questi controlli.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: ACSCallClient?
    @State var callAgent: ACSCallAgent?
    @State var call: ACSCall?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client Chiamate di Servizi di comunicazione di Azure:

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | CallClient è il principale punto di ingresso alla libreria client Chiamate.|
| ACSCallAgent | CallAgent si usa per avviare e gestire le chiamate. |
| CommunicationUserCredential | CommunicationUserCredential si usa come credenziale del token per creare un'istanza di CallAgent.| 
| CommunicationIndentifier | CommunicationIndentifier si usa per rappresentare l'identità dell'utente, che può essere una delle seguenti: CommunicationUser/PhoneNumber/CallingApplication. |

## <a name="authenticate-the-client"></a>Autenticare il client

Inizializzare un'istanza di `CallAgent` con un token di accesso utente, che consentirà di effettuare e ricevere chiamate. Aggiungere il codice seguente al callback `onAppear` in **ContentView.swift**:

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = ACSCallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

È necessario sostituire `<USER ACCESS TOKEN>` con un token di accesso utente valido per la risorsa. Se non è disponibile un token, vedere la documentazione dei [token di accesso utente](../../access-tokens.md).

## <a name="start-a-call"></a>Avviare una chiamata

Il metodo `startCall` viene impostato come azione che verrà eseguita quando viene toccato il pulsante *Start Call* (Avvia chiamata). Aggiornare l'implementazione per avviare una chiamata con `ASACallAgent`:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
            self.call = self.callAgent?.call(callees, options: ACSStartCallOptions())
        }
    }
}
```

È anche possibile usare le proprietà in `ACSStartCallOptions` per impostare le opzioni iniziali per la chiamata, ad esempio per consentire l'avvio della chiamata con il microfono disattivato.

## <a name="end-a-call"></a>Terminare una chiamata

Implementare il metodo `endCall` per terminare la chiamata corrente quando viene toccato il pulsante *End Call* (Termina chiamata).

```swift
func endCall()
{    
    self.call!.hangup(ACSHangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Eseguire il codice

È possibile creare ed eseguire un'app nel simulatore iOS selezionando **Product** > **Run** (Prodotto > Esegui) o premendo i tasti di scelta rapida &#8984;+R.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Aspetto finale dell'app di avvio rapido":::

È possibile effettuare una chiamata VoIP in uscita fornendo un ID utente nel campo di testo e toccando il pulsante **Start Call** (Avvia chiamata). Con la chiamata a `8:echo123` ci si connette a un Echo Bot, la scelta ideale per iniziare e per verificare che i dispositivi audio funzionino. 

> [!NOTE]
> La prima volta che si effettua una chiamata, il sistema chiederà l'accesso al microfono. In un'applicazione di produzione è consigliabile usare l'API `AVAudioSession` per [controllare lo stato delle autorizzazioni](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e aggiornare normalmente il comportamento dell'applicazione quando l'autorizzazione non viene concessa.

## <a name="sample"></a>Esempio

È possibile scaricare l'app di esempio da [GitHub](https://github.com/Azure/Communication/tree/master/samples/AzureCommunicationCalling/iOS/Swift)
