---
title: Entità
description: Definizione di entità nell'ambito dell'API per il rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c49c7bfaa7714dda902d05537fbe3d8a55d5abe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613915"
---
# <a name="entities"></a>Entità

Un *entità* rappresenta un oggetto mobile nello spazio ed è il blocco predefinito fondamentale del contenuto sottoposto a rendering da remoto.

## <a name="entity-properties"></a>Proprietà delle entità

Le entità subiscono una trasformazione definita in termini di posizione, rotazione e scalabilità. Le entità non possiedono di per sé alcuna funzionalità osservabile. Al contrario, il comportamento viene aggiunto tramite componenti, che sono collegati alle entità. Ad esempio, il collegamento di un componente [CutPlaneComponent](../overview/features/cut-planes.md) crea un piano di taglio in corrispondenza della posizione dell'entità.

L'aspetto più importante dell'entità stessa è rappresentato dalla gerarchia e dalla trasformazione gerarchica risultante. Ad esempio, quando più entità sono collegate come elementi figlio a un'entità padre condivisa, tutte queste entità possono essere spostate, ruotate e ridimensionate all'unisono modificando la trasformazione dell'entità padre. È inoltre `enabled` possibile utilizzare lo stato dell'entità per disattivare la visibilità e le risposte ai cast di Ray per un sottografico completo della gerarchia.

Un'entità è di proprietà esclusiva dell'elemento padre e ciò significa che, quando l'elemento padre viene eliminato definitivamente con `Entity.Destroy()`, vengono eliminati anche i relativi elementi figlio e tutti i [componenti](components.md) connessi. Pertanto, la rimozione di un modello dalla scena viene eseguita chiamando `Destroy` sul nodo radice di un modello, restituito da `AzureSession.Actions.LoadModelAsync()` o dalla relativa variante SAS `AzureSession.Actions.LoadModelFromSASAsync()`.

Le entità vengono create quando il server carica contenuti o quando l'utente desidera aggiungere un oggetto alla scena. Se, ad esempio, un utente desidera aggiungere un piano di taglio per visualizzare l'area interna di una mesh, può creare un'entità in cui deve esistere il piano e quindi aggiungervi il componente del piano tagliato.

## <a name="create-an-entity"></a>Creare un'entità

Per aggiungere una nuova entità alla scena, ad esempio per passarla come oggetto radice per il caricamento di modelli o per alleghirvi componenti, usare il codice seguente:

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Funzioni di query

Esistono due tipi di funzioni di query sulle entità: chiamate sincrone e asincrone. Le query sincrone possono essere usate solo per i dati presenti nel client e non implicano un calcolo molto elevato. Ne sono un esempio le query per componenti, trasformazioni di oggetti relativi o relazioni padre/figlio. Le query asincrone vengono usate per i dati che risiedono solo nel server o implicano calcoli aggiuntivi che sarebbero troppo costosi da eseguire sul client. Ne sono un esempio le query sui limiti spaziali o sui metadati.

### <a name="querying-components"></a>Esecuzione di query sui componenti

Per trovare un componente di un tipo specifico, usare `FindComponentOfType`:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Esecuzione di query sulle trasformazioni

Le query di trasformazione sono chiamate sincrone sull'oggetto. È importante notare che le trasformazioni sottoposte a query tramite l'API sono trasformazioni dello spazio locale rispetto all'elemento padre dell'oggetto. Fanno eccezione gli oggetti radice, per cui lo spazio locale e lo spazio globale sono identici.

> [!NOTE]
> Per eseguire query sulla trasformazione dello spazio globale di oggetti arbitrari non sono disponibili API dedicate.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>Query sui limiti spaziali

Le query sui limiti sono chiamate asincrone che operano su una gerarchia completa di oggetti, usando un'unica entità come radice. Vedere il capitolo dedicato sui [limiti degli oggetti](object-bounds.md).

### <a name="querying-metadata"></a>Query dei metadati

I metadati sono dati aggiuntivi archiviati negli oggetti, che vengono ignorati dal server. I metadati relativi ad oggetti sono essenzialmente un set di coppie (nome/valore), dove il _valore_ può essere di tipo numerico o booleano oppure una stringa. I metadati possono essere esportati con il modello.

Le query sui metadati sono chiamate asincrone su un'entità specifica. La query restituisce solo i metadati di una singola entità, non le informazioni unite di un sottografico.

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

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->GetIsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = query->GetResult();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->GetAsInt64();

            // ...
        }
    });
```

La query avrà esito positivo anche se l'oggetto non dispone di metadati.

## <a name="api-documentation"></a>Documentazione dell'API

* [Classe di entità C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RemoteManager. CreateEntity ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.createentity)
* [Classe di entità C++](https://docs.microsoft.com/cpp/api/remote-rendering/entity)
* [C++ RemoteManager:: CreateEntity ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#createentity)

## <a name="next-steps"></a>Passaggi successivi

* [Componenti](components.md)
* [Limiti degli oggetti](object-bounds.md)
