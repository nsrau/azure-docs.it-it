---
title: Trame
description: Flusso di lavoro delle risorse trama
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681662"
---
# <a name="textures"></a>Trame

Le trame sono una [risorsa condivisa](../concepts/lifetime.md)non modificabile. È possibile caricare trame dall' [archiviazione BLOB](../how-tos/conversion/blob-storage.md) e applicarle direttamente ai modelli, come illustrato in [esercitazione: modifica dell'ambiente e dei materiali](../tutorials/unity/changing-environment-and-materials.md). In genere, tuttavia, le trame faranno parte di un [modello convertito](../how-tos/conversion/model-conversion.md), dove vi fanno riferimento i [materiali](materials.md).

## <a name="texture-types"></a>Tipi di trama

Tipi di trama diversi hanno casi d'uso diversi:

* le **trame 2D** vengono utilizzate principalmente nei [materiali](materials.md).
* **CubeMaps** può essere usato per [Sky](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Formati di trama supportati

Tutte le trame fornite a ARR devono essere in [formato DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Preferibilmente con mipmap e la compressione della trama. Se si desidera automatizzare il processo di conversione, vedere [lo strumento da riga di comando TexConv](../resources/tools/tex-conv.md) .

## <a name="loading-textures"></a>Caricamento delle trame

Quando si carica una trama, è necessario specificare il tipo previsto. Se il tipo non corrisponde, il caricamento della trama ha esito negativo.
Il caricamento di una trama con lo stesso URI due volte restituirà lo stesso oggetto trama, perché si tratta di una [risorsa condivisa](../concepts/lifetime.md).

Analogamente al caricamento dei modelli, sono disponibili due varianti di indirizzamento di un asset di trama nell'archivio BLOB di origine:

* L'asset di trama può essere risolto tramite il relativo URI di firma di accesso condiviso. La funzione di caricamento `LoadTextureFromSASAsync` pertinente `LoadTextureFromSASParams`è con il parametro. Usare questa variante anche durante il caricamento [di trame predefinite](../overview/features/sky.md#built-in-environment-maps).
* La trama può essere risolta direttamente da parametri di archiviazione BLOB, nel caso in [cui l'archivio BLOB sia collegato all'account](../how-tos/create-an-account.md#link-storage-accounts). La funzione di caricamento pertinente in `LoadTextureAsync` questo caso `LoadTextureParams`è con il parametro.

Il codice di esempio seguente illustra come caricare una trama tramite il relativo URI di firma di accesso condiviso (o trama incorporata). si noti che solo la funzione di caricamento/parametro è diversa per l'altro caso:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
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

A seconda del tipo di trama da usare, è possibile che siano presenti restrizioni per il tipo di trama e il contenuto. La mappa di rugosità di un [materiale PBR](../overview/features/pbr-materials.md) , ad esempio, deve essere in scala di grigi.

> [!CAUTION]
> Tutte le funzioni *asincrone* in arr restituiscono oggetti operazione asincrona. È necessario archiviare un riferimento a tali oggetti fino al completamento dell'operazione. In caso contrario, il Garbage Collector C# potrebbe eliminare l'operazione in anticipo e non può mai finire. Nel codice di esempio precedente alla variabile membro ' _textureLoad ' viene usato per mantenere un riferimento fino a quando non arriva l'evento *completato* .

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](materials.md)
* [Sky](../overview/features/sky.md)
