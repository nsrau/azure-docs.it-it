---
title: Integrare Rendering remoto in un'app olografica C++/DirectX11
description: Questa esercitazione spiega come integrare Rendering remoto in un'app olografica C++/DirectX11 semplice creata con la creazione guidata progetto di Visual Studio
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: b6dceff8b777d09a6f791437eb4aaca70365b518
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300718"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Esercitazione: Integrare Rendering remoto in un'app olografica HoloLens

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Usare Visual Studio per creare un'app olografica che possa essere distribuita in HoloLens
> * Aggiungere i frammenti di codice e le impostazioni di progetto necessari per combinare il rendering locale con contenuto sottoposto a rendering in remoto

Questa esercitazione è incentrata sull'aggiunta dei bit necessari a una `Holographic App` nativa di esempio per combinare il rendering locale con Rendering remoto di Azure. L'unico tipo di feedback sullo stato in questa app è tramite il pannello di output di debug in Visual Studio, quindi è consigliabile avviare l'esempio dall'interno di Visual Studio. L'aggiunta di feedback in-app appropriato esula dall'ambito di questo esempio, poiché la creazione da zero di un pannello di testo dinamico comporta una grande quantità di scrittura di codice. Un buon punto di partenza è la classe `StatusDisplay`, che fa parte del [progetto di esempio Remoting Player su GitHub](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content). Di fatto, la versione predefinita di questa esercitazione usa una copia locale di questa classe.

> [!TIP]
> Il [repository di esempi ARR](https://github.com/Azure/azure-remote-rendering) contiene il risultato di questa esercitazione come progetto di Visual Studio pronto per l'uso. È inoltre arricchito con una segnalazione corretta degli errori e dello stato tramite la classe dell'interfaccia utente `StatusDisplay`. All'interno dell'esercitazione tutte le aggiunte specifiche di ARR sono definite dall'ambito `#ifdef USE_REMOTE_RENDERING` / `#endif`, quindi è facile identificare le aggiunte di Rendering remoto.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario avere:

* Informazioni sull'account (ID account, chiave dell'account, ID sottoscrizione). Se non si ha un account Azure, [crearne uno](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* L'ultima versione di Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/).
* [Visual Studio Tools per Realtà mista](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). In particolare, le installazioni del *carico di lavoro* seguente sono obbligatorie:
  * **Sviluppo per desktop con C++**
  * **Sviluppo per la piattaforma UWP (Universal Windows Platform)**
* I modelli di app Windows Mixed Reality per Visual Studio [(download)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Creare un nuovo esempio di app olografica

Come primo passaggio si creerà un esempio standard che costituirà la base per l'integrazione di Rendering remoto. Aprire Visual Studio, selezionare "Crea un nuovo progetto" e cercare "App olografica DirectX 11 (Windows universale - C++/WinRT)"

![Creare un nuovo progetto](media/new-project-wizard.png)

Digitare un nome di progetto a scelta, scegliere un percorso e selezionare il pulsante "Crea".
Nel nuovo progetto impostare la configurazione su **"Debug/ARM64"** . A questo punto dovrebbe essere possibile compilare il progetto e distribuirlo in un dispositivo HoloLens 2 connesso. Se lo si esegue in HoloLens, dovrebbe essere visualizzato un cubo rotante.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Aggiungere le dipendenze di Rendering remoto tramite NuGet

La prima cosa da fare per aggiungere funzionalità di Rendering remoto consiste nell'aggiungere le dipendenze del lato client. Le dipendenze rilevanti sono disponibili come pacchetto NuGet.
In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet** dal menu di scelta rapida.

Nella finestra di dialogo visualizzata cercare il pacchetto NuGet denominato **"Microsoft.Azure.RemoteRendering.Cpp"** :

![Cercare il pacchetto NuGet](media/add-nuget.png)

e aggiungerlo al progetto selezionando il pacchetto e quindi scegliendo il pulsante "Installa".

Il pacchetto NuGet aggiunge le dipendenze di Rendering remoto al progetto. In particolare:
* Crea un collegamento alla libreria client (RemoteRenderingClient.lib).
* Configura le dipendenze DLL.
* Imposta il percorso corretto per la directory di inclusione.

## <a name="project-preparation"></a>Preparazione del progetto

È necessario apportare piccole modifiche al progetto esistente. Queste modifiche sono minime, ma senza di esse Rendering remoto non funzionerebbe.

### <a name="enable-multithread-protection-on-directx-device"></a>Abilitare la protezione multithread sul dispositivo DirectX
Sul dispositivo `DirectX11` deve essere abilitata la protezione multithread. Per farlo, aprire il file DeviceResources.cpp nella cartella "Common" e inserire il codice seguente alla fine della funzione `DeviceResources::CreateDeviceResources()`:

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Abilitare le funzionalità di rete nel manifesto dell'app
Le funzionalità di rete devono essere abilitate in modo esplicito per l'app distribuita. Senza questa configurazione si verificherebbe il timeout delle query di connessione. Per abilitare le funzionalità di rete, fare doppio clic sull'elemento `package.appxmanifest` in Esplora soluzioni. Nell'interfaccia utente successiva passare alla scheda **Funzionalità** e selezionare:
* Internet (client e server)
* Internet (client)

![Funzionalità di rete](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Integrare Rendering remoto

Una volta preparato il progetto, è possibile iniziare a occuparsi del codice. Un buon punto di ingresso nell'applicazione è la classe `HolographicAppMain` (file HolographicAppMain.h/cpp), perché contiene tutti gli hook necessari per l'inizializzazione, la deinizializzazione e il rendering.

### <a name="includes"></a>Includes

Come prima cosa si aggiungeranno le inclusioni necessarie. Aggiungere l'inclusione seguente al file HolographicAppMain.h:

```cpp
#include <AzureRemoteRendering.h>
```

...e questa direttiva `include` aggiuntiva al file HolographicAppMain.cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
```

Per mantenere semplice il codice, viene definita l'abbreviazione per lo spazio dei nomi seguente all'inizio del file HolographicAppMain.h, dopo la direttiva `include`:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Questa abbreviazione è utile in quanto evita di scrivere lo spazio dei nomi per esteso ogni volta, consentendo comunque di riconoscere le strutture dei dati specifiche di ARR. Naturalmente è possibile usare anche la direttiva `using namespace...`.

### <a name="remote-rendering-initialization"></a>Inizializzazione di Rendering remoto
 
È necessario mantenere alcuni oggetti per la sessione e altro durante il ciclo di vita dell'applicazione. Il ciclo di vita coincide con la durata dell'oggetto `HolographicAppMain` dell'applicazione, quindi si aggiungeranno gli oggetti come membri alla classe `HolographicAppMain`. Il passaggio successivo consiste nell'aggiungere i membri della classe seguenti nel file HolographicAppMain.h:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Una buona posizione in cui eseguire l'implementazione effettiva è il costruttore della classe `HolographicAppMain`. Occorre eseguire tre tipi di inizializzazione:
1. Inizializzazione unica del sistema di Rendering remoto
1. Creazione del front-end
1. Creazione della sessione

Queste operazioni vengono eseguite in sequenza nel costruttore. Nei casi d'uso reali potrebbe però essere opportuno eseguire questi passaggi separatamente.

Aggiungere il codice seguente all'inizio del corpo del costruttore nel file HolographicAppMain.cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        memset(&clientInit, 0, sizeof(RR::RemoteRenderingInitialization));
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        RR::StartupRemoteRendering(clientInit);
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        memset(&init, 0, sizeof(RR::AzureFrontendAccountInfo));
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // if there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        auto SetNewSession = [this](RR::ApiHandle<RR::AzureSession> newSession)
        {
            SetNewState(AppConnectionStatus::Connecting, RR::Result::Success);
            m_session = newSession;
            m_api = m_session->Actions();
            m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
            m_session->ConnectionStatusChanged([this](auto status, auto error)
                {
                    OnConnectionStatusChanged(status, error);
                });

            // The following is async, but we'll get notifications via OnConnectionStatusChanged
            RR::ConnectToRuntimeParams init;
            init.mode = RR::ServiceRenderMode::Default;
            m_session->ConnectToRuntime(init);
        };

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // Create a new session
            RR::RenderingSessionCreationParams init;
            memset(&init, 0, sizeof(RR::RenderingSessionCreationParams));
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->Result())
                    {
                        SetNewSession(*handler->Result());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, RR::Result::Fail);
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, RR::Result::Success);
        }
    }

    // Rest of constructor code:
    ...
}
```
All'interno della funzione locale `SetNewSession` viene eseguita la registrazione a un callback che viene attivato ogni volta che cambia lo stato della connessione per la sessione specificata. La chiamata viene reindirizzata alla funzione `OnConnectionStatusChanged`. Verrà dichiarata e implementata (insieme al resto del codice della macchina a stati) nel paragrafo successivo. Si noti inoltre che le credenziali sono hardcoded nell'esempio e devono essere inserite ([ID account, chiave dell'account](../../../how-tos/create-an-account.md#retrieve-the-account-information) e [dominio](../../../reference/regions.md)).

La deinizializzazione viene eseguita in modo simmetrico e in ordine inverso alla fine del corpo del distruttore:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Macchina a stati

In Rendering remoto le funzioni principali per creare una sessione e caricare un modello sono funzioni asincrone. Per tenerne conto, è necessaria una semplice macchina a stati che essenzialmente esegua automaticamente la transizione fra gli stati seguenti:

*Inizializzazione -> creazione della sessione -> caricamento del modello (con stato)*

Di conseguenza, come passaggio successivo, si aggiungerà del codice per la gestione della macchina a stati alla classe. Viene dichiarata l'enumerazione `AppConnectionStatus` per i vari stati in cui può trovarsi l'applicazione. È simile a `RR::ConnectionStatus`, ma ha uno stato aggiuntivo per la connessione non riuscita.

Aggiungere i membri e le funzioni seguenti alla dichiarazione della classe:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, RR::Result error);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;

    }
```

Sul lato dell'implementazione del file cpp aggiungere i corpi delle funzioni seguenti:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = *async->Status();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, RR::Result error)
{
    m_currentStatus = state;
    m_connectionResult = error;

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, RR::ResultToString(error));
    OutputDebugStringA(buffer);
}


void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, error);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;

        // start model loading as soon as we have a valid connection
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
}
```

### <a name="per-frame-update"></a>Aggiornamento per frame

È necessario inviare un tick al client una volta per ogni tick di simulazione. La classe `HolographicApp1Main` fornisce un hook efficace per gli aggiornamenti per frame, quindi aggiungere il codice seguente al corpo della funzione `HolographicApp1Main::Update`:

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    // Tick Remote rendering:
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();
    }

    // Rest of the body:
    ...
}
```

### <a name="rendering"></a>Rendering

L'ultima cosa da fare è richiamare il rendering del contenuto remoto. Questa chiamata deve essere eseguita nella posizione esatta all'interno della pipeline di rendering, dopo la cancellazione della destinazione di rendering. Inserire il frammento di codice seguente nel blocco `UseHolographicCameraResources` all'interno della funzione `HolographicAppMain::Render`:

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);

        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Eseguire l'esempio

L'esempio dovrebbe ora essere in uno stato in cui viene compilato ed eseguito.

Quando l'esempio viene eseguito correttamente, visualizza il cubo rotante davanti all'utente e, dopo la creazione della sessione e il caricamento del modello, esegue il rendering del modello di motore che si trova nella posizione iniziale corrente. La creazione della sessione e il caricamento del modello possono richiedere alcuni minuti. Lo stato corrente viene scritto solo nel pannello di output di Visual Studio. È pertanto consigliabile avviare l'esempio dall'interno di Visual Studio.

> [!CAUTION]
> Il client si disconnette dal server quando la funzione di tick non viene chiamata da alcuni secondi. L'attivazione di punti di interruzione può quindi causare molto facilmente la disconnessione dell'applicazione.

Per una corretta visualizzazione dello stato con un pannello di testo, fare riferimento alla versione predefinita di questa esercitazione su GitHub.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati appresi tutti i passaggi necessari per aggiungere Rendering remoto a un esempio di **app olografica** C++/DirectX11 standard.
Per convertire il proprio modello, vedere la guida di avvio rapido seguente:

> [!div class="nextstepaction"]
> [Avvio rapido: Convertire un modello per il rendering](../../../quickstarts/convert-model.md)
