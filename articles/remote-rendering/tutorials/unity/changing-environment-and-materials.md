---
title: Cambiare l'ambiente e i materiali
description: Esercitazione che illustra come modificare la mappa del cielo e il materiale degli oggetti in una scena Unity.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678682"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Esercitazione: Cambiare l'ambiente e i materiali

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Cambiare la mappa ambientale di una scena.
> * Modificare i parametri del materiale.
> * Caricare trame personalizzate.

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che si abbia familiarità con [Esercitazione: Uso di entità remote in Unity](working-with-remote-entities.md). Tuttavia, è necessario solo un progetto Unity con cui è possibile connettersi alle sessioni e caricare un modello, come illustrato in [Esercitazione: Configurazione di un progetto Unity da zero](project-setup.md).

> [!TIP]
> Il [repository di esempi di Rendering remoto di Azure](https://github.com/Azure/azure-remote-rendering) contiene progetti Unity preparati per tutte le esercitazioni nella cartella *Unity*, che è possibile usare come riferimento.

## <a name="change-the-environment-map"></a>Cambiare la mappa ambientale

Rendering remoto di Azure supporta l'uso di [skybox](../../overview/features/sky.md) (anche detti mappe ambientali) per simulare l'illuminazione ambientale. Questa funzionalità è particolarmente utile quando gli oggetti si basano su *[Physically Based Rendering](../../overview/features/pbr-materials.md)* , come nel caso dei modelli di esempio. Rendering remoto di Azure include anche un'ampia varietà di trame predefinite del cielo, che verranno usate questa esercitazione.

Creare un nuovo script denominato **RemoteSky** e aggiungerlo a un nuovo oggetto GameObject. Aprire il file di script e sostituirlo con il codice seguente:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Si noti che qui sopra viene usata la variante `LoadTextureFromSASAsync` perché vengono caricate trame predefinite. Nel caso di caricamento da [risorse di archiviazione BLOB collegate](../../how-tos/create-an-account.md#link-storage-accounts), usare la variante `LoadTextureAsync`. Per un esempio del funzionamento per i modelli, vedere la [sezione sul caricamento di modelli](../../concepts/models.md#loading-models).

Quando si esegue il codice e si attivano le varie mappe del cielo, si noterà un'illuminazione notevolmente diversa nel modello. Tuttavia, lo sfondo rimarrà nero e non sarà possibile vedere la trama reale del cielo. Questo comportamento è intenzionale, perché il rendering di uno sfondo potrebbe costituire un elemento di disturbo con un dispositivo di realtà aumentata. In un'applicazione corretta, è consigliabile usare trame del cielo simili all'ambiente circostante, perché in questo modo gli oggetti avranno un aspetto più realistico.

## <a name="modify-materials"></a>Modificare i materiali

Nell'esercitazione precedente sono stati usati [componenti di override dello stato](../../overview/features/override-hierarchical-state.md) per cambiare la tonalità di colore degli oggetti selezionati. Per ottenere un effetto simile, in questo caso verrà modificato il [materiale](../../concepts/materials.md) di un oggetto.

Prima di tutto è necessario uno script per selezionare gli oggetti, come è stato fatto nella [seconda esercitazione](working-with-remote-entities.md). Se non è ancora disponibile uno script **RemoteRaycaster**, crearlo ora. Sostituire il contenuto con il codice seguente:

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
}
```

Aggiungere il componente all'oggetto gioco *RemoteRendering*. Serve a selezionare gli oggetti sotto il mouse e ad aggiungere componenti *RemoteModelEntity* agli oggetti selezionati. In questa classe di componenti viene implementata la funzionalità effettiva di modifica dei materiali.

Se non è ancora disponibile uno script **RemoteModelEntity**, crearlo e sostituirne il contenuto con il codice seguente:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Quando si esegue questo codice, gli oggetti su cui passa il puntatore del mouse vengono evidenziati. L'effetto è simile a quello dell'esercitazione 2, ma il modo in cui si ottiene è diverso. Qui si recupera l'elenco di materiali dell'oggetto selezionato e quindi si modifica il primo assegnandovi un colore albedo diverso.

> [!IMPORTANT]
> Tenere presente che il fatto che questo metodo evidenzi o meno le parti corrette di un modello dipende dalla modalità di creazione dello stesso. Funzionerà perfettamente se ogni oggetto usa un solo e unico materiale. Se tuttavia un modello non presenta una relazione 1:1 tra parti e materiali, il codice naive precedente non eseguirà l'operazione corretta.

## <a name="use-a-different-texture"></a>Usare una trama diversa

Le [trame](../../concepts/textures.md) fanno in genere parte di un modello di origine. Durante la [conversione del modello](../../quickstarts/convert-model.md), tutte le trame vengono convertite nel formato di runtime necessario e assemblate nel file di modello finale. Per sostituire una trama in fase di esecuzione, è necessario salvarla in [formato di file DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface) e caricarla in archiviazione BLOB di Azure. Per informazioni su come creare un contenitore BLOB di Azure, vedere questa [guida di avvio rapido](../../quickstarts/convert-model.md). Una volta creato un contenitore BLOB, è possibile aprirlo in Azure Storage Explorer e caricare il file tramite trascinamento della selezione.

Sul lato runtime è possibile inviare un asset di trama nell'archiviazione BLOB in due modi distinti:

* Inviare la trama tramite il relativo URI di firma di accesso condiviso. A tale scopo, fare clic con il pulsante destro del mouse sul file caricato e scegliere "**Ottieni firma di accesso condiviso**" dal menu di scelta rapida. Usare questo URI di firma di accesso condiviso con la variante della funzione `LoadTextureFromSASAsync` (vedere il codice di esempio riportato di seguito).
* Inviare direttamente la trama tramite parametri di archiviazione BLOB, nel caso in cui l'[archiviazione BLOB sia collegata all'account](../../how-tos/create-an-account.md#link-storage-accounts). La funzione di caricamento pertinente in questo caso è `LoadTextureAsync`.

Aprire ora lo script **RemoteModelEntity**, aggiungere il codice seguente e rimuovere le funzioni duplicate:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Eseguire questo codice e passare il puntatore del mouse sul modello. Se nel modello sono presenti coordinate UV appropriate, si noterà che la trama viene visualizzata. In caso contrario, si potrebbe notare solo una modifica del colore.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si conclude la serie introduttiva sull'uso di Rendering remoto di Azure con Unity. Come passaggio successivo, è consigliabile acquisire familiarità con alcuni concetti fondamentali di Rendering remoto di Azure, ad esempio [sessioni](../../concepts/sessions.md), [entità](../../concepts/entities.md) e [modelli](../../concepts/models.md) per approfondire la conoscenza. Sono inoltre disponibili diverse funzionalità, ad esempio [luci](../../overview/features/lights.md), [rendering del contorno](../../overview/features/outlines.md), [override dello stato gerarchico](../../overview/features/override-hierarchical-state.md) e [materiali](../../concepts/materials.md) da esaminare in maggior dettaglio.

> [!div class="nextstepaction"]
> [Oggetti e componenti di gioco Unity](../../how-tos/unity/objects-components.md)
