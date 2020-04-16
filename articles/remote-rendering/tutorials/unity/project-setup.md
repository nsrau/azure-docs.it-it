---
title: Configurazione di un progetto Unity da zero
description: Viene illustrato come configurare un progetto Unity vuoto per l'uso con Rendering remoto di Azure.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678652"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Esercitazione: Configurazione di un progetto Unity da zero

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Configurazione di un progetto Unity da zero per Rendering remoto di Azure.
> * Creazione e arresto di sessioni di rendering.
> * Riutilizzo di sessioni esistenti.
> * Connessione e disconnessione da sessioni.
> * Caricamento di modelli in una sessione di rendering.
> * Visualizzazione delle statistiche di connessione.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario avere:

* Informazioni sull'account (ID account, chiave dell'account, ID sottoscrizione). Se non si ha un account Azure, [crearne uno](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* L'ultima versione di Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(download)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
  * Installare questi moduli in Unity:
    * **UWP** - Supporto per la compilazione della piattaforma UWP (Universal Windows Platform)
    * **IL2CPP** - Supporto per la compilazione di Windows (IL2CPP)

> [!TIP]
> Il [repository di esempi di Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering) contiene progetti Unity preparati per tutte le esercitazioni. È possibile usarli per riferimento.

## <a name="create-a-new-unity-project"></a>Creare un nuovo progetto Unity

Nell'hub Unity creare un nuovo progetto.
In questo esempio si presuppone che il progetto venga creato in una cartella denominata `RemoteRendering`.

![finestra del nuovo progetto](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Configurare il manifesto del progetto

È necessario modificare il file `Packages/manifest.json` che si trova nella cartella del progetto Unity. Aprire il file in un editor di testo e aggiungere le righe elencate di seguito:

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
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Il pacchetto della pipeline di rendering Universal è facoltativo ma consigliato per motivi di prestazioni.
Dopo aver modificato e salvato il manifesto, Unity verrà aggiornato automaticamente. Verificare che i pacchetti siano stati caricati nella finestra *Project* (Progetto):

![verificare l'importazione del pacchetto](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Assicurarsi di avere l'ultima versione del pacchetto

I passaggi seguenti consentono di verificare l'uso dell'ultima versione del pacchetto di rendering remoto per il progetto.
1. Selezionare il pacchetto nella finestra Project (Progetto) e fare clic sull'icona del pacchetto: ![Selezione dell'icona del pacchetto](media/package-icons.png)
1. Nella finestra Inspector (Controllo) fare clic su "View in Package Manager" (Visualizza in Gestione pacchetti): ![controllo pacchetti](media/package-properties.png)
1. Nella pagina di gestione pacchetti per il pacchetto di rendering remoto, vedere se è disponibile il pulsante di aggiornamento. Se lo è, fare clic su di esso per aggiornare il pacchetto all'ultima versione disponibile: ![Il pacchetto di Rendering remoto di Azure in gestione pacchetti](media/package-manager.png)
1. Talvolta l'aggiornamento del pacchetto può generare errori nella console. In questo caso, provare a chiudere e riaprire il progetto.

## <a name="configure-the-camera"></a>Configurare la fotocamera

Selezionare il nodo **Main Camera** (Fotocamera principale).

1. Reimpostare la funzionalità *Transform* (Trasforma):

    ![reimpostare la trasformazione della fotocamera](media/camera-reset-transform.png)

1. Impostare **Clear flags** (Flag di cancellazione) su *Solid Color* (Tinta unita)

1. Impostare **Background** (Sfondo) su *Black* (Nero)

1. Impostare **Clipping Planes** (Pian di ritaglio) su *Near = 0.3* (Vicino = 0,3) e *Far = 20* (Lontano = 20). In questo modo il rendering ritaglierà la geometria più vicina di 30 cm o più lontana di 20 metri.

    ![Proprietà della fotocamera Unity](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Modificare le impostazioni del progetto

1. Aprire *Edit > Project Settings* (Modifica > Impostazioni progetto)
1. Nell'elenco a sinistra selezionare Quality (Qualità).
1. Impostare **Default Quality Level** (Livello di qualità predefinito) su *Low* (Basso)

    ![cambiare le impostazioni di qualità del progetto](media/settings-quality.png)

1. Selezionare **Graphics** (Grafica) a sinistra.
1. Impostare l'opzione **Scriptable Rendering Pipeline** (Pipeline di rendering gestibile tramite script) su *HybridRenderingPipeline*. Ignorare questo passaggio se non si usa la pipeline di rendering Universal.

    ![modifica delle impostazioni di grafica del progetto](media/settings-graphics-lwrp.png) A volte l'interfaccia utente non popola l'elenco dei tipi di pipeline disponibili nei pacchetti, nel qual caso l'asset *HybridRenderingPipeline* deve essere trascinato nel campo manualmente: ![modifica delle impostazioni di grafica del progetto](media/hybrid-rendering-pipeline.png)
1. Selezionare **Player** (Lettore) a sinistra.
1. Selezionare la scheda **Universal Windows Platform settings** (Impostazioni di Universal Windows Platform)
1. Cambiare l'opzione **XR Settings** (Impostazioni XR) per supportare la realtà mista Windows: ![impostazioni del player](media/xr-player-settings.png)
1. Selezionare le impostazioni come indicato nello screenshot precedente:
    1. Abilitare **Virtual Reality Supported** (Realtà virtuale supportata)
    1. Impostare **Depth Format** (Formato profondità) su *16-Bit Depth* (Profondità a 16 bit)
    1. Abilitare **Depth Buffer Sharing** (Condivisione del buffer profondità)
    1. Impostare **Stereo Rendering Mode** (Modalità di rendering stereo) su *Single Pass Instanced* (Istanze a singolo passaggio)

1. Nella stessa finestra, sopra *XR Settings* (Impostazioni XR), espandere **Publishing Settings** (Impostazioni di pubblicazione)
1. Scorre in basso fino a **Capabilities** (Funzionalità) e selezionare:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Facoltativo per lo sviluppo: **PrivateNetworkClientServer**

      Questa opzione è necessaria se si vuole connettere il debugger remoto Unity al dispositivo.

1. In **Supported Device Families** (Famiglie di dispositivi supportate) abilitare **Holographic** (Dispositivo olografico) e **Desktop**

1. Se si vuole usare il toolkit di realtà mista, vedere la [documentazione di MRTK](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview) per altre informazioni sulle impostazioni e le funzionalità consigliate.

## <a name="validate-project-setup"></a>Verificare la configurazione del progetto

Eseguire la procedura seguente per verificare che le impostazioni del progetto siano corrette.

1. Scegliere la voce ValidateProject dal menu RemoteRendering sulla barra degli strumenti dell'editor di Unity.
1. Usare la finestra ValidateProject per verificare e correggere le impostazioni del progetto, se necessario.

    ![Verifica del progetto nell'editor di Unity](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Creare uno script per inizializzare Rendering remoto di Azure

Creare un [nuovo script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) con il nome **RemoteRendering**. Aprire il file di script e sostituirne l'intero contenuto con il codice seguente:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Questo script Inizializza Rendering remoto di Azure, indica l'oggetto della fotocamera da usare per il rendering e inserisce un pulsante **Create Session** (Crea sessione) nel viewport, quando viene attivata la *modalità riproduzione*.

> [!CAUTION]
> Se lo script viene modificato e salvato mentre è attiva la modalità di riproduzione in Unity, è possibile che Unity si blocchi e che sia necessario arrestarlo tramite Gestione attività. Pertanto, interrompere sempre la modalità di riproduzione prima di modificare lo script  *RemoteRendering*.

## <a name="test-azure-remote-rendering-session-creation"></a>Testare la creazione della sessione di Rendering remoto di Azure

Creare un nuovo GameObject nella scena e aggiungervi il componente *RemoteRendering*. Immettere i valori di *Dominio dell'account*, *ID account* e *Chiave dell'account* appropriati per l'account di Rendering remoto:

![Proprietà del componente di rendering remoto](media/remote-rendering-component.png)

Avviare l'applicazione nell'editor (**premere Play** o CTRL + P). Nel viewport verrà visualizzato il pulsante **Create Session** (Crea sessione). Fare clic per avviare la prima sessione di Rendering remoto di Azure:

![Creazione di una prima sessione](media/test-create.png)

Se l'operazione non riesce, assicurarsi di aver immesso correttamente i dettagli dell'account nelle proprietà del componente RemoteRendering. In caso contrario, nella finestra della console verrà visualizzato un messaggio che mostra l'ID sessione assegnato e informa che la sessione è attualmente nello stato *Starting* (Avvio):

![Output di avvio della sessione](media/create-session-output.png)

A questo punto Azure effettua automaticamente il provisioning di un server e avvia una macchina virtuale di rendering remoto. Questa procedura **richiede in genere da 3 a 5 minuti**. Quando la VM è pronta, viene eseguito il callback `OnSessionStatusChanged` dello script Unity e viene visualizzato il nuovo stato della sessione:

![Output di sessione pronta](media/create-session-output-2.png)

La procedura è stata completata. Per il momento non succederà più nulla. Per evitare addebiti, è necessario arrestare sempre le sessioni quando non sono più necessarie. In questo esempio è possibile eseguire questa operazione facendo clic sul pulsante **Stop Session** (Arresta sessione) oppure arrestando la simulazione di Unity. A causa della proprietà **Auto-Stop Session** del componente *ARRServiceUnity*, che è attiva per impostazione predefinita, la sessione verrà arrestata automaticamente. Se si verificano arresti anomali o problemi di connessione, la sessione potrebbe rimanere in esecuzione per la durata di *MaxLeaseTime* prima che venga arrestata dal server.

> [!NOTE]
> L'arresto di una sessione avrà effetto immediato e non potrà essere annullato. Una volta arrestata, è necessario creare una nuova sessione con lo stesso sovraccarico di avvio.

## <a name="reusing-sessions"></a>Riutilizzo delle sessioni

La creazione di una nuova sessione è purtroppo un'operazione che richiede molto tempo. Pertanto, è consigliabile provare raramente a creare sessioni e riutilizzarle laddove possibile.

Inserire il codice seguente nello script  *RemoteRendering* e rimuovere le versioni precedenti delle funzioni duplicate:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Prima di eseguire questo codice, assicurarsi di disattivare l'opzione **Auto-Stop Session** nel componente RemoteRendering. In caso contrario, ogni sessione creata verrà arrestata automaticamente quando si interrompe la simulazione e non sarà possibile riutilizzarla.

Quando si preme *Play* (Riproduci), nel viewport sono ora disponibili tre pulsanti: **Create Session** (Crea sessione), **Query Active Sessions** (Cerca sessioni attive) e **Use Existing Session** (Usa una sessione esistente). Il primo pulsante crea sempre una nuova sessione. Il secondo pulsante esegue una query per cercare le sessioni *attive* esistenti. Se non è stato specificato manualmente un ID sessione da provare a usare, questa azione lo selezionerà automaticamente per uso futuro. Il terzo pulsante tenta di connettersi a una sessione esistente, che può essere una specificata manualmente tramite la proprietà *Session Id* del componente oppure una trovata tramite *Query Active Sessions* (Cerca sessioni attive).

La funzione **AutoStartSessionAsync** viene usata per simulare la pressione dei pulsanti al di fuori dell'editor.

> [!TIP]
> È possibile aprire sessioni arrestate, scadute o in stato di errore. Una volta aperta una sessione inattiva, anche se non può più essere usata per il rendering, è possibile eseguire query per trovare i relativi dettagli. Il codice precedente controlla lo stato di una sessione in `ARRService_OnSessionStarted`, per arrestarla automaticamente quando diventa inutilizzabile.

Con questa funzionalità, è ora possibile creare e riutilizzare le sessioni, migliorando in modo significativo il flusso di lavoro dello sviluppo.

In genere, la creazione di una sessione viene attivata all'esterno dell'applicazione client a causa del tempo necessario per avviare il server.

## <a name="connect-to-an-active-session"></a>Connettersi a una sessione attiva

Finora sono state create o aperte sessioni. Il passaggio successivo consiste nel *connettersi* a una sessione. Una volta stabilita la connessione, il server di rendering produrrà immagini e invierà un flusso video all'applicazione.

Inserire il codice seguente nello script  *RemoteRendering* e rimuovere le versioni precedenti delle funzioni duplicate:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Per testare questa funzionalità:

1. Premere **Play** (Riproduci) in Unity.
1. Aprire una sessione:
    1. Se è già disponibile una sessione, premere **Query Active Sessions** (Cerca sessioni attive) e quindi **Use Existing Session** (Usa una sessione esistente).
    1. In caso contrario, premere **Create Session** (Crea sessione).
1. Premere **Connect** (Connetti).
1. Dopo alcuni secondi, l'output della console dovrebbe indicare che si è connessi.
1. Per il momento, non succede niente altro.
1. Premere **Disconnect** (Disconnetti) per arrestare la modalità di riproduzione di Unity.

>[!NOTE]
> Più utenti possono *aprire* una sessione per eseguire query sulle relative informazioni, ma solo un utente può essere *connesso* a una sessione alla volta. Se un altro utente è già connesso, la connessione non riesce con un **errore di handshake**.

## <a name="load-a-model"></a>Caricare un modello

Inserire il codice seguente nello script  *RemoteRendering* e rimuovere le versioni precedenti delle funzioni duplicate:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Quando si preme il pulsante di riproduzione, si apre una sessione e ci si connette, viene visualizzato il pulsante **Load Model** (Carica modello). Dopo aver fatto clic sul pulsante, l'output della console visualizzerà lo stato di avanzamento del caricamento e quando raggiungerà il 100% verrà visualizzato il modello di un motore:

![Modello caricato nell'editor](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) è un importante componente usato per la [stabilità degli ologrammi](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Tuttavia, avrà effetto solo quando viene distribuito in un dispositivo di realtà mista.

> [!TIP]
> Se è stata seguita la procedura descritta nell'articolo [Avvio rapido: Convertire un modello per il rendering](../../quickstarts/convert-model.md), si è già appreso come convertire i modelli personalizzati. Per eseguirne il rendering, è sufficiente inserire l'URI di un modello convertito nella proprietà *Model Name*.

## <a name="display-frame-statistics"></a>Visualizzare le statistiche dei fotogrammi

Rendering remoto di Azure tiene traccia di varie informazioni sulla qualità della connessione. Per visualizzare rapidamente queste informazioni, procedere come segue:

Creare un [nuovo script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) con il nome **RemoteFrameStats**. Aprire il file di script e sostituirne l'intero contenuto con il codice seguente:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Creare un GameObject con il nome *FrameStats*. Collegarlo come nodo figlio all'oggetto *Main Camera* (Fotocamera principale) e impostarne la posizione su **x = 0, y = 0, z = 0,325**. Aggiungere il componente **RemoteFrameStats** all'oggetto.

Aggiungere un oggetto figlio **UI > Canvas** (Interfaccia utente > Canvas) all'oggetto *FrameStats* e impostarne le proprietà come segue:

![proprietà del canvas](media/framestats-canvas.png)

Aggiungere un oggetto **UI > Text** (Interfaccia utente > Testo) come elemento figlio del canvas e impostarne le proprietà come segue:

![proprietà del testo](media/framestats-text.png)

Selezionare l'oggetto *FrameStats* e popolare il campo **FrameStats** facendo clic sull'icona del cerchio e selezionando l'oggetto **Text** (Testo):

![impostazione della proprietà del testo](media/framestats-set-text.png)

A questo punto, quando si è connessi alla sessione remota, il testo dovrebbe mostrare le statistiche di streaming:

![output delle statistiche dei fotogrammi](media/framestats-output.png)

Il codice disabilita l'aggiornamento delle statistiche al di fuori dell'editor, perché una casella di testo bloccata costituirebbe un elemento di disturbo. Un'implementazione più sofisticata è disponibile nel progetto [Avvio rapido](../../quickstarts/render-model.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati appresi tutti i passaggi necessari per usare un progetto Unity vuoto con Rendering remoto di Azure. Nell'esercitazione successiva verrà analizzato in dettaglio come usare le entità remote.

> [!div class="nextstepaction"]
> [Esercitazione: Uso di entità remote in Unity](working-with-remote-entities.md)
