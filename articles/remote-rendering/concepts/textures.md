---
title: Trame
description: Flusso di lavoro delle risorse trama
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 27395fe377972f51c849f8a61f51a628612ed54d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202668"
---
# <a name="textures"></a>Trame

Le trame sono una [risorsa condivisa](../concepts/lifetime.md) non modificabile. È possibile caricare trame da una [risorsa di archiviazione BLOB](../how-tos/conversion/blob-storage.md) e applicarle direttamente ai modelli, come illustrato nell'esercitazione [: Modificare l'ambiente e i materiali](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). In genere, tuttavia, le trame faranno parte di un [modello convertito](../how-tos/conversion/model-conversion.md), in cui si fa riferimento ad esse con i [materiali](materials.md).

## <a name="texture-types"></a>Tipi di trama

Tipi di trama diversi hanno casi d'uso diversi:

* Le **trame 2D** vengono usate principalmente nei [materiali](materials.md).
* Le **mappe cubi** possono essere usate per il [cielo](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Formati di trama supportati

Tutte le trame fornite ad ARR devono essere in formato [DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Preferibilmente con mipmap e la compressione della trama. Se si vuole automatizzare il processo di conversione, vedere lo [strumento da riga di comando TexConv](../resources/tools/tex-conv.md).

## <a name="loading-textures"></a>Caricamento delle trame

Quando si carica una trama, è necessario specificarne il tipo previsto. Se il tipo non corrisponde, il caricamento della trama non riesce.
Il caricamento di una trama con lo stesso URI due volte restituirà lo stesso oggetto trama, perché si tratta di una [risorsa condivisa](../concepts/lifetime.md).

Analogamente al caricamento dei modelli, sono disponibili due varianti di indirizzamento di una risorsa trama nella risorsa di archiviazione BLOB di origine:

* La risorsa trama può essere inviata tramite il relativo URI SAS. La funzione di caricamento pertinente è `LoadTextureFromSASAsync` con parametro `LoadTextureFromSASParams`. È consigliabile usare questa variante anche quando si caricano [trame predefinite](../overview/features/sky.md#built-in-environment-maps).
* La trama può essere inviata direttamente tramite parametri della risorsa di archiviazione BLOB, nel caso in cui la [risorsa di archiviazione BLOB sia collegata all'account](../how-tos/create-an-account.md#link-storage-accounts). La funzione di caricamento pertinente in questo caso è `LoadTextureAsync` con parametro `LoadTextureParams`.

Il codice di esempio seguente illustra come caricare una trama tramite il relativo URI SAS (o trama predefinita). Si noti che solo la funzione/parametro di caricamento è diversa per l'altro caso:

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```

A seconda del tipo di trama da usare, è possibile che siano presenti restrizioni per il tipo di trama e il contenuto. La mappa di rugosità di un [materiale PBR](../overview/features/pbr-materials.md), ad esempio, deve essere in scala di grigi.

> [!CAUTION]
> Tutte le funzioni *asincrone* in ARR restituiscono oggetti di operazioni asincrone. È necessario quindi archiviare un riferimento a tali oggetti fino al completamento dell'operazione. In caso contrario, è possibile che il Garbage Collector C# elimini l'operazione in anticipo, che quindi non verrà mai completata. Nel codice di esempio precedente la variabile membro '_textureLoad ' viene usata per mantenere un riferimento fino a quando non viene *completato* l'evento.

## <a name="api-documentation"></a>Documentazione dell'API

* [Classe trama C#](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RemoteManager. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtextureasync)
* [C# RemoteManager. LoadTextureFromSASAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtexturefromsasasync)
* [Classe trama C++](/cpp/api/remote-rendering/texture)
* [C++ RemoteManager:: LoadTextureAsync ()](/cpp/api/remote-rendering/remotemanager#loadtextureasync)
* [C++ RemoteManager:: LoadTextureFromSASAsync ()](/cpp/api/remote-rendering/remotemanager#loadtexturefromsasasync)

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](materials.md)
* [Cielo](../overview/features/sky.md)