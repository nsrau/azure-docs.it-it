---
title: Disporre i file per la conversione
description: Suggerimenti sul modo migliore per inserire i file nel contenitore di input.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 13c4b841fe2d0b62472628d9382b5f6ee3d1fa6c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318076"
---
# <a name="laying-out-files-for-conversion"></a>Disporre i file per la conversione

Per elaborare correttamente un asset, il servizio di conversione deve essere in grado di trovare tutti i file di input.
Sono costituiti dal file di asset principale convertito e in genere da altri file a cui fanno riferimento i percorsi all'interno del file di asset.
Alla richiesta di conversione di un asset vengono assegnati due parametri che determinano il modo in cui il servizio di conversione trova questi file: `input.folderPath` (facoltativo) e `input.inputAssetPath` .
Sono completamente documentati nella pagina [API REST di conversione](conversion-rest-api.md) .
Ai fini della definizione dei file, l'aspetto importante da notare è che il `folderPath` determina il set completo di file disponibili per il servizio di conversione durante l'elaborazione dell'asset.

## <a name="placing-files-so-they-can-be-found"></a>Inserimento di file in modo che possano essere trovati

Quando un asset di origine usa file esterni, i percorsi di tali file verranno archiviati all'interno dell'asset.
Il servizio di conversione deve interpretare questi percorsi in una file system diversa dalla file system originale dell'asset.
Se i percorsi vengono archiviati come percorsi relativi e la posizione relativa tra l'asset di origine e il file a cui fa riferimento è invariata, è facile per il servizio di conversione trovare il file a cui si fa riferimento.

> [!Note]
> Si consiglia di inserire i file nel contenitore di input in modo che i percorsi relativi dei file corrispondano a quelli del momento della creazione dell'asset.

> [!Note]
> Preferire la creazione di asset che contengono percorsi relativi.
> L'esercitazione sulla [configurazione dei materiali per 3ds Max](../../tutorials/modeling/3dsmax-material-setup.md) fornisce un esempio 3ds Max su come garantire che un asset usi percorsi relativi.

## <a name="finding-textures"></a>Ricerca di trame

A causa dei diversi modi in cui è possibile generare asset, il servizio di conversione deve essere flessibile.
In particolare, deve gestire le situazioni in cui i percorsi nell'asset e la posizione delle trame non corrispondono esattamente.
Un esempio si verifica quando vengono generate risorse contenenti percorsi assoluti, perché questi percorsi non corrisponderanno mai al file System utilizzato dal servizio di conversione.
Per gestire questa situazione tra gli altri, viene usato un approccio ottimale per individuare le trame.

L'algoritmo per l'individuazione delle trame è il seguente: dato un percorso archiviato in un asset, trovare il suffisso del sottopercorso più lungo che, se usato come percorso relativo dalla posizione dell'asset di origine, è destinato a un file esistente.
Se questo sottopercorso (incluso l'intero percorso) non è destinato a un file, la trama viene considerata mancante.

Si consideri il filesystem escogitato seguente: 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
Se MyFile. fbx fa riferimento a una trama con il percorso relativo `..\Textures\MyAssetTextures\myTexture.png` , il servizio di conversione utilizzerà il file E. Infatti, può utilizzare uno qualsiasi dei file A, C e e, se esistenti, con il file E che è preferibile poiché viene trovato con il suffisso più lungo.
I file B e D non verranno mai usati, perché `Textures\myTexture.png` non fa parte di alcun suffisso del percorso archiviato.
Se l'asset contiene invece i percorsi `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` o `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` , il servizio di conversione sarà in grado di trovare i file a e c se sono presenti (preferendo C su un). Tuttavia, non è possibile trovare questo modo e il file dovrà essere rilocato.
Questo problema può essere risolto spostando la cartella Textures accanto all'asset.

> [!Note]
> Se non vengono trovate trame, un possibile rimedio consiste nel garantire che l'asset sia un elemento di pari livello di un sottoalbero che contiene le trame.

## <a name="next-steps"></a>Passaggi successivi

- [Conversione di modelli](model-conversion.md)
