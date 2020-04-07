---
title: Entità
description: Definizione di entità nell'ambito dell'API di rendering remoto di AzureDefinition of entities in the scope of the Azure Remote Rendering API
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681948"
---
# <a name="entities"></a>Entità

*Un'entità* rappresenta un oggetto mobile nello spazio ed è il blocco predefinito fondamentale del contenuto sottoposto a rendering remoto.

## <a name="entity-properties"></a>Proprietà delle entità

Le entità hanno una trasformazione definita da una posizione, una rotazione e una scala. Di per sé le entità non hanno alcuna funzionalità osservabile. Al contrario, il comportamento viene aggiunto attraverso i componenti, che sono collegati alle entità. Ad esempio, l'associazione di un [CutPlaneComponent](../overview/features/cut-planes.md) creerà un piano di taglio nella posizione dell'entità.

L'aspetto più importante dell'entità stessa è la gerarchia e la trasformazione gerarchica risultante. Ad esempio, quando più entità sono associate come elementi figlio a un'entità padre condivisa, tutte queste entità possono essere spostate, ruotate e ridimensionate all'unisono modificando la trasformazione dell'entità padre.

Un'entità è di proprietà univoca dell'elemento `Entity.Destroy()`padre, ovvero quando l'elemento padre viene eliminato con , lo sono anche i relativi elementi figlio e tutti i [componenti](components.md)connessi. Pertanto, la rimozione di un modello `Destroy` dalla scena viene eseguita chiamando `AzureSession.Actions.LoadModelAsync()` il nodo radice `AzureSession.Actions.LoadModelFromSASAsync()`di un modello, restituito da o la relativa variante SAS .

Le entità vengono create quando il server carica il contenuto o quando l'utente desidera aggiungere un oggetto alla scena. Ad esempio, se un utente desidera aggiungere un piano di taglio per visualizzare l'interno di una mesh, l'utente può creare un'entità in cui deve esistere il piano e quindi aggiungervi il componente piano di taglio.

## <a name="query-functions"></a>Funzioni di query

Esistono due tipi di funzioni di query sulle entità: chiamate sincrone e asincrone. Le query sincrone possono essere utilizzate solo per i dati presenti nel client e che non implicano molti calcoli. Esempi sono l'esecuzione di query per i componenti, trasformazioni di oggetti relativi o relazioni padre/figlio. Le query asincrone vengono usate per i dati che risiedono solo sul server o che comportano calcoli aggiuntivi che sarebbero troppo costosi per l'esecuzione sul client. Esempi sono le query dei limiti spaziali o le query di meta dati.

### <a name="querying-components"></a>Esecuzione di query sui componenti

Per trovare un componente di `FindComponentOfType`un tipo specifico, utilizzare:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Esecuzione di query sulle trasformazioniQuerying transforms

Le query di trasformazione sono chiamate sincrone sull'oggetto. È importante notare che le trasformazioni sottoposte a query tramite l'API sono trasformazioni dello spazio locale, rispetto all'elemento padre dell'oggetto. Le eccezioni sono oggetti radice, per i quali lo spazio locale e lo spazio globale sono identici.

> [!NOTE]
> Non esiste un'API dedicata per eseguire query sulla trasformazione dello spazio globale di oggetti arbitrari.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Esecuzione di query sui limiti spazialiQuerying spatial bounds

Le query dei limiti sono chiamate asincrone che operano su una gerarchia di oggetti completa, usando un'entità come radice. Vedere il capitolo dedicato sui limiti degli [oggetti](object-bounds.md).

### <a name="querying-metadata"></a>Query dei metadati

I metadati sono dati aggiuntivi archiviati negli oggetti, che vengono ignorati dal server. I metadati dell'oggetto sono essenzialmente un set di coppie (nome, valore), in cui _value_ può essere di tipo numerico, booleano o stringa. I metadati possono essere esportati con il modello.

Le query di metadati sono chiamate asincrone su un'entità specifica. La query restituisce solo i metadati di una singola entità, non le informazioni unite di un sottografico.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

La query avrà esito positivo anche se l'oggetto non contiene metadati.

## <a name="next-steps"></a>Passaggi successivi

* [Componenti](components.md)
* [Limiti dell'oggetto](object-bounds.md)
