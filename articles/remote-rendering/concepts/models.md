---
title: Modelli
description: Descrive l'aspetto di un modello nel rendering remoto di Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617944"
---
# <a name="models"></a>Modelli

Un *modello* nel rendering remoto di Azure fa riferimento a una rappresentazione completa dell'oggetto, costituita da [entità](entities.md) e [componenti](components.md). I modelli rappresentano il metodo principale per ottenere dati personalizzati nel servizio di rendering remoto.

## <a name="model-structure"></a>Struttura del modello

Un modello ha esattamente un' [entità](entities.md) come nodo radice. Di seguito è possibile che sia presente una gerarchia arbitraria di entità figlio. Quando si carica un modello, viene restituito un riferimento a questa entità radice.

Ogni entità potrebbe avere [componenti](components.md) collegati. Nel caso più comune, le entità hanno *MeshComponents*, che fanno riferimento a [risorse mesh](meshes.md).

## <a name="creating-models"></a>Creazione di modelli

La creazione di modelli per il runtime viene eseguita [convertendo i modelli di input](../how-tos/conversion/model-conversion.md) dai formati di file, ad esempio FBX e GLTF. Il processo di conversione estrae tutte le risorse, ad esempio trame, materiali e mesh, e le converte in formati di runtime ottimizzati. Estrae anche le informazioni strutturali e le converte nella struttura del grafico di entità/componente di ARR.

> [!IMPORTANT]
>
> La [conversione del modello](../how-tos/conversion/model-conversion.md) è l'unico modo per creare [mesh](meshes.md). Sebbene le maglie possano essere condivise tra entità in fase di esecuzione, non esiste un altro modo per ottenere una mesh nel runtime, ad eccezione del caricamento di un modello.

## <a name="loading-models"></a>Caricamento di modelli

Una volta convertito, un modello può essere caricato dall'archiviazione BLOB di Azure al runtime.

Esistono due funzioni di caricamento distinte che differiscono per il modo in cui l'asset viene risolto nell'archivio BLOB:

* Il modello può essere risolto tramite il relativo URI di firma di accesso condiviso. La funzione di caricamento `LoadModelFromSASAsync` pertinente `LoadModelFromSASParams`è con il parametro. Usare questa variante anche durante il caricamento [di modelli predefiniti](../samples/sample-model.md).
* Il modello può essere risolto direttamente dai parametri di archiviazione BLOB, nel caso in [cui l'archiviazione BLOB sia collegata all'account](../how-tos/create-an-account.md#link-storage-accounts). La funzione di caricamento pertinente in `LoadModelAsync` questo caso `LoadModelParams`è con il parametro.

Nei frammenti di codice seguenti viene illustrato come caricare modelli con una delle due funzioni. Per caricare un modello usando l'URI SAS, usare un codice simile al seguente:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

Se si vuole caricare un modello usando direttamente i relativi parametri di archiviazione BLOB, usare codice simile al frammento di codice seguente:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

Successivamente, è possibile attraversare la gerarchia delle entità e modificare le entità e i componenti. Caricando più volte lo stesso modello vengono create più istanze, ognuna con la propria copia della struttura di entità/componenti. Poiché le mesh, i materiali e le trame sono [risorse condivise](../concepts/lifetime.md), i dati non verranno caricati di nuovo. Quindi, la creazione di un'istanza di un modello più di una volta comporta un sovraccarico di memoria relativamente ridotto.

> [!CAUTION]
> Tutte le funzioni *asincrone* in arr restituiscono oggetti operazione asincrona. È necessario archiviare un riferimento a tali oggetti fino al completamento dell'operazione. In caso contrario, il Garbage Collector C# potrebbe eliminare l'operazione in anticipo e non può mai finire. Nel codice di esempio sopra l'uso di *await* garantisce che la variabile locale ' loadOp ' contenga un riferimento fino al completamento del caricamento del modello. Tuttavia, se invece si utilizzasse l'evento *Completed* , sarebbe necessario archiviare l'operazione asincrona in una variabile membro.

## <a name="next-steps"></a>Passaggi successivi

* [Entities](entities.md)
* [Mesh](meshes.md)
