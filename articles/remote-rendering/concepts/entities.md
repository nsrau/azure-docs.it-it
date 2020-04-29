---
title: Entità
description: Definizione di entità nell'ambito dell'API per il rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681948"
---
# <a name="entities"></a>Entità

Un' *entità* rappresenta un oggetto mobile nello spazio ed è il blocco predefinito fondamentale del contenuto sottoposto a rendering remoto.

## <a name="entity-properties"></a>Proprietà delle entità

Le entità hanno una trasformazione definita da una posizione, una rotazione e una scala. Le entità stesse non hanno alcuna funzionalità osservabile. Al contrario, il comportamento viene aggiunto tramite i componenti, che sono collegati a entità. Ad esempio, il collegamento di un [CutPlaneComponent](../overview/features/cut-planes.md) creerà un piano di taglio in corrispondenza della posizione dell'entità.

L'aspetto più importante dell'entità stessa è la gerarchia e la trasformazione gerarchica risultante. Ad esempio, quando più entità sono collegate come elementi figlio a un'entità padre condivisa, tutte queste entità possono essere spostate, ruotate e ridimensionate in uniformità modificando la trasformazione dell'entità padre.

Un'entità è di proprietà dell'elemento padre in modo univoco, vale a dire che, quando `Entity.Destroy()`l'elemento padre viene eliminato con, sono i relativi elementi figlio e tutti i [componenti](components.md)connessi. Pertanto, la rimozione di un modello dalla scena viene eseguita chiamando `Destroy` sul nodo radice di un modello, restituito da o `AzureSession.Actions.LoadModelAsync()` dalla relativa variante `AzureSession.Actions.LoadModelFromSASAsync()`SAS.

Le entità vengono create quando il server carica il contenuto o quando l'utente desidera aggiungere un oggetto alla scena. Se, ad esempio, un utente desidera aggiungere un piano taglia per visualizzare l'area interna di una mesh, l'utente può creare un'entità in cui deve esistere il piano e quindi aggiungervi il componente del piano tagliato.

## <a name="query-functions"></a>Funzioni di query

Esistono due tipi di funzioni di query sulle entità: chiamate sincrone e asincrone. Le query sincrone possono essere utilizzate solo per i dati presenti nel client e non implicano un calcolo molto elevato. Esempi di query per componenti, trasformazioni di oggetti relativi o relazioni padre/figlio. Le query asincrone vengono utilizzate per i dati che si trovano solo sul server o implicano calcoli aggiuntivi che sarebbero troppo costosi per l'esecuzione sul client. Esempi sono query sui limiti spaziali o query di metadati.

### <a name="querying-components"></a>Esecuzione di query sui componenti

Per trovare un componente di un tipo specifico, usare `FindComponentOfType`:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Esecuzione di query sulle trasformazioni

Le query di trasformazione sono chiamate sincrone sull'oggetto. È importante notare che le trasformazioni sottoposte a query tramite l'API sono trasformazioni dello spazio locale rispetto all'elemento padre dell'oggetto. Le eccezioni sono oggetti radice, per cui lo spazio locale e lo spazio globale sono identici.

> [!NOTE]
> Non sono disponibili API dedicate per eseguire query sulla trasformazione dello spazio globale degli oggetti arbitrari.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Esecuzione di query sui limiti spaziali

Le query sui limiti sono chiamate asincrone che operano su una gerarchia di oggetti completa, usando un'entità come radice. Vedere il capitolo dedicato sui [limiti degli oggetti](object-bounds.md).

### <a name="querying-metadata"></a>Query dei metadati

I metadati sono dati aggiuntivi archiviati negli oggetti, che vengono ignorati dal server. I metadati dell'oggetto sono essenzialmente un set di coppie (nome, valore), in cui il _valore_ può essere di tipo numerico, booleano o stringa. I metadati possono essere esportati con il modello.

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

La query avrà esito positivo anche se l'oggetto non dispone di metadati.

## <a name="next-steps"></a>Passaggi successivi

* [Componenti](components.md)
* [Limiti degli oggetti](object-bounds.md)
