---
title: Modelli
description: Descrive l'aspetto di un modello in Rendering remoto di Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 7832f999de2f6f16cfe816c061925e371f90662e
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758691"
---
# <a name="models"></a>Modelli

In Rendering remoto di Azure, un *modello* fa riferimento a una rappresentazione completa dell'oggetto, costituita da [entità](entities.md) e [componenti](components.md). Costituisce lo strumento principale con cui è possibile trasmettere dati personalizzati al servizio di rendering remoto.

## <a name="model-structure"></a>Struttura del modello

Un modello ha esattamente un'[entità](entities.md) come nodo radice, al di sotto della quale può avere una gerarchia arbitraria di entità figlio. Quando si carica un modello, all'entità radice viene restituito un riferimento.

A ogni entità possono essere associati alcuni [componenti](components.md) che, nel caso più comune, sono costituiti da *MeshComponents*, che fanno riferimento a [risorse mesh](meshes.md).

## <a name="creating-models"></a>Creazione di modelli

Per creare modelli per il runtime è necessario [convertire modelli di input](../how-tos/conversion/model-conversion.md) da file di tipo FBX e GLTF. Il processo di conversione estrae tutte le risorse, quali trame, materiali e mesh, e le converte in formati di runtime ottimizzati. Estrae anche le informazioni strutturali e le converte nella struttura del grafo entità/componente di Rendering remoto di Azure.

> [!IMPORTANT]
>
> La [conversione di un modello](../how-tos/conversion/model-conversion.md) è l'unico modo per creare [mesh](meshes.md). Sebbene in fase di runtime sia possibile condividere le mesh tra più entità, non esiste altro modo per inserire una mesh nel runtime se non caricando un modello.

## <a name="loading-models"></a>Caricamento di modelli

Dopo essere stato convertito, un modello può essere caricato da Archiviazione BLOB di Azure nel runtime.

Sono disponibili due funzioni di caricamento, che si differenziano per il modo in cui l'asset viene inviato nell'archiviazione BLOB:

* Il modello può essere inviato tramite il relativo URI SAS. La funzione di caricamento pertinente è `LoadModelFromSASAsync` con parametro `LoadModelFromSASParams`. È consigliabile usare questa variante anche quando si caricano [modelli predefiniti](../samples/sample-model.md).
* Il modello può essere inviato direttamente tramite parametri di archiviazione BLOB, nel caso in cui l'[archiviazione BLOB sia collegata all'account](../how-tos/create-an-account.md#link-storage-accounts). La funzione di caricamento pertinente in questo caso è `LoadModelAsync` con parametro `LoadModelParams`.

I frammenti di codice seguenti illustrano come caricare i modelli con le due funzioni. Per caricare un modello usando l'URI SAS, usare un frammento di codice simile al seguente:

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

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

Se invece si vuole caricare un modello usando direttamente i parametri di archiviazione BLOB, usare un frammento di codice simile al seguente:

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

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

Successivamente, è possibile attraversare la gerarchia delle entità e modificare le entità e i componenti. Caricando più volte lo stesso modello vengono create più istanze, ognuna con una copia specifica della struttura entità/componente. Poiché, tuttavia, le mesh, i materiali e le trame sono [risorse condivise](../concepts/lifetime.md), questi dati non vengono caricati di nuovo. Quando si crea più volte l'istanza di un modello, quindi, si verifica un sovraccarico di memoria relativamente ridotto.

> [!CAUTION]
> Tutte le funzioni *asincrone* in Rendering remoto di Azure restituiscono oggetti di operazioni asincrone. È necessario quindi archiviare un riferimento a tali oggetti fino al completamento dell'operazione. In caso contrario, è possibile che il Garbage Collector C# elimini l'operazione in anticipo, che quindi non verrà mai completata. Nel codice di esempio sopra riportato, l'uso di *await* garantisce che la variabile locale 'loadOp' contenga un riferimento fino al completo caricamento del modello. Se invece si usasse l'evento *Completato*, sarebbe necessario archiviare l'operazione asincrona in una variabile membro.

## <a name="next-steps"></a>Passaggi successivi

* [Entità](entities.md)
* [Mesh](meshes.md)
