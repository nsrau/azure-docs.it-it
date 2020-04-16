---
title: Unity oggetti di gioco e componenti
description: Vengono descritti i metodi specifici di Unity per l'utilizzo di entità e componenti di rendering remoto.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409862"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagire con gli oggetti e i componenti di gioco Unity

Il rendering remoto di Azure (ARR) è ottimizzato per un numero elevato di oggetti (vedere [Limitazioni).](../../reference/limits.md) Sebbene sia possibile gestire gerarchie grandi e complesse nell'host, replicarle tutte in Unity su dispositivi a basso consumo non è fattibile.

Pertanto, quando un modello viene caricato nell'host, il rendering remoto di Azure rispecchia le informazioni sulla struttura del modello nel dispositivo client (che incorrerà nel traffico di rete), ma non replica gli oggetti e i componenti in Unity.Therefore, when a model is loaded on the host, Azure Remote Rendering mirrors the information about the model structure on the client device (which will incur network traffic), but does not replicate the objects and components in Unity. Al contrario, si aspetta di richiedere manualmente gli oggetti e i componenti del gioco Unity necessari, in modo da limitare l'overhead a ciò che è effettivamente necessario. In questo modo si ha un maggiore controllo sulle prestazioni lato client.

Di conseguenza, l'integrazione Unity del rendering remoto di Azure include funzionalità aggiuntive per replicare la struttura di rendering remoto su richiesta.

## <a name="load-a-model-in-unity"></a>Caricare un modello in Unity

Quando si carica un modello, si ottiene un riferimento all'oggetto radice del modello caricato. Questo riferimento non è un oggetto di gioco Unity, ma `Entity.GetOrCreateGameObject()`è possibile trasformarlo in uno utilizzando il metodo di estensione . Tale funzione prevede un `UnityCreationMode`argomento di tipo . Se si `CreateUnityComponents`passa , l'oggetto di gioco Unity appena creato verrà inoltre popolato con componenti proxy per tutti i componenti di rendering remoto presenti nell'host. Si consiglia, tuttavia, `DoNotCreateUnityComponents`di preferire , per mantenere l'overhead minimo.

### <a name="load-model-with-task"></a>Carica modello con attività

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Modello di carico con coroutine Unity

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Modello di carico con modello await

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Gli esempi di codice precedenti hanno usato il percorso di caricamento del modello tramite firma di accesso utenti perché viene caricato il modello predefinito. L'indirizzamento del modello `LoadModelAsync` `LoadModelParams`tramite contenitori BLOB (using e ) funziona in modo completamente analogo.

## <a name="remoteentitysyncobject"></a>Oggetto RemoteEntitySyncObject

La creazione di un oggetto `RemoteEntitySyncObject` di gioco Unity aggiunge implicitamente un componente all'oggetto gioco. Questo componente viene utilizzato per sincronizzare la trasformazione dell'entità nel server. Per `RemoteEntitySyncObject` impostazione predefinita, `SyncToRemote()` l'utente deve chiamare in modo esplicito per sincronizzare lo stato Unity locale con il server. L'abilitazione `SyncEveryFrame` consente di sincronizzare automaticamente l'oggetto.

Gli oggetti `RemoteEntitySyncObject` con un oggetto possono fare in modo che i relativi elementi figlio remoti vengano istanziati e visualizzati nell'editor di Unity tramite il pulsante **Mostra elementi figlio.**

![Oggetto RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Componenti wrapper

[I componenti](../../concepts/components.md) collegati alle entità di rendering `MonoBehavior`remoto sono esposti a Unity tramite proxy s. Questi proxy rappresentano il componente remoto in Unity e inoltrano tutte le modifiche all'host.

Per creare componenti di rendering remoto `GetOrCreateArrComponent`proxy, utilizzare il metodo di estensione :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Vite accoppiate

La durata di [un'entità](../../concepts/entities.md) remota e di un oggetto `RemoteEntitySyncObject`di gioco Unity viene accoppiata mentre sono associati tramite un oggetto . Se chiami `UnityEngine.Object.Destroy(...)` con un oggetto di gioco di questo tipo, verrà rimossa anche l'entità remota.

Per distruggere l'oggetto gioco Unity, senza influire sull'entità remota, è necessario chiamare `Unbind()` il `RemoteEntitySyncObject`file .

Lo stesso vale per tutti i componenti proxy. Per eliminare solo la rappresentazione sul `Unbind()` lato client, è necessario chiamare prima il componente proxy:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Configurare Rendering remoto per Unity](unity-setup.md)
* [Esercitazione: Utilizzo di entità remote in UnityTutorial: Working with remote entities in Unity](../../tutorials/unity/working-with-remote-entities.md)
