---
title: Perfezionamento di materiali, illuminazione ed effetti
description: Modificare i materiali e l'illuminazione del modello. Aggiungere altri effetti, ad esempio la struttura e i piani di taglio.
author: michael-house
ms.author: v-mihous
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 0379fe460d2bb90d28999a139e3ca9c8b7110eca
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85566710"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>Esercitazione: Perfezionamento di materiali, illuminazione ed effetti

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Evidenziare e strutturare i modelli e i relativi componenti
> * Applicare materiali diversi ai modelli
> * Sezionare i modelli con i piani di taglio
> * Aggiungere animazioni semplici per oggetti sottoposti a rendering remoto

## <a name="prerequisites"></a>Prerequisiti

* Questa esercitazione si basa sull'argomento [Esercitazione: Manipolazione dei modelli](..\manipulate-models\manipulate-models.md).

## <a name="highlighting-and-outlining"></a>Evidenziazione e struttura

Una parte importante dell'esperienza utente di tutte le applicazioni consiste nel fornire un feedback visivo all'utente. Rendering remoto di Azure prevede meccanismi di feedback visivo tramite gli [override degli stati gerarchici](../../../overview/features/override-hierarchical-state.md). Gli override degli stati gerarchici vengono implementati con i componenti collegati a istanze locali dei modelli. La creazione di queste istanze locali viene descritta nell'argomento [Sincronizzazione del grafico di oggetti remoto nella gerarchia di Unity](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy).

Prima di tutto, verrà creato un wrapper intorno all componente [**HierarchicalStateOverrideComponent**](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent). Il componente **HierarchicalStateOverrideComponent** è lo script locale che controlla gli override nell'entità remota. Gli [**asset dell'esercitazione**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial) includono una classe di base astratta denominata **BaseEntityOverrideController**, che verrà estesa per creare il wrapper.

1. Creare un nuovo script denominato **EntityOverrideController** e sostituirne l'intero contenuto con il codice seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

Il compito principale di **LocalOverride** consiste nel creare un collegamento tra se stesso e il relativo componente `RemoteComponent`. **LocalOverride** quindi consente di impostare i flag di stato sul componente locale, che sono associati all'entità remota. Gli override e i relativi stati vengono descritti nella pagina [Override degli stati gerarchici](../../../overview/features/override-hierarchical-state.md). 

Questa implementazione imposta un solo stato alla volta. Tuttavia, è possibile combinare più override in singole entità e creare combinazioni a livelli diversi nella gerarchia. Se ad esempio si combinano `Selected` e `SeeThrough` in un singolo componente, quest'ultimo ottiene una struttura e diventa anche trasparente. Oppure, se si impostano l'override `Hidden` dell'entità radice su `ForceOn` e l'override `Hidden` dell'entità figlio su `ForceOff`, verrà nascosto tutto tranne l'entità figlio con l'override.

Per applicare gli stati alle entità, è possibile modificare la classe **RemoteEntityHelper** creata in precedenza.

1. Modificare la classe **RemoteEntityHelper** per implementare la classe astratta **BaseRemoteEntityHelper**. Questa modifica consentirà l'uso di un controller di visualizzazione fornito negli **asset dell'esercitazione**. Dopo la modifica si otterrà quanto segue:

    ```csharp
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. Eseguire l'override dei metodi astratti usando il codice seguente:

    ```csharp
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

Questo codice assicura che venga aggiunto un componente **EntityOverrideController** all'entità di destinazione, quindi chiama uno dei metodi di attivazione. Se lo si desidera, nel GameObject **TestModel** la chiamata a questi metodi helper può essere eseguita aggiungendo **RemoteEntityHelper** come callback all'evento `OnRemoteEntityClicked` nel componente **RemoteRayCastPointerHandler**.

![Callback di puntatori](./media/pointer-event-callbacks.png)

Ora che questi script sono stati aggiunti al modello, dopo la connessione al runtime, il controller di visualizzazione **AppMenu** dovrebbe includere altre interfacce abilitate per l'interazione con lo script **EntityOverrideController**. Per vedere i controller di visualizzazione sbloccati, aprire il menu **Model Tools** (Strumenti del modello).

A questo punto, i componenti del GameObject **TestModel** avranno un aspetto simile al seguente:

![Modello di test con script aggiuntivi](./media/test-model-updated.png)

Ecco un esempio di override raggruppati in una singola entità. Sono stati usati `Select` e `Tint` per fornire una struttura e una colorazione:

![Selezione della tinta del modello di test](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>Piani di taglio

I [piani di taglio](../../../overview/features/cut-planes.md) sono una funzionalità che è possibile aggiungere a qualsiasi entità remota. Nella maggior parte dei casi, si crea una nuova entità remota non associata a dati di mesh in cui contenere il componente piano di taglio. La posizione e l'orientamento del piano tagliato sono determinati dalla posizione e dall'orientamento dell'entità remota a cui è collegato.

Verrà creato uno script che crea automaticamente un'entità remota, aggiunge un componente piano di taglio e sincronizza la trasformazione di un oggetto locale con l'entità del piano di taglio. Quindi, è possibile usare **CutPlaneViewController** per eseguire il wrapping del piano di taglio in un'interfaccia che consentirà di modificarlo.

1. Creare un nuovo script denominato **RemoteCutPlane** e sostituirne il codice con quello seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    Questo codice estende la classe **BaseRemoteCutPlane** inclusa negli **asset dell'esercitazione**. Analogamente al modello sottoposto a rendering remoto, questo script collega e resta in ascolto di modifiche di `RemoteRenderingState` da parte del coordinatore remoto. Quando il coordinatore raggiunge lo stato `RuntimeConnected`, proverà a connettersi automaticamente, se previsto. È disponibile anche una variabile `CutPlaneComponent` che verrà tracciata. Si tratta del componente di Rendering remoto di Azure che esegue la sincronizzazione con il piano di taglio nella sessione remota. Ecco le procedure per creare il piano di taglio.

2. Sostituire il metodo `CreateCutPlane()` con la versione completa seguente:

    ```csharp
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    Viene creata un'entità remota che viene quindi associata a un GameObject locale. Per assicurare che la trasformazione dell'entità remota verrà sincronizzata con la trasformazione locale, impostare `SyncEveryFrame` su `true`. Usare quindi la chiamata a `CreateComponent` per aggiungere `CutPlaneComponent` all'oggetto remoto. Infine, configurare il piano di taglio con le impostazioni definite all'inizio di MonoBehaviour. Ecco la procedura per pulire un piano di taglio implementando il metodo `DestroyCutPlane()`.

3. Sostituire il metodo `DestroyCutPlane()` con la versione completa seguente:

    ```csharp
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

Poiché l'oggetto remoto è piuttosto semplice e viene pulita solo l'estremità remota, mantenendo l'oggetto locale, è possibile chiamare semplicemente `Destroy` sull'oggetto remoto e cancellare il riferimento a tale oggetto.

**AppMenu** include un controller di visualizzazione che si collegherà automaticamente al piano di taglio con cui sarà possibile interagire. Non è necessario usare **AppMenu** o uno qualsiasi dei controller di visualizzazione, ma offrono un'esperienza migliore. Testare ora il piano di taglio e il controller di visualizzazione.

1. Creare un nuovo GameObject vuoto nella scena e assegnare il nome **CutPlane**.
1. Aggiungere il componente **RemoteCutPlane** al GameObject **CutPlane**.

   ![Configurazione del componente piano di taglio](./media/cut-plane-config.png)

1. Premere Play nell'editor di Unity per caricare e connettersi a una sessione remota.
1. Usando la simulazione della mano di MRTK, afferrare e ruotare (tenere premuto CTRL per ruotare) l'oggetto CutPlane per spostarlo nella scena. Notare che seziona **TestModel** per esporne i componenti interni.

![Esempio di piano di taglio](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>Configurazione dell'illuminazione remota

La sessione di rendering remoto supporta un'ampia gamma di [opzioni di illuminazione](../../../overview/features/lights.md). Verranno creati gli script per la [trama del cielo](../../../overview/features/sky.md) e una semplice mappa per due tipi di luce di Unity da usare con il rendering remoto.

### <a name="sky-texture"></a>Trama del cielo

Per cambiare la trama del cielo, sono disponibili diverse mappe cubi tra cui scegliere. Vengono caricate nella sessione e applicate alla trama del cielo. È anche possibile [caricare trame personalizzate](../../../concepts/textures.md) da usare come luce del cielo.

Verrà creato uno script **RemoteSky** che include un elenco delle mappe cubi predefinite disponibili sotto forma di parametri di caricamento. Quindi, si consentirà all'utente di selezionare e caricare una delle opzioni.

1. Creare un nuovo script denominato **RemoteSky** e sostituirne l'intero contenuto con il codice seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSASParams> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSASParams> builtInTextures = new Dictionary<string, LoadTextureFromSASParams>()
        {
            {"Autoshop",new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    La parte più importante di questo codice è costituita da poche righe:

    ```csharp
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    Qui si ottiene un riferimento alla trama da usare caricandola nella sessione dall'archivio BLOB predefinito. Quindi, è necessario assegnare tale trama all'oggetto `SkyReflectionTexture` della sessione per applicarla.

1. Creare un GameObject vuoto nella scena e assegnare il nome **SkyLight**.

1. Aggiungere lo script **RemoteSky** al GameObject **SkyLight**.

    Il passaggio tra luci del cielo può essere eseguito chiamando `SetSky` con una delle chiavi di stringa definite in `AvailableCubemaps`. Il controller di visualizzazione incorporato in **AppMenu** crea automaticamente i pulsanti e collega i relativi eventi per chiamare `SetSky` con la rispettiva chiave.
1. Premere Play nell'editor di Unity e autorizzare una connessione.
1. Dopo la connessione del runtime locale a una sessione remota, passare a **AppMenu -> Session Tools -> Remote Sky** (AppMenu -> Strumenti sessione -> Cielo remoto) per esplorare le diverse opzioni relative al cielo e vedere come influiscono su **TestModel**.

### <a name="scene-lights"></a>Luci della scena

Le luci della scena remota includono la luce puntiforme, spot e direzionale. Analogamente al piano di taglio creato in precedenza, queste luci della scena sono entità remote con componenti collegati. Una considerazione importante da fare per l'illuminazione della scena remota è il tentativo di abbinare l'illuminazione nella scena locale. Questa strategia non è sempre possibile perché molte applicazioni Unity per HoloLens 2 non usano il rendering fisico per gli oggetti sottoposti a rendering locale. Tuttavia, a un certo livello, è possibile simulare l'illuminazione predefinita più semplice di Unity.

1. Creare un nuovo script denominato **RemoteLight** e sostituirne il codice con quello seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    Questo script crea diversi tipi di luci remote a seconda del tipo di luce di Unity locale a cui è collegato. La luce remota duplicherà la luce locale per quanto riguarda la posizione, la rotazione, il colore e l'intensità. Laddove possibile, la luce remota imposterà anche una configurazione aggiuntiva. Non si tratta di una corrispondenza perfetta poiché le luci di Unity non sono luci di rendering fisico.

1. Trovare il GameObject **DirectionalLight** nella scena. Se l'oggetto **DirectionalLight** predefinito è stato rimosso dalla scena, sulla barra dei menu superiore selezionare *GameObject -> Light -> DirectionalLight* per creare una nuova luce nella scena.

1. Selezionare il GameObject **DirectionalLight** e usare il pulsante **Add Component** (Aggiungi componente) per aggiungere lo script **RemoteLight**.

1. Poiché questo script implementa la classe di base `BaseRemoteLight`, è possibile usare il controller di visualizzazione **AppMenu** fornito per interagire con la luce remota. Passare a **AppMenu -> Session Tools -> Directional Light** (AppMenu -> Strumenti sessione -> Luce direzionale).

    > [!NOTE]
    > L'interfaccia utente in **AppMenu** è stata limitata a una singola luce direzionale per semplicità. Tuttavia, è comunque possibile e consigliabile aggiungere luci puntiformi e collegarvi lo script **RemoteLight**. Queste luci aggiuntive possono essere cambiate modificando le proprietà della luce di Unity nell'editor. Sarà necessario sincronizzare manualmente le modifiche locali con la luce remota usando il menu di scelta rapida **RemoteLight** nella finestra di controllo:
    >
    > ![Sincronizzazione manuale della luce remota](./media/sync-remote-light.png)

1. Premere Play nell'editor di Unity e autorizzare una connessione.

1. Dopo la connessione del runtime a una sessione remota, posizionare e puntare la videocamera (usare i tasti WASD e fare clic con il pulsante destro del mouse + spostamento del mouse) per avere in primo piano il controller di visualizzazione della luce direzionale. 
1. Usare il controller di visualizzazione della luce remota per modificare le proprietà della luce. Usando la simulazione della mano di MRTK, afferrare e ruotare (tenere premuto CTRL per ruotare) la luce direzionale per vedere l'effetto sull'illuminazione della scena.

    ![Luce direzionale](./media/directional-light-test.png)

## <a name="editing-materials"></a>Materiali di modifica

È possibile modificare i [materiali](../../../concepts/materials.md) sottoposti a rendering remoto per aggiungere altri effetti visivi, ottimizzare gli oggetti visivi dei modelli sottoposti a rendering o fornire altro feedback agli utenti. Esistono molti modi e molti motivi per modificare un materiale. Qui viene illustrato come cambiare il colore dell'albedo di un materiale, oltre alla rugosità e alla trama metallica del materiale sottoposto a rendering fisico.

> [!NOTE]
> In molti casi, se è possibile implementare una funzionalità o un effetto usando **HierarchicalStateOverrideComponent**, è consigliabile procedere in questo modo invece di modificare il materiale.

Verrà creato uno script che accetta un'entità di destinazione e configura alcuni oggetti `OverrideMaterialProperty` per cambiare le proprietà del materiale dell'entità di destinazione. Per iniziare, ottenere [**MeshComponent**](../../../concepts/meshes.md#meshcomponent) dell'entità di destinazione, che contiene un elenco di materiali usati nel mesh. Per semplicità, verrà usato solo il primo materiale trovato. Questa strategia semplice può non riuscire molto facilmente a seconda del modo in cui è stato creato il contenuto, quindi è consigliabile adottare un approccio più complesso per selezionare il materiale appropriato.

Dal materiale è possibile accedere a valori comuni come l'albedo. È necessario prima di tutto eseguire il cast dei materiali nel tipo appropriato, `PbrMaterial` o `ColorMaterial`, per recuperare i relativi valori, come nel metodo **GetMaterialColor**. Una volta ottenuto un riferimento al materiale desiderato, è sufficiente impostare i valori e Rendering remoto di Azure eseguirà la sincronizzazione tra le proprietà del materiale locale e il materiale remoto.

1. Creare uno script denominato **EntityMaterialController** e sostituirne l'intero contenuto con il codice seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

Il tipo `OverrideMaterialProperty` deve essere sufficientemente flessibile da consentire la modifica di alcuni altri valori dei materiali, se necessario. Il tipo `OverrideMaterialProperty` tiene traccia dello stato di un override, mantiene il valore precedente e quello nuovo e usa un delegato per impostare l'override. Per un esempio, vedere `ColorOverride`:

```csharp
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

Viene creato un nuovo oggetto `OverrideMaterialProperty` in cui l'override eseguirà il wrapping del tipo `Color`. Al momento della creazione dell'override, viene fornito il colore corrente o originale. Viene anche fornito un materiale di Rendering remoto di Azure su cui agire. Infine, viene fornito un delegato che applicherà l'override. Il delegato è un metodo che accetta un materiale e il tipo di Rendering remoto di Azure di cui l'override esegue il wrapping. Questo metodo è la parte più importante per comprendere il modo in cui Rendering remoto di Azure regola i valori dei materiali.

`ColorOverride` usa il metodo `ApplyMaterialColor` per eseguire le operazioni seguenti:

```csharp
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

Questo codice accetta un materiale e un colore. Verifica il tipo di materiale che viene quindi ne esegue il cast per applicare il colore.

`RoughnessOverride` e `MetalnessOverride` funzionano in modo analogo, usando i metodi `ApplyRoughnessValue` e `ApplyMetalnessValue` per svolgere le proprie attività.

Successivamente, verrà testato il controller di materiali.

1. Aggiungere lo script **EntityMaterialController** al GameObject **TestModel**.
1. Premere Play in Unity per avviare la scena e connettersi a Rendering remoto di Azure.
1. Dopo la connessione del runtime a una sessione remota e il caricamento del modello, passare a **AppMenu -> Model Tools -> Edit Material** (AppMenu -> Strumenti modello -> Modifica materiale)
1. Selezionare un'entità dal modello usando le mani simulate per fare clic su **TestModel**.
1. Verificare che il controller di visualizzazione dei materiali (**AppMenu-> Model Tools-> Edit Material** - AppMenu -> Strumenti modello -> Modifica materiale) sia stato aggiornato all'entità di destinazione.
1. Usare il controller di visualizzazione dei materiali per modificare il materiale nell'entità di destinazione.

Poiché viene modificato solo il primo materiale della mesh, la modifica potrebbe non essere visibile. Usare l'override gerarchico **SeeThrough** per verificare se il materiale che si sta modificando si trova all'interno della mesh.

![Esempio di modifica del materiale](./media/material-edit-example.png)

## <a name="next-steps"></a>Passaggi successivi

Congratulazioni! A questo punto sono state implementate tutte le funzionalità di base di Rendering remoto di Azure. Nel capitolo successivo verrà illustrato come proteggere Rendering remoto di Azure e Archiviazione BLOB. Saranno i primi passaggi per rilasciare un'applicazione commerciale che usa Rendering remoto di Azure.

> [!div class="nextstepaction"]
> [Passaggio successivo: Protezione di Rendering remoto di Azure e dello spazio di archiviazione dei modelli](../security/security.md)