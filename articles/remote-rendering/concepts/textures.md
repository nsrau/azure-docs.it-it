---
title: Trame
description: Flusso di lavoro delle risorse Texture
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681662"
---
# <a name="textures"></a>Trame

Le trame sono una [risorsa condivisa](../concepts/lifetime.md)non modificabile. Le trame possono essere caricate [dall'archiviazione BLOB](../how-tos/conversion/blob-storage.md) e applicate direttamente ai modelli, come illustrato in [Esercitazione: Modifica dell'ambiente e](../tutorials/unity/changing-environment-and-materials.md)dei materiali. In genere, tuttavia, le trame faranno parte di un [modello convertito,](../how-tos/conversion/model-conversion.md)dove vi viene fatto riferimento dai suoi [materiali.](materials.md)

## <a name="texture-types"></a>Tipi di texture

Tipi di texture diversi hanno casi d'uso diversi:

* **Le texture 2D** sono utilizzate principalmente nei [materiali.](materials.md)
* **Le mappe cubo** possono essere utilizzate per il [cielo.](../overview/features/sky.md)

## <a name="supported-texture-formats"></a>Formati di trama supportati

Tutte le trame date ad ARR devono essere in [formato DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Preferibilmente con mipmap e compressione delle trame. Vedere lo strumento da riga di [comando TexConv](../resources/tools/tex-conv.md) se si desidera automatizzare il processo di conversione.

## <a name="loading-textures"></a>Caricamento delle trame

Quando si carica una trama, è necessario specificare il tipo previsto. Se il tipo non corrisponde, il caricamento della trama non riesce.
Il caricamento di una trama con lo stesso URI due volte restituirà lo stesso oggetto trama, in quanto è una [risorsa condivisa.](../concepts/lifetime.md)

Analogamente al caricamento dei modelli, esistono due varianti di indirizzamento di un asset di trama nell'archiviazione BLOB di origine:Similar to loading models, there are two variants of addressing a texture asset in source blob storage:

* L'asset di trama può essere indirizzato dal relativo URI di accesso condiviso. La funzione `LoadTextureFromSASAsync` di `LoadTextureFromSASParams`caricamento rilevante è con il parametro . Utilizzare questa variante anche quando si [caricano le trame incorporate.](../overview/features/sky.md#built-in-environment-maps)
* La trama può essere indirizzata direttamente dai parametri di archiviazione BLOB, nel caso in cui [l'archiviazione BLOB sia collegata all'account.](../how-tos/create-an-account.md#link-storage-accounts) La funzione di caricamento `LoadTextureAsync` rilevante `LoadTextureParams`in questo caso è con il parametro .

Il codice di esempio seguente mostra come caricare una trama tramite il relativo URI di firma di accesso condiviso (o trama incorporata): si noti che solo la funzione/parametro di caricamento è diversa per l'altro caso:The following sample code shows how to load a texture via its SAS URI (or built-in texture) - note that only the loading function/parameter differs for the other case:

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

A seconda di ciò che la trama dovrebbe essere utilizzata per, ci possono essere restrizioni per il tipo di trama e il contenuto. Ad esempio, la mappa di rugosità di un [materiale PBR](../overview/features/pbr-materials.md) deve essere in scala di grigi.

> [!CAUTION]
> Tutte le funzioni *Async* in ARR restituiscono oggetti operazione asincrona. È necessario archiviare un riferimento a tali oggetti fino al completamento dell'operazione. In caso contrario, il Garbage Collector di C'è può eliminare l'operazione in anticipo e non può mai terminare. Nel codice di esempio sopra la variabile membro '_textureLoad' viene usato per contenere un riferimento fino all'arrivo dell'evento *Completed.*

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](materials.md)
* [Cielo](../overview/features/sky.md)
