---
title: Uso di entità remote in Unity
description: Esercitazione che illustra come usare le entità di Rendering remoto di Azure.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 5d995e9a5cdb6fc18532e0c3533959e9feece908
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021246"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Esercitazione: Uso di entità remote in Unity

[Esercitazione: Configurazione di un progetto Unity da zero](project-setup.md) ha illustrato come configurare un nuovo progetto Unity per l'uso con Rendering remoto di Azure. In questa esercitazione vengono esaminate le funzionalità più comuni necessarie per ogni utente di Rendering remoto di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Selezionare gli oggetti usando il ray casting.
> * Eseguire l'override degli stati degli oggetti, ad esempio le tonalità di colore, lo stato di selezione e la visibilità.
> * Eliminare le entità remote.
> * Spostare le entità remote.
> * Usare i piani di taglio per guardare l'interno degli oggetti.

## <a name="prerequisites"></a>Prerequisiti

* Questa esercitazione si basa su [Esercitazione: Configurazione di un progetto Unity da zero](project-setup.md).

> [!TIP]
> Il [repository di esempi di Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering) contiene progetti Unity preparati per tutte le esercitazioni nella cartella *Unity*, che è possibile usare come riferimento.

## <a name="pick-objects"></a>Selezionare gli oggetti

Per interagire con gli oggetti, per prima cosa è necessario selezionarli sotto il cursore del mouse.

Creare un [nuovo script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) denominato **RemoteRaycaster** e sostituire l'intero contenuto con il codice seguente:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Aggiungere questo componente all'oggetto *RemoteRendering* nella scena.

> [!WARNING]
>
> Il componente *RemoteRaycaster* richiede che un componente *ARRServiceUnity* sia collegato allo stesso oggetto. *ARRServiceUnity* è una classe helper per semplificare l'accesso alle funzionalità di Rendering remoto di Azure. Tuttavia, nella scena può essere presente una sola istanza di questo componente. Assicurarsi pertanto di aggiungere tutti i componenti che richiedono *ARRServiceUnity* allo stesso GameObject.
> Se si vuole accedere alle funzionalità di Rendering remoto di Azure da più oggetti gioco, aggiungere il componente *ARRServiceUnity* solo a uno di essi e farvi riferimento in altri script oppure accedere direttamente alle funzionalità di Rendering remoto di Azure.

Premere Play, connettersi a una sessione e caricare un modello. Ora puntare agli oggetti nella scena e osservare l'output della console. Verrà visualizzato il nome dell'oggetto di ogni parte su cui si passa il puntatore del mouse.

## <a name="highlight-objects"></a>Evidenziare gli oggetti

Come passaggio successivo, è necessario fornire un feedback visivo, ossia le parti di un modello a cui punta l'utente. A tale scopo, collegare [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) all'entità selezionata. Questo componente può essere usato per abilitare o disabilitare le varie caratteristiche di un oggetto. Qui viene usato per impostare una tonalità di colore e per abilitare il [rendering del contorno](../../overview/features/outlines.md).

Creare un altro file script denominato **RemoteModelEntity** e sostituirne il contenuto con il codice seguente:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> Non assegnare questo script a un oggetto gioco di Unity perché verrà assegnato a livello di codice dal codice seguente.

Successivamente, è necessario estendere *RemoteRaycaster* per aggiungere il componente *RemoteModelEntity* all'oggetto appena selezionato.

Aggiungere il codice seguente all'implementazione di **RemoteRaycaster** e rimuovere le funzioni duplicate:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Eseguire il progetto e puntare a un modello. Si ottiene una tonalità rossa e un contorno di selezione bianco.

## <a name="isolate-the-selected-object"></a>Isolare l'oggetto selezionato

Un altro uso di [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) è la possibilità di eseguire l'override della visibilità. In questo modo è possibile isolare un oggetto selezionato dal resto del modello. Aprire lo script **RemoteModelEntity**, aggiungere il codice seguente e rimuovere le funzioni duplicate:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Questo codice si basa sulla presenza di un componente di override dello stato nell'oggetto di primo livello della gerarchia, rendendo invisibile tutti gli oggetti. Esegue quindi di nuovo l'override della visibilità in corrispondenza dell'oggetto selezionato, in modo da rendere visibile un unico oggetto. Pertanto, è necessario creare un componente di override dello stato nell'oggetto radice.

Aprire lo script **RemoteRendering** e inserire il codice seguente all'inizio della funzione *LoadModel*:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Infine, è necessario un modo per attivare e disattivare la visibilità. Aprire lo script **RemoteRaycaster** e sostituire la funzione *Update*:

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Eseguire il codice e fare clic con il pulsante destro del mouse su una parte del modello. Il resto del modello scomparirà e resterà visibile solo la parte evidenziata.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Rimuovere le istanze di GameObject di entità remote

Come si può notare, il codice continua a creare oggetti, ma non li pulisce mai. Questo comportamento è visibile anche nel pannello della gerarchia degli oggetti. Quando si espande la gerarchia di oggetti remoti durante la simulazione, è possibile visualizzare sempre più oggetti ogni volta che si passa il puntatore del mouse su una nuova parte del modello.

La presenza di molti oggetti in una scena influisce negativamente sulle prestazioni. È consigliabile pulire sempre gli oggetti che non sono più necessari.

Inserire il codice seguente nello script **RemoteRaycaster** e rimuovere le funzioni duplicate:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Spostare gli oggetti

Il passaggio successivo consiste nello spostare un oggetto selezionato. Nello script **RemoteRaycaster** inserire questo codice e rimuovere la funzione duplicata:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Se si esegue questo codice, si noterà che non viene eseguita alcuna operazione. Infatti, la modifica della trasformazione di un oggetto non sincronizza automaticamente la modifica dello stato con il server, per motivi di prestazioni. Al contrario, è necessario eseguire manualmente il push di questa modifica dello stato nel server o abilitare **SyncEveryFrame** nel componente *RemoteEntitySyncObject*.

Aprire lo script **RemoteModelEntity** e aggiungere la riga seguente:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Eseguendo di nuovo il codice, dovrebbe essere possibile fare clic su un oggetto e trascinarlo sullo schermo.

## <a name="add-a-cut-plane"></a>Aggiungere un piano di taglio

La funzionalità finale da provare in questa esercitazione consiste nell'uso dei [piani di taglio](../../overview/features/cut-planes.md). Un piano di taglio rimuove parti degli oggetti sottoposti a rendering, consentendo di guardare al loro interno.

Creare un nuovo GameObject nella scena **CutPlane**. Creare un nuovo script denominato **RemoteCutPlane**. Aggiungere il componente al nuovo GameObject.

Aprire il file di script e sostituirne il contenuto con il codice seguente:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Quando si esegue il codice ora, il modello è tagliato aperto rispetto al piano. È possibile selezionare l'oggetto *CutPlane* e spostarlo e ruotarlo nella finestra *Scena*. È possibile attivare e disattivare il piano di taglio disabilitando l'oggetto corrispondente.

## <a name="next-steps"></a>Passaggi successivi

Si è acquisita familiarità con le funzionalità più importanti per interagire con oggetti remoti. Nell'esercitazione successiva verrà illustrata la personalizzazione dell'aspetto di una scena.

> [!div class="nextstepaction"]
> [Esercitazione: Cambiare l'ambiente e i materiali](changing-environment-and-materials.md)
