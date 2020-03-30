---
title: Codifica relè personalizzata con Servizi multimediali v3 .NET - Azure . Documenti Microsoft
description: Questo argomento illustra come usare Servizi multimediali di Azure v3 per codificare una trasformazione personalizzata usando .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068040"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Come codificare con una trasformazione personalizzata - .NETHow to encode with a custom transform - .NET

Quando si esegue la codifica con Servizi multimediali di Azure, è possibile iniziare rapidamente con una delle procedure predefinite predefinite consigliate in base alle procedure consigliate del settore, come illustrato nell'esercitazione [sui file](stream-files-tutorial-with-api.md) di streaming. Puoi anche creare un predefinito personalizzato per il tuo scenario specifico o i requisiti del dispositivo.

## <a name="considerations"></a>Considerazioni

Quando create predefiniti personalizzati, si applicano le seguenti considerazioni:

* Tutti i valori per altezza e larghezza sul contenuto AVC devono essere un multiplo di 4.
* In Servizi multimediali di Azure v3, tutte le velocità in bit di codifica sono in bit al secondo. Questo è diverso dai preset con le nostre API v2, che hanno usato kilobit al secondo come unità. Ad esempio, se la velocità in bit 2 è stata specificata come 128 (kilobit/secondo), in v3 verrà impostata su 128000 (bit/secondo).

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimedialiCreate a Media Services account](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET Core completo usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
L'esempio di set di impostazioni personalizzato si trova nella cartella [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Creare una trasformazione con un set di impostazioni personalizzato 

Quando si crea un nuovo oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. Il **Preset** descrive le istruzioni dettagliate delle operazioni di elaborazione video e/o audio che devono essere utilizzate per generare l'oggetto **TransformOutput**desiderato. L'oggetto **TransformOutput** seguente crea impostazioni di output personalizzate per codec e livello.

Quando si crea un oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente. In Servizi multimediali v3, i metodi **Get** sulle entità restituiscono **null** se l'entità non esiste (un controllo senza distinzione tra maiuscole e minuscole sul nome).

### <a name="example"></a>Esempio

L'esempio seguente definisce un set di output che si desidera generare quando viene utilizzata questa trasformazione. Aggiungiamo innanzitutto un livello AacAudio per la codifica audio e due livelli H264Video per la codifica video. Nei livelli video, assegniamo le etichette in modo che possano essere utilizzate nei nomi dei file di output. Successivamente, vogliamo che l'output includa anche le miniature. Nell'esempio seguente specifichiamo le immagini in formato PNG, generate al 50% della risoluzione del video di input, e a tre timestamp : 25%, 50%, 75 della lunghezza del video di input. Infine, specifichiamo il formato per i file di output - uno per il video e l'audio e un altro per le miniature. Poiché abbiamo più H264Layers, dobbiamo usare macro che producono nomi univoci per ogni livello. Possiamo usare una `{Label}` `{Bitrate}` o macro, l'esempio mostra il primo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md) 
