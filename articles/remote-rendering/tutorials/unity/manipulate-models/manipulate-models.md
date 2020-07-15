---
title: Manipolazione dei modelli
description: Manipolare i modelli sottoposti a rendering remoto tramite spostamento, rotazione, dimensionamento e altro ancora
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 182ceca11d6e9b9bbebcf2911de1783cef43dd1a
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207337"
---
# <a name="tutorial-manipulating-models"></a>Esercitazione: Manipolazione dei modelli

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Aggiungere limiti visivi e di manipolazione intorno ai modelli sottoposti a rendering remoto
> * Spostare, ruotare e dimensionare
> * Raycast con query spaziali
> * Aggiungere animazioni semplici per oggetti sottoposti a rendering remoto

## <a name="prerequisites"></a>Prerequisiti

* Questa esercitazione si basa sull'argomento [Esercitazione: Interfacce e modelli personalizzati](../custom-models/custom-models.md).

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Eseguire query sui limiti degli oggetti remoti e applicare i risultati ai limiti locali

Per interagire con gli oggetti remoti, è prima necessaria una rappresentazione locale. I [limiti degli oggetti](../../../concepts/object-bounds.md) sono utili per la manipolazione rapida di oggetti remoti. I limiti remoti possono essere sottoposti a query da Rendering remoto di Azure, usando l'entità locale come riferimento. I limiti vengono sottoposti a query dopo il caricamento del modello nella sessione remota.

I limiti di un modello sono definiti dal riquadro che lo contiene interamente, esattamente come [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html) di Unity, con un centro e dimensioni definiti per gli assi x, y, z. In realtà, si userà **BoxCollider** di Unity per rappresentare i limiti del modello remoto.

1. Creare un nuovo script nella stessa directory di **RemoteRenderedModel** e assegnargli il nome **RemoteBounds**.
1. Sostituire il contenuto dello script con il codice seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private BoundsQueryAsync remoteBoundsQuery = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create a query using the model entity
        private void QueryBounds()
        {
            //Implement me
        }

        // Check the result and apply it to the local Unity bounding box if it was successful
        private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Se in Visual Studio viene visualizzato il messaggio di errore *La funzionalità 'X' non è disponibile in C# 6. Usare la versione 7.0 o versioni successive del linguaggio*, è possibile ignorarlo tranquillamente. Fa riferimento alla generazione di soluzioni e progetti di Unity.

    Questo script deve essere aggiunto allo stesso GameObject dello script che implementa **BaseRemoteRenderedModel**. In questo caso, significa **RemoteRenderedModel**. Analogamente agli script precedenti, questo codice iniziale gestirà tutte le modifiche di stato, gli eventi e i dati correlati ai limiti remoti.

    Rimangono due metodi da implementare: **QueryBounds** e **ProcessQueryResult**. **QueryBounds** recupera i limiti e **ProcessQueryResult** acquisisce il risultato della query e lo applica all'oggetto **BoxCollider** locale.

    Il metodo **QueryBounds** è semplice: invia una query alla sessione di rendering remota e resta in ascolto dell'evento `Completed`.

1. Sostituire il metodo **QueryBounds** con il metodo completato seguente:

    ```csharp
    // Create a query using the model entity
    private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        remoteBoundsQuery.Completed += ProcessQueryResult;
    }
    ```

    Anche **ProcessQueryResult** è semplice. Controllare il risultato per verificare se è riuscito. In caso affermativo, convertire e applicare i limiti restituiti in un formato accettabile da **BoxCollider**.    

1. Sostituire il metodo **ProcessQueryResult** con il metodo completato seguente:

    ```csharp
    // Check the result and apply it to the local Unity bounding box if it was successful
    private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
    {
        if (remoteBounds.IsRanToCompletion)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

A questo punto, quando lo script **RemoteBounds** viene aggiunto allo stesso GameObject di **RemoteRenderedModel**, se necessario viene aggiunto un oggetto **BoxCollider** e quando il modello raggiunge lo stato `Loaded`, i limiti verranno sottoposti automaticamente a query e applicati a **BoxCollider**.

1. Usando il GameObject di **TestModel** creato in precedenza, aggiungere il componente **RemoteBounds**.
1. Verificare che lo script venga aggiunto.

     ![Aggiungere il componente RemoteBounds](./media/remote-bounds-script.png)

1. Eseguire di nuovo l'applicazione. Subito dopo il caricamento del modello, verranno visualizzati i limiti per l'oggetto remoto. Sotto i valori verrà visualizzato qualcosa di simile:

     ![Limiti aggiornati](./media/updated-bounds.png)

A questo punto è disponibile un oggetto **BoxCollider** locale configurato con limiti accurati nell'oggetto Unity. I limiti consentono la visualizzazione e l'interazione usando le stesse strategie applicate per un oggetto sottoposto a rendering in locale, ad esempio script che modificano la trasformazione, la fisica e altro ancora.

## <a name="move-rotate-and-scale"></a>Spostare, ruotare e dimensionare  

Lo spostamento, la rotazione e il dimensionamento di oggetti sottoposti a rendering remoto funzionano allo stesso modo di qualsiasi altro oggetto Unity. **RemoteRenderingCoordinator**, nel relativo metodo `LateUpdate`, chiama `Update` nella sessione attiva. `Update`, tra l'altro, provvede a sincronizzare le trasformazioni delle entità dei modelli locali con le relative controparti remote. Per spostare, ruotare o dimensionare un modello sottoposto a rendering remoto, è necessario solo spostare, ruotare o dimensionare la trasformazione del GameObject che rappresenta il modello remoto. In questo caso, verrà modificata la trasformazione del GameObject padre a cui è associato lo script **RemoteRenderedModel**.

Questa esercitazione usa MRTK per l'interazione degli oggetti. La maggior parte dell'implementazione specifica di MRTK per lo spostamento, la rotazione e il dimensionamento di un oggetto esula dall'ambito di questa esercitazione. È disponibile un controller di visualizzazione dei modelli preconfigurato all'interno di **AppMenu**, nel menu **Model Tools** (Strumenti modello).

1. Assicurarsi che il GameObject **TestModel** creato in precedenza sia nella scena.
1. Assicurarsi che il prefab **AppMenu** sia nella scena.
1. Premere il pulsante Play di Unity per riprodurre la scena e aprire il menu **Model Tools** (Strumenti modello) all'interno di **AppMenu**.
![Controller di visualizzazione](./media/model-with-view-controller.png)

**AppMenu** include il sottomenu **Model Tools** (Strumenti modello) che implementa un controller di visualizzazione per il binding con il modello. Quando il GameObject contiene un componente **RemoteBounds**, il controller di visualizzazione aggiungerà un componente [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html), ovvero un componente MRTK che esegue il rendering di un rettangolo di delimitazione intorno a un oggetto con **BoxCollider**. [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/api/Microsoft.MixedReality.Toolkit.Experimental.UI.ObjectManipulator.html?q=ObjectManipulator), responsabile delle interazioni con la mano. Questi script combinati consentiranno di spostare, ruotare e dimensionare il modello sottoposto a rendering remoto.

1. Spostare il mouse sul pannello del gioco e fare clic al suo interno per attivarlo.
1. Usando la [simulazione della mano di MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation), tenere premuto MAIUSC sinistro.
1. Spostare la mano simulata in modo che il raggio punti al modello di test.

    ![Raggio della mano puntato](./media/handray-engine.png)

1. Tenere premuto il pulsante sinistro del mouse e trascinare il modello per spostarlo.

Il contenuto sottoposto a rendering remoto si sposterà insieme al rettangolo delimitatore. Si potrebbe osservare un ritardo tra il rettangolo delimitatore e il contenuto remoto. Questo ritardo dipenderà dalla latenza e dalla larghezza di banda di Internet.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Raycast e query spaziali dei modelli remoti

Un oggetto BoxCollider intorno ai modelli è indicato per l'interazione con l'intero modello, ma non è sufficientemente dettagliato per l'interazione con le singole parti. Per risolvere questo problema, è possibile usare la funzionalità di [raycasting remoto](../../../overview/features/spatial-queries.md#ray-casts). Si tratta di un'API fornita da Rendering remoto di Azure per eseguire il raycasting nella scena remota e restituire i risultati della collisione in locale. Questa tecnica può essere usata per selezionare le entità figlio di un modello di grandi dimensioni o per ottenere informazioni sui risultati delle collisioni, ad esempio posizione, superficie normale e distanza.

Il modello di test include diverse sottoentità che è possibile sottoporre a query e selezionare. Per il momento, la selezione genererà il nome dell'entità selezionata nella console Unity. Per informazioni su come evidenziare l'entità selezionata, vedere la sezione [Materiali, illuminazione ed effetti](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining).

Creare prima di tutto un wrapper statico intorno alle query di raycast remoto. Questo script accetterà una posizione e una direzione nello spazio Unity, le convertirà nei tipi di dati accettati dal raycast raggio remoto e restituirà i risultati. Lo script userà l'API `RayCastQueryAsync`.

1. Creare un nuovo script denominato **RemoteRaycaster** e sostituirne l'intero contenuto con il codice seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                return await RemoteRenderingCoordinator.CurrentSession.Actions.RayCastQueryAsync(rayCast).AsTask();
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Unity include una classe denominata [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html) e Rendering remoto di Azure una classe denominata [**RayCastHit**](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.raycasthit). La **C** maiuscola è un'importante differenza per evitare errori di compilazione.

    **RemoteRayCaster** fornisce un punto di accesso comune per il raycasting remoto nella sessione corrente. Nello specifico, verrà implementato un Per essere più specifici, verrà implementato un gestore del puntatore MRTK. Lo script implementerà l'interfaccia `IMixedRealityPointerHandler`, che indica a MRTK che si vuole che lo script resti in ascolto di eventi del [puntatore di Realtà mista](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html).

1. Creare un nuovo script denominato **RemoteRayCastPointerHandler** e sostituire il codice con il codice seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

Il metodo di `OnPointerClicked` di **RemoteRayCastPointerHandler** viene chiamato da MRTK quando un puntatore fa clic su un collider come BoxCollider. Successivamente, viene chiamato `PointerDataToRemoteRayCast` per convertire il risultato del puntatore in un punto e in una direzione. Il punto e la direzione vengono quindi usati per eseguire il raycasting remoto nella sessione remota.

![Limiti aggiornati](./media/raycast-local-remote.png)

L'invio di richieste di raycasting al clic è una strategia efficace per l'esecuzione di query sugli oggetti remoti. Tuttavia, non si tratta di un'esperienza utente ideale perché il cursore entra in collisione con BoxCollider, non con il modello stesso.

È anche possibile creare un nuovo puntatore MRTK che esegue il raycasting nella sessione remota con maggiore frequenza. Anche se è un approccio più complesso, l'esperienza utente sarà più soddisfacente. Questa strategia esula dall'ambito di questa esercitazione, ma è possibile vedere un esempio di questo approccio nell'app di presentazione disponibile nel [repository di esempi di Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/AzureRemoteRenderingShowcase).

Al termine di un raycast in **RemoteRayCastPointerHandler**, il risultato `Entity` viene emesso dall'evento `OnRemoteEntityClicked` di Unity. Per rispondere a tale evento, viene creato uno script helper che accetta `Entity` ed esegue un'azione. Per iniziare, ottenere lo script per stampare il nome di `Entity` nel log di debug.

1. Creare un nuovo script denominato **RemoteEntityHelper** e sostituirne il contenuto con il seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. Nel GameObject **TestModel** creato in precedenza, aggiungere il componente **RemoteRayCastPointerHandler** e il componente **RemoteEntityHelper**.
1. Assegnare il metodo `EntityToDebugLog` all'evento `OnRemoteEntityClicked`. Quando il tipo di output dell'evento e il tipo di input del metodo corrispondono, è possibile usare il collegamento di eventi dinamici di Unity, che passerà automaticamente il valore dell'evento al metodo.
    1. Crea un nuovo campo di callback\
    ![Aggiungere il callback](./media/add-callback-remote-entity-clicked.png)
    1. Trascinare il componente **Remote Entity Helper** nel campo dell'oggetto per fare riferimento al GameObject padre\
    ![Assegnare l'oggetto](./media/assign-object.png)
    1. Assegnare `EntityToDebugLog` come callback\
    ![Assegnare il callback](./media/remote-entity-event.png)
1. Premere Play nell'editor di Unity per avviare la scena, connettersi a una sessione remota e caricare il modello di test.
1. Usando la simulazione della mano di MRTK, tenere premuto MAIUSC sinistro.
1. Spostare la mano simulata in modo che il raggio punti al modello di test.
1. Fare un clic lungo per simulare il tocco, eseguendo l'evento `OnPointerClicked`.
1. Cercare nella console di Unity un messaggio di log con il nome dell'entità figlio selezionato. Ad esempio:\
![Esempio di entità figlio](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>Sincronizzazione del grafico di oggetti remoto nella gerarchia di Unity

Fino a questo punto, abbiamo visto solo un singolo GameObject locale che rappresenta l'intero modello. Questa opzione funziona bene per il rendering e la manipolazione dell'intero modello. Se però si vogliono applicare gli effetti o manipolare specifiche sottoentità, è necessario creare GameObject locali per rappresentarle. Prima di tutto, è possibile esplorare manualmente il modello di testo.

1. Avviare la scena e caricare il modello di test.
1. Espandere gli elementi figlio del GameObject **TestModel** nella gerarchia di Unity e selezionare l'elemento GameObject **TestModel_Entity**.
1. Nella finestra di controllo fare clic sul pulsante *Show Children* (Mostra elementi figlio).\
![Mostrare gli elementi figlio](./media/show-remote-children.png)
1. Continuare ad espandere gli elementi figlio nella gerarchia e fare clic su *Show Children* (Mostra elementi figlio) finché non viene visualizzato un elenco di elementi figlio.\
![Tutti gli elementi figlio](./media/test-model-children.png)

Un elenco di decine di entità ora popola la gerarchia. Selezionandone una, nella finestra di controllo verranno visualizzati i componenti `Transform` e `RemoteEntitySyncObject`. Per impostazione predefinita, ogni entità non viene sincronizzata automaticamente con ogni fotogramma, quindi le modifiche locali apportate a `Transform` non vengono sincronizzate con il server. È possibile selezionare *Sync Every Frame* (Sincronizza ogni fotogramma) e quindi spostare, dimensionare o ruotare la trasformazione nella visualizzazione scena, Il modello sottoposto a rendering non verrà visualizzato nella visualizzazione scena. Per visualizzare l'aggiornamento visivo della posizione e della rotazione del modello, guardare la visualizzazione gioco.

Lo stesso processo può essere eseguito a livello di codice ed è il primo passaggio per la modifica di entità remote specifiche.

1. Modificare lo script **RemoteEntityHelper** in modo che contenga anche il metodo seguente:

    ```csharp
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Aggiungere un callback aggiuntivo all'evento **RemoteRayCastPointerHandler** `OnRemoteEntityClicked`, impostandolo su `MakeSyncedGameObject`.\
![Callback aggiuntivo](./media/additional-callback.png)
1. Usando la simulazione della mano di MRTK, tenere premuto MAIUSC sinistro.
1. Spostare la mano simulata in modo che il raggio punti al modello di test.
1. Fare un clic lungo per simulare il tocco, eseguendo l'evento `OnPointerClicked`.
1. Controllare ed espandere la gerarchia per visualizzare un nuovo oggetto figlio, che rappresenta l'entità selezionata.\
![Rappresentazione di GameObject](./media/gameobject-representing-entity.png)\
1. Dopo il test, rimuovere il callback per `MakeSyncedGameObject`, dal momento che verrà incorporato con altri effetti in seguito.

> [!NOTE]
> La sincronizzazione di ogni fotogramma è richiesta solo quando è necessario sincronizzare i dati di trasformazione. La sincronizzazione delle trasformazioni comporta un sovraccarico, quindi è consigliabile eseguirla raramente.

La creazione di un'istanza locale e la relativa sincronizzazione automatica rappresentano il primo passaggio per la manipolazione di sottoentità. Le stesse tecniche usate per manipolare il modello interno possono essere usate anche per le sottoentità. Ad esempio, dopo aver creato un'istanza locale sincronizzata di un'entità, è possibile eseguire una query sui limiti e aggiungere i gestori di manipolazione per consentirne con i raggi della mano dell'utente.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile manipolare e interagire con i modelli sottoposti a rendering remoto. Nell'esercitazione successiva verranno descritte le procedure per modificare i materiali, alterare l'illuminazione e applicare effetti ai modelli sottoposti a rendering remoto.

> [!div class="nextstepaction"]
> [Passaggio successivo: Perfezionamento di materiali, illuminazione ed effetti](../materials-lighting-effects/materials-lighting-effects.md)
