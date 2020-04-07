---
title: Modelli
description: Descrive il modello in Rendering remoto di AzureDescribes what a model is in Azure Remote Rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 770c88ddfb44004b76633cbeb726d28e1626a72c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681727"
---
# <a name="models"></a>Modelli

Un *modello* in Rendering remoto di Azure fa riferimento a una rappresentazione completa dell'oggetto, costituita da [entità](entities.md) e [componenti.](components.md) I modelli sono il modo principale per ottenere dati personalizzati nel servizio di rendering remoto.

## <a name="model-structure"></a>Struttura del modello

Un modello ha esattamente [un'entità](entities.md) come nodo radice. Al di sotto di esso può avere una gerarchia arbitraria di entità figlio. Quando si carica un modello, viene restituito un riferimento a questa entità radice.

A ogni entità possono essere collegati [componenti.](components.md) Nel caso più comune, le entità dispongono di *MeshComponents*, che fanno riferimento a [risorse mesh.](meshes.md)

## <a name="creating-models"></a>Creazione di modelli

La creazione di modelli per il runtime viene ottenuta [convertendo](../how-tos/conversion/model-conversion.md) i modelli di input da formati di file quali FBX e GLTF. Il processo di conversione estrae tutte le risorse, ad esempio trame, materiali e immagini, e le converte in formati di runtime ottimizzati. Estrarrà inoltre le informazioni strutturali e le convertirà nella struttura del grafico di entità/componente di ARR.

> [!IMPORTANT]
>
> [La conversione](../how-tos/conversion/model-conversion.md) del modello è l'unico modo per creare [le maglie.](meshes.md) Anche se le mesh possono essere condivise tra entità in fase di esecuzione, non esiste altro modo per ottenere una mesh nel runtime, se non il caricamento di un modello.

## <a name="loading-models"></a>Caricamento dei modelli

Dopo la conversione, un modello può essere caricato dall'archiviazione BLOB di Azure nel runtime.

Esistono due funzioni di caricamento distinte che differiscono in base al modo in cui l'asset viene affrontato nell'archiviazione BLOB:There are two distinct loading functions that differ by the way the asset is addressed in blob storage:

* Il modello può essere risolto in base al relativo URI di accesso condiviso. La funzione `LoadModelFromSASAsync` di `LoadModelFromSASParams`caricamento rilevante è con il parametro . Utilizzare questa variante anche durante il caricamento dei [modelli incorporati.](../samples/sample-model.md)
* Il modello può essere indirizzato direttamente dai parametri di archiviazione BLOB, nel caso in cui [l'archiviazione BLOB sia collegata all'account.](../how-tos/create-an-account.md#link-storage-accounts) La funzione di caricamento `LoadModelAsync` rilevante `LoadModelParams`in questo caso è con il parametro .

I frammenti di codice seguenti illustrano come caricare modelli con entrambe le funzioni. Per caricare un modello usando l'URI di firma di accesso condiviso, usare il codice simile a quello riportato di seguito:To load a model using the SAS URI, use code like the one below:

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

Se si vuole caricare un modello usando direttamente i relativi parametri di archiviazione BLOB, usare codice simile al frammento di codice seguente:If you want to load a model by directly using its blob storage parameters, use code similar to the following snippet:

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

    // ... (identical to the SAS URI snippet above)
}
```

Successivamente è possibile attraversare la gerarchia di entità e modificare le entità e i componenti. Il caricamento dello stesso modello più volte crea più istanze, ognuna con la propria copia della struttura di entità/componente. Poiché le canne, i materiali e le trame sono [risorse condivise,](../concepts/lifetime.md)i relativi dati non verranno comunque caricati di nuovo. Pertanto la creazione di un'istanza di un modello più di una volta comporta un overhead di memoria relativamente ridotto.

> [!CAUTION]
> Tutte le funzioni *Async* in ARR restituiscono oggetti operazione asincrona. È necessario archiviare un riferimento a tali oggetti fino al completamento dell'operazione. In caso contrario, il Garbage Collector di C'è può eliminare l'operazione in anticipo e non può mai terminare. Nel codice di esempio precedente l'uso di *await* garantisce che la variabile locale 'loadOp' contenga un riferimento fino al termine del caricamento del modello. Tuttavia, se si dovesse usare il *Completed* evento invece, è necessario archiviare l'operazione asincrona in una variabile membro.

## <a name="next-steps"></a>Passaggi successivi

* [Entities](entities.md)
* [Maglie](meshes.md)
