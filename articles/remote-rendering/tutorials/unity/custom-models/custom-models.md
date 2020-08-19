---
title: Interfacce e modelli personalizzati
description: Aggiungere i controller di visualizzazione e inserire i modelli personalizzati di cui eseguire il rendering tramite Rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 3753c809d8222030a885693ede800fe17c08b14b
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224544"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Esercitazione: Interfacce e modelli personalizzati

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Aggiungere Mixed Reality Toolkit al progetto
> * Gestire lo stato dei modelli
> * Configurare Archiviazione BLOB di Azure per l'inserimento di modelli
> * Caricare ed elaborare i modelli per il rendering

## <a name="prerequisites"></a>Prerequisiti

* Questa esercitazione si basa sull'argomento [Esercitazione: Visualizzazione di un modello di cui è stato eseguito il rendering in remoto](../view-remote-models/view-remote-models.md).

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Introduzione a Mixed Reality Toolkit (MRTK)

Mixed Reality Toolkit (MRTK) è un toolkit multipiattaforma per la creazione di esperienze di realtà mista. Verrà usato MRTK 2.3 verrà usato per le funzionalità di interazione e visualizzazione.

Per aggiungere MRTK, seguire i [passaggi necessari](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#required) descritti in [Introduzione a MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html).

I passaggi sono:
 - [Ottenere i pacchetti più recenti di MRTK Unity](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#get-the-latest-mrtk-unity-packages)
     - Anche se sono indicati i pacchetti più recenti, questa esercitazione riguarda la versione 2.3.
     - Per l'esercitazione verrà usato solo il pacchetto *Foundation*. I pacchetti *Extensions*, *Tools* e *Examples* non sono necessari.
 - [Importare i pacchetti MRTK nel progetto Unity](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#import-mrtk-packages-into-your-unity-project)
 - [Passare il progetto Unity alla piattaforma di destinazione](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#switch-your-unity-project-to-the-target-platform)
     - Questo passaggio dovrebbe essere già stato eseguito nel primo capitolo, ma è consigliabile verificarlo.
 - [Aggiungere MRTK a una nuova scena o a un nuovo progetto](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#add-mrtk-to-a-new-scene-or-new-project)
     - È possibile aggiungere MRTK a una nuova scena e aggiungere di nuovo gli oggetti/script di coordinatore e modello oppure è possibile aggiungere MRTK alla scena esistente con il comando di menu *Mixed Reality Toolkit -> Add to Scene and Configure* (Mixed Reality Toolkit ->Aggiungi alla scena e configura).

## <a name="import-assets-used-by-this-tutorial"></a>Importare gli asset usati da questa esercitazione

A partire da questo capitolo, verrà implementato un semplice modello [model-view-controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) per gran parte del materiale trattato. La parte *model* del modello è il codice specifico per Rendering remoto di Azure e la gestione dello stato correlata a Rendering remoto di Azure. Le parti *view* e *controller* del modello vengono implementate tramite asset di MRTK e alcuni script personalizzati. È possibile usare *model* in questa esercitazione senza le parti *view-controller* implementate qui. Questa separazione consente di integrare facilmente il codice di questa esercitazione nella propria applicazione, in cui verrà sostituita la parte *view-controller* del modello di progettazione.

Con l'introduzione di MRTK, sono disponibili diversi script, prefab e asset che è ora possibile aggiungere al progetto per supportare le interazioni e il feedback visivo. Questi asset, chiamati **asset dell'esercitazione**, sono raggruppati in un [pacchetto di Unity](https://docs.unity3d.com/Manual/AssetPackages.html), incluso in [GitHub Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering) in '\Unity\TutorialAssets\TutorialAssets.unitypackage'.

1. Clonare o scaricare il repository git [Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering), se il download estrae lo ZIP in una posizione nota.
1. Nel progetto Unity scegliere *Assets -> Import Package -> Custom Package* (Asset -> Importa pacchetto -> Pacchetto personalizzato).
1. In Esplora file passare alla directory in cui è stato clonato o decompresso il repository Rendering remoto di Azure, quindi selezionare il file con estensione unitypackage in **Unity -> TutorialAssets -> TutorialAssets.unitypackage**
1. Selezionare il pulsante **Import** (Importa) per integrare il contenuto del pacchetto nel progetto.
1. Nell'editor di Unity selezionare *Mixed Reality Toolkit -> Utilities -> Upgrade MRTK Standard Shader for Lightweight Render Pipeline* (Mixed Reality Toolkit -> Utilità -> Aggiorna lo shader standard per la pipeline di rendering leggera) sulla barra dei menu superiore e seguire le istruzioni per aggiornare lo shader.

Dopo aver incluso MRTK e gli asset dell'esercitazione nel progetto, sostituire il profilo MRTK con uno o più adatti per l'esercitazione.

1. Selezionare il GameObject **MixedRealityToolkit** nella gerarchia della scena.
1. Nella finestra di controllo, nel componente **MixedRealityToolkit**, impostare il profilo di configurazione su *ARRMixedRealityToolkitConfigurationProfile*.
1. Premere *CTRL+S* per salvare le modifiche.

Verrà configurato MRTK, principalmente con i profili HoloLens 2 predefiniti. I profili forniti sono preconfigurati nei modi seguenti:
 - Disattivare il profiler. Premere 9 per attivarlo o disattivarlo oppure selezionare "Show/Hide Profiler" (Mostra/nascondi profiler) nel dispositivo.
 - Disattivare il cursore dello sguardo.
 - Abilitare i clic del mouse di Unity, in modo che sia possibile fare clic sugli elementi dell'interfaccia utente di MRTK con il mouse invece che con la mano simulata.

## <a name="add-the-app-menu"></a>Aggiungere il menu di app

La maggior parte dei controller di visualizzazione di questa esercitazione opera su classi di base astratte invece che concrete. Questo modello offre una maggiore flessibilità e consente di fornire i controller di visualizzazione, semplificando al tempo stesso l'apprendimento del codice di Rendering remoto di Azure. Per semplicità, la classe **RemoteRenderingCoordinator** non ha una classe astratta e il relativo controller di visualizzazione funziona direttamente con la classe concreta.

È ora possibile aggiungere il prefab **AppMenu** alla scena, per il feedback visivo dello stato della sessione corrente. Il controller di visualizzazione "sblocca" altri controller di visualizzazione del sottomenu durante l'implementazione e l'integrazione di altre funzionalità di Rendering remoto di Azure nella scena. Per il momento, **AppMenu** avrà un'indicazione visiva dello stato di Rendering remoto di Azure e presenterà il pannello del modello che l'utente usa per autorizzare la connessione dell'applicazione con Rendering remoto di Azure.

1. Individuare il prefab **AppMenu** in *Assets/RemoteRenderingTutorial/Prefabs/AppMenu*
1. Trascinare il prefab **AppMenu** nella scena.
1. È probabile che venga visualizzata una finestra di dialogo per **TMP Importer**, perché è la prima volta che vengono inclusi asset di *Text Mesh Pro* nella scena. Seguire le istruzioni per **importare TMP Essentials**. Chiudere quindi la finestra di dialogo di importazione. Gli esempi e i contenuti aggiuntivi non sono necessari.
1. **AppMenu** è configurato per l'associazione automatica e fornisce la finestra di dialogo modale per acconsentire alla connessione a una sessione, quindi è possibile rimuovere il bypass inserito in precedenza. Nel GameObject **RemoteRenderingCoordinator** rimuovere il bypass per l'autorizzazione implementato in precedenza, premendo il pulsante '-' nell'evento **On Requesting Authorization**.
 ![Rimuovere il bypass](./media/remove-bypass-event.png).
1. Testare il controller di visualizzazione premendo **Play** nell'editor di Unity.
1. Nell'editor, ora che MRTK è configurato, è possibile usare i tasti WASD per cambiare la posizione della visualizzazione e tenere premuto il pulsante destro del mouse spostando contemporaneamente il mouse per cambiare la direzione di visualizzazione. Provare a muoversi nella scena per acquisire familiarità con i controlli.
1. Nel dispositivo è possibile sollevare il palmo per richiamare **AppMenu**. Nell'editor di Unity usare il tasto di scelta rapida 'M'.
1. Se il menu non viene più visualizzato, premere 'M' per richiamarlo. Il menu verrà inserito vicino alla videocamera per facilitare l'interazione.
1. L'autorizzazione verrà ora visualizzata come richiesta a destra di **AppMenu**. Da questo momento in poi verrà usato questo sistema per autorizzare l'app a gestire le sessioni di rendering remoto.
 ![Autorizzazione con l'interfaccia utente](./media/authorize-request-ui.png)
1. Interrompere la riproduzione di Unity per continuare con l'esercitazione.

## <a name="manage-model-state"></a>Gestire lo stato dei modelli

Ora verrà implementato un nuovo script, **RemoteRenderedModel** per il rilevamento dello stato, la risposta agli eventi, la generazione di eventi e la configurazione. Essenzialmente, **RemoteRenderedModel** archivia il percorso remoto per i dati del modello in `modelPath`. Resterà in ascolto delle modifiche di stato in **RemoteRenderingCoordinator** per verificare se deve caricare o scaricare automaticamente il modello che definisce. Il GameObject a cui è collegato **RemoteRenderedModel** sarà l'elemento padre locale del contenuto remoto.

Si noti che lo script **RemoteRenderedModel** implementa **BaseRemoteRenderedModel**, incluso dagli **asset dell'esercitazione**. In questo modo, il controller di visualizzazione modello remoto può essere associato allo script.

1. Creare un nuovo script denominato **RemoteRenderedModel** nella stessa cartella di **RemoteRenderingCoordinator**. Sostituire l'intero contenuto con il codice seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

In termini più semplici, **RemoteRenderedModel** include i dati necessari per caricare un modello (in questo caso l'URI di firma di accesso condiviso o *builtin://* ) e tiene traccia dello stato del modello remoto. Quando è il momento di caricare, viene chiamato il metodo `LoadModel` su **RemoteRenderingCoordinator** e l'entità che contiene il modello viene restituita per riferimento e scaricamento.

## <a name="load-the-test-model"></a>Caricare il modello di test

È ora necessario testare il nuovo script caricando di nuovo il modello di test. Verrà creato un GameObject in cui contenere lo script e che sarà un elemento padre del modello di test.

1. Creare un nuovo GameObject vuoto nella scena e assegnare il nome **TestModel**.
1. Aggiungere lo script *RemoteRenderedModel* a **TestModel**.
![Aggiungere il componente RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Compilare `Model Display Name` e `Model Path` rispettivamente con "*TestModel*" e "*builtin://Engine*".
![Specificare i dettagli del modello](./media/add-model-script.png)
1. Posizionare l'oggetto **TestModel** davanti alla videocamera, nella posizione **x = 0, y = 0, z = 3**.
![Posizionare l'oggetto](./media/test-model-position.png)
1. Assicurarsi che **AutomaticallyLoad** sia attivato.
1. Premere **Play** nell'editor di Unity per testare l'applicazione.
1. Concedere l'autorizzazione facendo clic sul pulsante *Connect* (Connetti) per consentire all'app di creare una sessione. Si connetterà a una sessione e caricherà automaticamente il modello.

Osservare la console mentre l'applicazione avanza attraverso i relativi stati. Tenere presente che il completamento di alcuni stati può richiedere del tempo e lo stato di avanzamento non verrà visualizzato. Alla fine, verranno visualizzati i log dal caricamento del modello e quindi verrà eseguito il rendering del modello di test nella scena.

Provare a spostare e ruotare il GameObject **TestModel** tramite la trasformazione nella finestra di controllo o nella visualizzazione scena. Il modello verrà spostato e ruotato nella visualizzazione gioco.

![Log di Unity](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Provisioning di Archiviazione BLOB di Azure e inserimento del modello personalizzato

A questo punto è possibile provare a caricare il proprio modello. A tale scopo, è necessario configurare Archiviazione BLOB e in Azure caricare e convertire un modello, quindi caricare il modello usando lo script **RemoteRenderedModel**. I passaggi di caricamento del modello personalizzato possono essere tranquillamente ignorati se al momento non è disponibile un proprio modello.

Seguire i passaggi specificati in [Avvio rapido: Convertire un modello per il rendering](../../../quickstarts/convert-model.md). Ignorare la sezione **Inserire un nuovo modello nell'app di esempio di avvio rapido** ai fini di questa esercitazione. Dopo aver inserito l'URI di *firma di accesso condiviso* del modello inserito, continuare con il passaggio successivo.

## <a name="load-and-rendering-a-custom-model"></a>Caricamento e rendering di un modello personalizzato

1. Creare un nuovo GameObject vuoto nella scena e assegnargli un nome simile a quello del modello personalizzato.
1. Aggiungere lo script *RemoteRenderedModel* al GameObject appena creato.
 ![Aggiungere il componente RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Compilare `Model Display Name` con un nome appropriato per il modello.
1. Compilare `Model Path` con l'URI di *firma di accesso condiviso* del modello creato nei passaggi di inserimento precedenti.
1. Posizionare il GameObject davanti alla videocamera, nella posizione **x = 0, y = 0, z = 3**.
1. Assicurarsi che **AutomaticallyLoad** sia attivato.
1. Premere **Play** nell'editor di Unity per testare l'applicazione.

    Si noterà che la console inizia a essere popolata con lo stato corrente e alla fine con i messaggi di avanzamento del caricamento del modello. Il modello personalizzato verrà quindi caricato nella scena.

1. Rimuovere l'oggetto modello personalizzato dalla scena. L'esperienza ottimale per questa esercitazione consiste nell'usare il modello di test. Anche se in Rendering remoto di Azure sono ovviamente supportati più modelli, questa esercitazione è stata scritta per supportare in modo ottimale un singolo modello remoto alla volta.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile caricare i propri modelli in Rendering remoto di Azure e visualizzarli nell'applicazione. Successivamente, verrà illustrato come manipolare i modelli.

> [!div class="nextstepaction"]
> [Passaggio successivo: Manipolazione dei modelli](../manipulate-models/manipulate-models.md)
