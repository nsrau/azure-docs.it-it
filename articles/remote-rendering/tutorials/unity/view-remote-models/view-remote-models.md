---
title: Visualizzazione di un modello di cui è stato eseguito il rendering in remoto
description: L'esercitazione "Hello World" di Rendering remoto di Azure illustra come visualizzare un modello sottoposto a rendering remoto da Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: bd9e9b6754c8626a8d858b9832a8e3547b72352d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231906"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Esercitazione: Visualizzazione di un modello di cui è stato eseguito il rendering in remoto

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Effettuare il provisioning di un'istanza di Rendering remoto di Azure
> * Creare e arrestare una sessione di rendering
> * Riutilizzare una sessione di rendering esistente
> * Connettersi e disconnettersi dalle sessioni
> * Caricare modelli in una sessione di rendering

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario avere:

* Una sottoscrizione attiva di Azure con pagamento in base al consumo. [Creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* L'ultima versione di Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(download)](https://git-scm.com/downloads)
* Unity, la versione più recente 2019.3, è consigliabile usare l'hub Unity [(scarica)](https://unity3d.com/get-unity/download)
  * Installare questi moduli in Unity:
    * **UWP** - Supporto per la compilazione della piattaforma UWP (Universal Windows Platform)
    * **IL2CPP** - Supporto per la compilazione di Windows (IL2CPP)
* Conoscenza intermedia di Unity e del linguaggio C# (ad esempio, creazione di script e oggetti, uso di prefab, configurazione di eventi di Unity e così via)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Effettuare il provisioning di un'istanza di Rendering remoto di Azure

Per ottenere l'accesso al servizio Rendering remoto di Azure, è prima di tutto necessario [creare un account](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Creare un nuovo progetto Unity

> [!TIP]
> Il [repository di esempi di Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering) contiene un progetto con tutte le esercitazioni completate, che può essere usato come riferimento. Esplorare *Unity\Tutorial-Complete* per trovare il progetto Unity completo.

Nell'hub Unity creare un nuovo progetto.
In questo esempio si presuppone che il progetto venga creato in una cartella denominata **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Nuovo progetto di Unity":::

## <a name="include-the-azure-remote-rendering-package"></a>Includere il pacchetto di Rendering remoto di Azure

È necessario modificare il file `Packages/manifest.json` che si trova nella cartella del progetto Unity. Aprire il file in un editor di testo e aggiungere le righe seguenti all'inizio del manifesto:

```json
{
    "scopedRegistries": [
    {
        "name": "Azure Mixed Reality Services",
        "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
        "scopes": ["com.microsoft.azure"]
    }
    ],
    "dependencies": {
        "com.unity.render-pipelines.universal": "7.3.1",
        "com.microsoft.azure.remote-rendering": "0.1.31",
        ...existing dependencies...
    }
}
```

Dopo aver modificato e salvato il manifesto, Unity verrà aggiornato automaticamente. Verificare che i pacchetti siano stati caricati nella finestra *Project* (Progetto):

:::image type="content" source="./media/confirm-packages.png" alt-text="Verificare l'importazione dei pacchetti":::

Se i pacchetti non vengono caricati, controllare la presenza di errori nella console di Unity. Se non sono presenti errori e non è ancora possibile visualizzare i pacchetti nella cartella **Packages**, selezionare il pulsante di attivazione visibilità pacchetti.\
![Proprietà della videocamera Unity](./media/unity-package-visibility.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Assicurarsi di avere l'ultima versione del pacchetto

I passaggi seguenti consentono di verificare l'uso dell'ultima versione del pacchetto di rendering remoto per il progetto.

1. Nel menu superiore dell'editor di Unity aprire *Window->Package Manager* (Finestra -> Gestione pacchetti).
1. Selezionare il pacchetto **Rendering remoto di Microsoft Azure**.
1. Nella pagina di gestione pacchetti per il pacchetto **Rendering remoto di Microsoft Azure** verificare se è disponibile il pulsante **Update** (Aggiorna). Se lo è, selezionarlo per aggiornare il pacchetto all'ultima versione disponibile:\
![Il pacchetto di Rendering remoto di Azure in gestione pacchetti](./media/package-manager.png)
1. L'aggiornamento del pacchetto può talvolta causare errori nella console. In questo caso, provare a chiudere e riaprire il progetto.
1. Quando il pacchetto è aggiornato,nella gestione pacchetti verrà visualizzato il messaggio **Up to date** (Aggiornato) invece del pulsante Update (Aggiorna).\
![Pacchetto aggiornato](./media/package-up-to-date.png)
## <a name="configure-the-camera"></a>Configurare la fotocamera

1. Selezionare il nodo **Main Camera** (Fotocamera principale).

1. Aprire il menu di scelta rapida facendo clic con il pulsante destro del mouse sul componente *Transform* e scegliere l'opzione **Reset** (Reimposta):

    ![reimpostare la trasformazione della fotocamera](./media/camera-reset-transform.png)

1. Impostare **Clear flags** (Flag di cancellazione) su *Solid Color* (Tinta unita)

1. Impostare **Background** (Sfondo) su *Black* (Nero) (#000000), con alfa (A) completamente trasparente (0)

    ![Selettore colori](./media/color-wheel-black.png)

1. Impostare **Clipping Planes** (Piani di ritaglio) su *Near = 0.3* (Vicino = 0,3) e *Far = 20* (Lontano = 20). In questo modo il rendering ritaglierà la geometria più vicina di 30 cm o più lontana di 20 metri.

    ![Proprietà della fotocamera Unity](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Modificare le impostazioni del progetto

1. Aprire *Edit > Project Settings...* (Modifica > Impostazioni progetto...)
1. Selezionare **Quality** (Qualità) nel menu di elenco a sinistra
1. Impostare **Default Quality Level** (Livello di qualità predefinito) per tutte le piattaforme su *Low* (Basso). Questa impostazione assicura un rendering più efficiente del contenuto locale e non influisce sulla qualità del contenuto sottoposto a rendering remoto.

    ![cambiare le impostazioni di qualità del progetto](./media/settings-quality.png)

1. Selezionare **Graphics** (Grafica) nel menu di elenco a sinistra
1. Impostare l'opzione **Scriptable Rendering Pipeline** (Pipeline di rendering gestibile tramite script) su *HybridRenderingPipeline*.\
    ![Modifica delle impostazioni grafiche del progetto](./media/settings-graphics-render-pipeline.png)\
    In alcuni casi l'interfaccia utente non popola l'elenco dei tipi di pipeline disponibili dai pacchetti. In tal caso, l'asset *HybridRenderingPipeline* deve essere trascinato nel campo manualmente:\
    ![Modifica delle impostazioni grafiche del progetto](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Se non è possibile trascinare l'asset *HybridRenderingPipeline* nel campo Render Pipeline Asset (Asset della pipeline di rendering), probabilmente perché il campo non esiste, verificare che la configurazione dei pacchetti contenga il pacchetto `com.unity.render-pipelines.universal`.

1. Selezionare **Player** (Lettore) nel menu di elenco a sinistra
1. Selezionare la scheda **Universal Windows Platform settings** (Impostazioni di Universal Windows Platform), rappresentata dall'icona di Windows.
1. Cambiare l'opzione **XR Settings** (Impostazioni XR) per supportare la realtà mista Windows, come illustrato di seguito:
    1. Abilitare **Virtual Reality Supported** (Realtà virtuale supportata)
    1. Premere il pulsante '+' e aggiungere **Realtà mista di Windows**
    1. Impostare **Depth Format** (Formato profondità) su *16-Bit Depth* (Profondità a 16 bit)
    1. Verificare che l'opzione **Depth Buffer Sharing** (Condivisione buffer di profondità) sia abilitata
    1. Impostare **Stereo Rendering Mode** (Modalità di rendering stereo) su *Single Pass Instanced* (Istanze a singolo passaggio)

    ![Impostazioni del lettore](./media/xr-player-settings.png)

1. Nella stessa finestra, sopra **XR Settings** (Impostazioni XR), espandere **Publishing Settings** (Impostazioni di pubblicazione)
1. Scorre in basso fino a **Capabilities** (Funzionalità) e selezionare:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (*facoltativo*). Selezionare questa opzione se si vuole connettere il debugger remoto di Unity al dispositivo.

1. In **Supported Device Families** (Famiglie di dispositivi supportate) abilitare **Holographic** (Dispositivo olografico) e **Desktop**
1. Chiudere o ancorare il pannello **Project Settings** (Impostazioni progetto)
1. Aprire *File->Build Settings* (File->Impostazioni compilazione)
1. Selezionare **Universal Windows Platform**
1. Configurare le impostazioni in modo che corrispondano a quelle indicate di seguito
1. Premere il pulsante **Switch Platform** (Cambia piattaforma).\
![Impostazioni di compilazione](./media/build-settings.png)
1. Dopo che Unity cambia piattaforma, chiudere il pannello di compilazione.

## <a name="validate-project-setup"></a>Verificare la configurazione del progetto

Eseguire la procedura seguente per verificare che le impostazioni del progetto siano corrette.

1. Scegliere la voce **ValidateProject** dal menu **RemoteRendering** sulla barra degli strumenti dell'editor di Unity.
1. Nella finestra **ValidateProject** per verificare e correggere gli errori delle impostazioni del progetto, se necessario.

    ![Verifica del progetto nell'editor di Unity](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Creare uno script per coordinare la connessione e lo stato di Rendering remoto di Azure

Sono disponibili quattro fasi di base per visualizzare i modelli sottoposti a rendering remoto, descritte nel diagramma di flusso seguente. Ogni fase deve essere eseguita nell'ordine indicato. Il passaggio successivo consiste nel creare uno script che gestirà lo stato dell'applicazione e procederà attraverso ogni fase necessaria.

![Rendering remoto di Azure, stack 0](./media/remote-render-stack-0.png)

1. Nel riquadro *Project* (Progetto), in **Assets**, creare una nuova cartella denominata *RemoteRenderingCore*. Quindi all'interno di *RemoteRenderingCore* creare un'altra cartella denominata *Scripts*.

1. Creare un [nuovo script C#](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) denominato **RemoteRenderingCoordinator**.
Il progetto sarà simile al seguente:

    ![Gerarchia del progetto](./media/project-structure.png)

    Questo script coordinatore terrà traccia e gestirà lo stato di rendering remoto. Si noti che parte di questo codice si usa per mantenere lo stato, esporre funzionalità ad altri componenti, attivare eventi e archiviare dati specifici dell'applicazione che non sono correlati *direttamente* a Rendering remoto di Azure. Usare il codice seguente come punto di partenza. Il codice di Rendering remoto di Azure specifico verrà descritto e implementato più avanti nell'esercitazione.

1. Aprire **RemoteRenderingCoordinator** nell'editor di codice e sostituirne l'intero contenuto con il codice seguente:

```csharp
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // For most people '<region>' is either 'westus2' or 'westeurope'
    public string AccountDomain = "westus2.mixedreality.azure.com";

    [Header("Development Account Credentials")]
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    public string sessionIDOverride;

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<AzureFrontendAccountInfo> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static AzureSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<AzureFrontendAccountInfo> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(sessionIDOverride))
                return sessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();
        return allSessions.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Actions.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Actions?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        var properties = await session.GetPropertiesAsync().AsTask();

        switch (properties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Creare il GameObject di Rendering remoto di Azure

Il coordinatore del rendering remoto e il relativo script necessario (*ARRServiceUnity*) sono entrambi MonoBehavior che è necessario collegare a un GameObject nella scena. Lo script *ARRServiceUnity* viene fornito da Rendering remoto di Azure per esporre gran parte della funzionalità del servizio per la connessione e la gestione di sessioni remote.

1. Creare un nuovo GameObject nella scena premendo CTRL+ MAIUSC+N o selezionando *GameObject-> Create Empty* (Crea vuoto) e assegnargli il nome **RemoteRenderingCoordinator**.
1. Aggiungere lo script *RemoteRenderingCoordinator* al GameObject **RemoteRenderingCoordinator**.\
![Aggiungere il componente RemoteRenderingCoordinator](./media/add-coordinator-script.png)
1. Verificare che lo script *ARRServiceUnity*, visualizzato come *Service* nella finestra di controllo, venga aggiunto automaticamente al GameObject. Questo è il risultato di avere `[RequireComponent(typeof(ARRServiceUnity))]` all'inizio dello script **RemoteRenderingCoordinator**.
1. Aggiungere le credenziali di Rendering remoto di Azure e il dominio dell'account allo script coordinatore:\
![Aggiungere le credenziali](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Inizializzare Rendering remoto di Azure

Ora che è disponibile il framework per il coordinatore, verrà implementata ognuna delle quattro fasi che iniziano con **Inizializzare il rendering remoto**.

![Rendering remoto di Azure, stack 1](./media/remote-render-stack-1.png)

L'**inizializzazione** indica a Rendering remoto di Azure quale oggetto videocamera usare per il rendering e fa avanzare la macchina a stati in **NotAuthorized**. Questo significa che è inizializzato ma non ancora autorizzato a connettersi a una sessione. Poiché l'avvio di una sessione di Rendering remoto di Azure comporta un costo, è necessario confermare che si vuole procedere.

Con il passaggio allo stato **NotAuthorized**, viene chiamato **CheckAuthorization**, che richiama l'evento **RequestingAuthorization** e determina quali credenziali dell'account usare (**AccountInfo** viene definito all'inizio della classe e usa le credenziali definite tramite la finestra di controllo di Unity nel passaggio precedente).

   > [!NOTE]
   > La ricompilazione del runtime non è supportata da Rendering remoto di Azure. Se lo script viene modificato e salvato mentre è attiva la modalità di riproduzione, è possibile che Unity si blocchi e che sia necessario arrestarlo tramite Gestione attività. Assicurarsi sempre di aver interrotto la modalità di riproduzione prima di modificare gli script.

1. Sostituire il contenuto di **InitializeARR** e **InitializeSessionService** con il codice completo seguente:

 ```csharp
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

Per procedere da **NotAuthorized** a **NoSession**, in genere viene visualizzata una finestra di dialogo modale in cui è possibile effettuare una selezione, come descritto in un altro capitolo. Per il momento, il controllo dell'autorizzazione verrà ignorato automaticamente chiamando **ByPassAuthentication** non appena viene attivato l'evento **RequestingAuthorization**.

1. Selezionare il GameObject **RemoteRenderingCoordinator** e individuare l'evento **OnRequestingAuthorization** di Unity esposto nella finestra di controllo del componente **RemoteRenderingCoordinator**.

1. Aggiungere un nuovo evento premendo '+' in basso a destra.
1. Trascinare il componente sul relativo evento, in modo che faccia riferimento a se stesso.\
![Ignorare l'autenticazione](./media/bypass-authorization-add-event.png)\
1. Nell'elenco a discesa selezionare **RemoteRenderingCoordinator-> BypassAuthorization**.\
![Ignorare l'autenticazione](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Creare o partecipare a una sessione remota

La seconda fase consiste nel creare o partecipare a una sessione di rendering remoto. Vedere [Sessioni di rendering remoto](../../../concepts/sessions.md) per altre informazioni.

![Rendering remoto di Azure, stack 2](./media/remote-render-stack-2.png)

La sessione remota è quella in cui verrà eseguito il rendering dei modelli. Il metodo **JoinRemoteSession( )** proverà a partecipare a una sessione esistente, monitorata con la proprietà **LastUsedSessionID** oppure se è presente un ID sessione attivo assegnato in **sessionIDOverride**. **sessionIDOverride** è destinato solo a scopi di debug e deve essere usato solo quando si sa che la sessione esiste e ci si vuole connettere esplicitamente.

Se non sono disponibili sessioni, ne verrà creata una nuova. La creazione di una nuova sessione è, tuttavia, un'operazione che richiede molto tempo. Pertanto, è consigliabile provare a creare sessioni solo quando è necessario e riutilizzarle quando possibile. Vedere [Applicazioni pronte per la commercializzazione: pool di sessioni, pianificazione e procedure consigliate](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) per altre informazioni sulla gestione delle sessioni.

> [!TIP]
> **StopRemoteSession()** terminerà la sessione attiva. Per evitare addebiti inutili, arrestare sempre le sessioni quando non sono più necessarie.

La macchina a stati passerà ora a **ConnectingToNewRemoteSession** o **ConnectingToExistingRemoteSession**, a seconda delle sessioni disponibili. L'apertura di una sessione esistente o la creazione di una nuova attiverà l'evento **ARRSessionService.OnSessionStatusChanged**, eseguendo il metodo **OnRemoteSessionStatusChanged**. Idealmente, questa operazione comporterà l'avanzamento della macchina a stati fino a **RemoteSessionReady**.

1. Per partecipare a una nuova sessione, modificare il codice per sostituire i metodi **JoinRemoteSession( )** e **StopRemoteSession( )** con gli esempi completi seguenti:

```csharp
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationParams(renderingSessionVmSize, maxLeaseHours, maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == sessionIDOverride)
            sessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Se si vuole risparmiare tempo riutilizzando le sessioni, assicurarsi di disattivare l'opzione **Auto-Stop Session** (Arresta automaticamente la sessione) nel componente *ARRServiceUnity*. Tenere presente che in questo modo le sessioni rimangono in esecuzione, anche se nessuno è connesso. La sessione può essere eseguita fino a quando viene raggiunto il valore di *MaxLeaseTime* prima che venga arrestata dal server. Il valore di *MaxLeaseTime* può essere modificato nel coordinatore di Rendering remoto, in *New Session Defaults* (Nuovi valori predefiniti della sessione). D'altra parte, se si arresta automaticamente ogni sessione alla disconnessione, sarà necessario attendere l'avvio di una nuova sessione ogni volta, il che può essere un processo alquanto lungo.

> [!NOTE]
> L'arresto di una sessione avrà effetto immediato e non potrà essere annullato. Una volta arrestata, è necessario creare una nuova sessione con lo stesso sovraccarico di avvio.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Connettere il runtime locale alla sessione remota

Successivamente, l'applicazione deve connettere il runtime locale alla sessione remota.

![Rendering remoto di Azure, stack 3](./media/remote-render-stack-3.png)

L'applicazione deve inoltre restare in ascolto di eventi relativi alla connessione tra il runtime e la sessione corrente. Tali modifiche di stato vengono gestite in **OnLocalRuntimeStatusChanged**. Questo codice consente di passare allo stato **ConnectingToRuntime**. Una volta stabilita la connessione in **OnLocalRuntimeStatusChanged**, lo stato passerà a **RuntimeConnected**. La connessione al runtime è l'ultimo stato di cui si occupa il coordinatore, il che significa che l'applicazione viene eseguita con tutta la configurazione comune ed è pronta per iniziare la funzione specifica della sessione, ossia il caricamento dei modelli sottoposti a rendering.

 1. Sostituire i metodi **ConnectRuntimeToRemoteSession( )** e **DisconnectRuntimeFromRemoteSession( )** con le versioni complete seguenti.
 1. È importante notare il metodo Unity **LateUpdate** e il fatto che aggiorna la sessione attiva corrente. In questo modo la sessione corrente può inviare e ricevere messaggi, nonché aggiornare il buffer dei fotogrammi con i fotogrammi ricevuti dalla sessione remota. È essenziale per il corretto funzionamento di Rendering remoto di Azure.

```csharp
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectToRuntime(new ConnectToRuntimeParams());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.DisconnectFromRuntime();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Actions.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Actions?.Update();
}
```

> [!NOTE]
> La connessione del runtime locale a una sessione remota dipende dalla chiamata a **Update** nella sessione attiva corrente. Se l'applicazione non avanza mai oltre lo stato **ConnectingToRuntime**, assicurarsi di chiamare regolarmente **Update** nella sessione attiva.

## <a name="load-a-model"></a>Caricare un modello

Una volta implementate le basi necessarie, è possibile caricare un modello nella sessione remota e iniziare a ricevere fotogrammi.

![Rendering remoto di Azure, stack 4](./media/remote-render-stack-4.png)

Il metodo **LoadModel** è progettato per accettare un percorso di modelli, un gestore di stato e una trasformazione padre. Questi argomenti verranno usati per caricare un modello nella sessione remota, aggiornare l'utente sullo stato di caricamento e orientare il modello sottoposto a rendering remoto in base alla trasformazione padre.

1. Sostituire completamente il metodo **LoadModel** con il codice seguente:

    ```csharp
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        if(progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

Questo codice esegue i passaggi seguenti:

1. Crea un'[entità remota](../../../concepts/entities.md).
1. Crea un GameObject locale per rappresentare l'entità remota.
1. Configura il GameObject locale per sincronizzare il relativo stato (ovvero la trasformazione) con ogni fotogramma dell'entità remota.
1. Imposta un nome e aggiunge [**WorldAnchor**](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) per facilitare la stabilizzazione.
1. Carica i dati del modello da Archiviazione BLOB nell'entità remota.
1. Restituisce l'entità padre per riferimento futuro.

## <a name="view-the-test-model"></a>Visualizza il modello di test

A questo punto è disponibile tutto il codice necessario per visualizzare un modello sottoposto a rendering remoto. Tutte e quattro le fasi necessarie per il rendering remoto sono state completate. Ora è necessario aggiungere il codice per avviare il processo di caricamento del modello.

![Rendering remoto di Azure, stack 4](./media/remote-render-stack-5.png)

1. Aggiungere il codice seguente alla classe **RemoteRenderingCoordinator**, subito sotto il metodo **LoadModel**:

    ```csharp
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Questo codice crea un GameObject che funge da elemento padre per il modello di test. Chiama quindi il metodo **LoadModel** per caricare il modello "builtin://Engine", che è un asset incorporato in Rendering remoto di Azure a scopo di test del rendering.

1. Salvare il codice.
1. Premere il pulsante Play nell'editor di Unity per avviare il processo di connessione a Rendering remoto di Azure e la creazione di una nuova sessione.
1. La visualizzazione gioco non contiene quasi niente, mentre la console mostra la modifica dello stato dell'applicazione. È probabile che passi a `ConnectingToNewRemoteSession` e che rimanga in questo stato, possibilmente per un massimo di cinque minuti.
1. Selezionare il GameObject **RemoteRenderingCoordinator** per vedere i relativi script collegati nella finestra di controllo. Osservare il componente **Service** che viene aggiornato tra le fasi di inizializzazione e connessione.
1. Monitorare l'output della console. Attendere che lo stato diventi **RuntimeConnected**.
1. Dopo la connessione del runtime, fare clic con il pulsante destro del mouse su **RemoteRenderingCoordinator** nella finestra di controllo per aprire il menu di scelta rapida. Quindi, scegliere l'opzione **Load Test Model** (Carica modello di test) nel menu di scelta rapida, aggiunto dalla parte `[ContextMenu("Load Test Model")]` del codice precedente.

    ![Caricamento dal menu di scelta rapida](./media/load-test-model.png)

1. Osservare nella console l'output di **ProgressHandler** passato nel metodo **LoadModel**.
1. Il modello viene sottoposto a rendering remoto.

> [!NOTE]
> Il modello remoto non sarà mai visibile nella visualizzazione scena, ma solo nella visualizzazione gioco. Il motivo è che Rendering remoto di Azure esegue il rendering dei fotogrammi in remoto specificamente per la prospettiva della videocamera della visualizzazione gioco e non riconosce la videocamera dell'editor, usata per il rendering della visualizzazione scena.

## <a name="next-steps"></a>Passaggi successivi

![Modello caricato](./media/test-model-rendered.png)

Congratulazioni! È stata creata un'applicazione di base in grado di visualizzare modelli sottoposti a rendering remoto con Rendering remoto di Azure. Nell'esercitazione successiva si integrerà MRTK e si importeranno modelli personalizzati.

> [!div class="nextstepaction"]
> [Passaggio successivo: Interfacce e modelli personalizzati](../custom-models/custom-models.md)
