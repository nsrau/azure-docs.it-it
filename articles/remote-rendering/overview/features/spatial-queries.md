---
title: Query spaziali
description: Come eseguire query spaziali in una scenaHow to do spatial queries in a scene
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680531"
---
# <a name="spatial-queries"></a>Query spaziali

Le query spaziali sono operazioni con cui è possibile chiedere al servizio di rendering remoto quali oggetti si trovano in un'area. Le query spaziali vengono spesso utilizzate per implementare interazioni, ad esempio per capire a quale oggetto punta un utente.

Tutte le query spaziali vengono valutate sul server. Di conseguenza si tratta di operazioni asincrone e i risultati arriveranno con un ritardo che dipende dalla latenza di rete. Poiché ogni query spaziale genera traffico di rete, fare attenzione a non eseguire troppe query contemporaneamente.

## <a name="collision-meshes"></a>Maglie di collisione

Le query spaziali sono alimentate dal motore [Havok Physics](https://www.havok.com/products/havok-physics) e richiedono la presenza di una mesh di collisione dedicata. Per impostazione predefinita, la [conversione](../../how-tos/conversion/model-conversion.md) del modello genera le dimensioni di collisione. Se non sono necessarie query spaziali su un modello complesso, è consigliabile disabilitare la generazione della mesh di collisione nelle opzioni di [conversione,](../../how-tos/conversion/configure-model-conversion.md)in quanto ha un impatto in diversi modi:

* [La conversione](../../how-tos/conversion/model-conversion.md) del modello richiederà molto più tempo.
* Le dimensioni dei file del modello convertiti sono notevolmente maggiori, con un impatto sulla velocità di download.
* I tempi di caricamento in fase di esecuzione sono più lunghi.
* Il consumo di memoria della CPU di runtime è maggiore.
* C'è un leggero sovraccarico delle prestazioni di runtime per ogni istanza del modello.

## <a name="ray-casts"></a>Cast di raggi

Un cast a *raggi* è una query spaziale in cui il runtime controlla quali oggetti vengono intersecati da un raggio, a partire da una determinata posizione e puntando in una determinata direzione. Come ottimizzazione, viene data anche una distanza massima del raggio, per non cercare oggetti troppo lontani.

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

Ci sono tre modalità di raccolta dei colpi:

* **Più vicino:** In questa modalità, verrà segnalato solo il colpo più vicino.
* **Qualsiasi:** Preferisci questa modalità quando tutto quello che vuoi sapere è *se* un raggio colpirebbe qualsiasi cosa, ma non importa cosa è stato colpito esattamente. Questa query può essere notevolmente più economica da valutare, ma ha anche solo poche applicazioni.
* **Tutti:** In questa modalità, vengono segnalati tutti i colpi lungo il raggio, ordinati per distanza. Non utilizzare questa modalità a meno che non sia necessario più del primo colpo. Limita il numero di `MaxHits` hit segnalati con l'opzione.

Per escludere gli oggetti in modo selettivo dall'essere considerati per i cast di raggi, è possibile utilizzare il componente [HierarchicalStateOverrideComponent.](override-hierarchical-state.md)

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Risultato del colpo

Il risultato di una query di cast a raggi è una matrice di hit. La matrice è vuota, se non è stato raggiunto alcun oggetto.

Un Hit ha le seguenti proprietà:

* **HitEntity:** Quale [entità](../../concepts/entities.md) è stata colpita.
* **SubPartId:** Quale *sottomesh* è stato colpito in un [MeshComponent](../../concepts/meshes.md). Può essere utilizzato `MeshComponent.UsedMaterials` per indicizzare e cercare il [materiale](../../concepts/materials.md) in quel punto.
* **HitPosition:** Posizione dello spazio globale in cui il raggio interseca l'oggetto.
* **HitNormale:** Superficie dello spazio globale normale della mesh nella posizione dell'intersezione.
* **DistanzaToHit:** La distanza dalla posizione di partenza del raggio al colpo.

## <a name="next-steps"></a>Passaggi successivi

* [Limiti dell'oggetto](../../concepts/object-bounds.md)
* [Sostituzione degli stati gerarchici](override-hierarchical-state.md)
