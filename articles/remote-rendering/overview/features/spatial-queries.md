---
title: Query spaziali
description: Come eseguire query spaziali in una scena
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680531"
---
# <a name="spatial-queries"></a>Query spaziali

Le query spaziali sono operazioni con le quali è possibile richiedere al servizio di rendering remoto quali oggetti si trovano in un'area. Le query spaziali vengono spesso usate per implementare interazioni, ad esempio individuare l'oggetto a cui un utente sta puntando.

Tutte le query spaziali vengono valutate nel server. Di conseguenza, si tratta di operazioni asincrone e i risultati arrivano con un ritardo che dipende dalla latenza di rete. Poiché ogni query spaziale genera traffico di rete, prestare attenzione a non eseguire troppe operazioni contemporaneamente.

## <a name="collision-meshes"></a>Mesh di collisioni

Le query spaziali sono basate sul motore di [fisica Havok](https://www.havok.com/products/havok-physics) e richiedono la presenza di una mesh di collisione dedicata. Per impostazione predefinita, la [conversione del modello](../../how-tos/conversion/model-conversion.md) genera mesh di collisione. Se non è necessario eseguire query spaziali su un modello complesso, è consigliabile disabilitare la generazione della mesh di collisione nelle [Opzioni di conversione](../../how-tos/conversion/configure-model-conversion.md), in quanto ha un impatto in diversi modi:

* La [conversione del modello](../../how-tos/conversion/model-conversion.md) può richiedere molto più tempo.
* Le dimensioni dei file di modello convertite sono notevolmente maggiori e influiscano sulla velocità di download.
* I tempi di caricamento del runtime sono più lunghi.
* Il consumo di memoria della CPU di runtime è superiore.
* Si verifica un lieve sovraccarico delle prestazioni in fase di esecuzione per ogni istanza del modello.

## <a name="ray-casts"></a>Cast Ray

Un *cast di raggio* è una query spaziale in cui il runtime controlla quali oggetti sono intersecati da un raggio, iniziando da una posizione specificata e puntando a una determinata direzione. Come ottimizzazione, viene anche fornita una distanza massima del raggio, in modo da non cercare oggetti troppo lontani.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

Sono disponibili tre modalità di raccolta riscontri:

* **Più vicino:** In questa modalità verrà segnalato solo il hit più vicino.
* **Qualsiasi:** Preferisci questa modalità quando si vuole sapere *se* un raggio raggiunge qualcosa, ma non importa cosa è stato raggiunto esattamente. Questa query può essere notevolmente più economica per la valutazione, ma dispone anche di poche applicazioni.
* **Tutti:** In questa modalità, tutti i riscontri lungo il raggio vengono segnalati, ordinati in base alla distanza. Non usare questa modalità a meno che non sia effettivamente necessario più del primo hit. Limitare il numero di riscontri segnalati `MaxHits` con l'opzione.

Per escludere gli oggetti in modo selettivo da considerare per i cast di raggio, è possibile usare il componente [HierarchicalStateOverrideComponent](override-hierarchical-state.md) .

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Risultato hit

Il risultato di una query ray cast è una matrice di riscontri. La matrice è vuota, se nessun oggetto è stato raggiunto.

Un hit presenta le proprietà seguenti:

* **HitEntity:** [Entità](../../concepts/entities.md) raggiunta.
* **Sottopartita:** Quale *submesh* è stato raggiunto in un [MeshComponent](../../concepts/meshes.md). Può essere usato per indicizzare `MeshComponent.UsedMaterials` e cercare il [materiale](../../concepts/materials.md) in quel punto.
* **HitPosition:** Posizione dello spazio globale in cui il raggio interseca l'oggetto.
* **HitNormal:** Il normale della superficie di spazio globale della mesh in corrispondenza della posizione dell'intersezione.
* **DistanceToHit:** Distanza dalla posizione iniziale del raggio al riscontro.

## <a name="next-steps"></a>Passaggi successivi

* [Limiti degli oggetti](../../concepts/object-bounds.md)
* [Override degli Stati gerarchici](override-hierarchical-state.md)
