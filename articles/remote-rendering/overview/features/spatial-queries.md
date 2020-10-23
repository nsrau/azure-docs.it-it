---
title: Query spaziali
description: Come eseguire query spaziali in una scena
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: d1a7baa25497cf1ba697725ac8530bc04c458aa5
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207442"
---
# <a name="spatial-queries"></a>Query spaziali

Le query spaziali sono operazioni che consentono di chiedere al servizio di rendering remoto quali oggetti si trovano in un'area. Le query spaziali vengono spesso usate per implementare interazioni, ad esempio capire a quale oggetto stia puntando un utente.

Tutte le query spaziali vengono valutate nel server. Di conseguenza, si tratta di operazioni asincrone e i risultati giungeranno con un ritardo che dipende dalla latenza di rete. Poiché ogni query spaziale genera traffico di rete, prestare attenzione a non eseguirne troppe contemporaneamente.

## <a name="collision-meshes"></a>Mesh di collisione

Le query spaziali sono basate sul motore [Havok Physics](https://www.havok.com/products/havok-physics) e richiedono la presenza di una mesh di collisione dedicata. Per impostazione predefinita, la [conversione di modelli](../../how-tos/conversion/model-conversion.md) genera mesh di collisione. Se non è necessario eseguire query spaziali su un modello complesso, è consigliabile disabilitare la generazione di mesh di collisione nelle [opzioni di conversione](../../how-tos/conversion/configure-model-conversion.md), in quanto incide sotto vari punti di vista:

* La [conversione di modelli](../../how-tos/conversion/model-conversion.md) richiederà molto più tempo.
* Le dimensioni dei file di modello convertiti sono notevolmente maggiori e questo incide sulla velocità di download.
* I tempi di caricamento in fase di esecuzione sono più lunghi.
* Il consumo di memoria della CPU in fase di esecuzione è superiore.
* Si verifica un lieve sovraccarico delle prestazioni in fase di esecuzione per ogni istanza del modello.

## <a name="ray-casts"></a>Raycast

Un *raycast* è una query spaziale in cui il runtime controlla quali oggetti vengono intersecati da un raggio che parte da una determinata posizione e punta verso una direzione specificata. Come ottimizzazione, viene anche fornita una distanza massima del raggio, in modo da non cercare oggetti troppo lontani.

```cs
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<AzureSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = { 0, 0, 0 };
    rayCast.EndPos = { 0, 0, 1 };
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    ApiHandle<RaycastQueryAsync> castQuery = *session->Actions()->RayCastQueryAsync(rayCast);

    castQuery->Completed([](const ApiHandle<RaycastQueryAsync>& async)
        {
            std::vector<RayCastHit> hits;
            async->GetResult(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        });
}
```


Sono disponibili tre modalità di raccolta delle collisioni (hit):

* **`Closest`:** in questa modalità verrà segnalata solo la collisione più vicina.
* **`Any`:** preferire questa modalità quando si vuole sapere *se* un raggio potrebbe colpire qualcosa, ma non è importante sapere cosa esattamente è stato colpito. La valutazione di questa query può essere notevolmente più economica, ma ha anche poche applicazioni.
* **`All`:** in questa modalità vengono segnalate tutte le collisioni lungo il raggio, ordinate in base alla distanza. Non usare questa modalità a meno che non siano realmente necessarie altre collisioni oltre la prima. Limitare il numero di collisioni segnalate con l'opzione `MaxHits`.

Per escludere in modo selettivo gli oggetti da considerare per i raycast è possibile usare il componente [HierarchicalStateOverrideComponent](override-hierarchical-state.md).

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Risultati

Il risultato di una query raycast è una matrice di collisioni. Se non è stato colpito alcun oggetto, la matrice è vuota.

Una collisione ha le proprietà seguenti:

* **`HitEntity`:** quale [entità](../../concepts/entities.md) è stata colpita.
* **`SubPartId`:** quale *sottomesh* è stata colpita in un [MeshComponent](../../concepts/meshes.md). Si può usare per l'indicizzazione in `MeshComponent.UsedMaterials` e per cercare il [materiale ](../../concepts/materials.md) in quel punto.
* **`HitPosition`:** posizione dello spazio globale in cui il raggio ha intersecato l'oggetto.
* **`HitNormal`:** normale della superficie di spazio globale della mesh nel punto dell'intersezione.
* **`DistanceToHit`:** distanza dalla posizione iniziale del raggio al punto di collisione.

## <a name="api-documentation"></a>Documentazione dell'API

* [C# RemoteManager. RayCastQueryAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.raycastqueryasync)
* [C++ RemoteManager:: RayCastQueryAsync ()](/cpp/api/remote-rendering/remotemanager#raycastqueryasync)

## <a name="next-steps"></a>Passaggi successivi

* [Limiti degli oggetti](../../concepts/object-bounds.md)
* [Override dello stato gerarchico](override-hierarchical-state.md)