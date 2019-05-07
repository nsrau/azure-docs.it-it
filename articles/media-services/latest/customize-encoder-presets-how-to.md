---
title: Codifica di trasformazione personalizzato con servizi multimediali v3 .NET - Azure | Microsoft Docs
description: Questo argomento illustra come usare servizi multimediali di Azure v3 per codificare un file di trasformazione personalizzato con .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/03/2019
ms.author: juliako
ms.openlocfilehash: 2167a74dc81bdbb2562211cf5c0195a755941d9d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148327"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Come codificare con una trasformazione personalizzata - .NET

Durante la codifica con servizi multimediali di Azure, è possibile iniziare a usare rapidamente uno dei set di impostazioni predefinite consigliate basati sulle procedure consigliate del settore, come illustrato nel [Streaming i file](stream-files-tutorial-with-api.md) esercitazione. È anche possibile creare un set di impostazioni per i requisiti specifici di uno scenario o un dispositivo di destinazione personalizzato.

## <a name="considerations"></a>Considerazioni

Durante la creazione di set di impostazioni personalizzati, si applicano le considerazioni seguenti:

* Tutti i valori per l'altezza e larghezza su contenuto AVC devono essere un multiplo di 4.
* In servizi multimediali di Azure v3, tutte le velocità in bit di codifica sono in bit al secondo. Ciò è diverso dal set di impostazioni con le nostre API v2, che usato kilobit al secondo come unità. Ad esempio, se la velocità in bit nella versione 2 è stato specificato come 128 (kilobit/sec), in v3 valore potrebbe essere impostato su 128000 (bit/sec).

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure.](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET Core completo usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
L'esempio di set di impostazioni personalizzato si trova nella cartella [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Creare una trasformazione con un set di impostazioni personalizzato 

Quando si crea un nuovo oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. Il **preimpostato** descrive le procedure dettagliate di operazioni di elaborazione video e/o audio che devono essere utilizzati per generare il valore desiderato **TransformOutput**. L'oggetto **TransformOutput** seguente crea impostazioni di output personalizzate per codec e livello.

Quando si crea un oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente. In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

### <a name="example"></a>Esempio

L'esempio seguente definisce un set di output che si vuole essere generato quando viene utilizzata questa trasformazione. È prima di tutto aggiungere un livello AacAudio per la codifica audio e due livelli di H264Video per la codifica video. Nei livelli video, abbiamo assegnare etichette in modo che possono essere utilizzati nei nomi dei file di output. Successivamente, si desidera l'output includono anche le anteprime. Nell'esempio seguente viene specificato di immagini in formato PNG, generato al 50% della risoluzione del video di input e tre i timestamp - {25%, 50%, 75} della lunghezza del video di input. Infine, specifichiamo il formato per i file di output: uno per video e audio e l'altro per le anteprime. Poiché sono presenti più H264Layers, è necessario usare le macro che generano nomi univoci per ogni livello. È possibile usare una `{Label}` o `{Bitrate}` (macro), nell'esempio viene illustrato il primo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md) 
