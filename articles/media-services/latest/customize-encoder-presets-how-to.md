---
title: Codificare una trasformazione personalizzata usando Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo argomento illustrato come usare Servizi multimediali di Azure v3 per codificare una trasformazione personalizzata.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/17/2018
ms.author: juliako
ms.openlocfilehash: d298070877a366d04b2df1ef8ac63b08f8771de0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659570"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Come codificare con una trasformazione personalizzata

Quando si esegue la codifica con Servizi multimediali di Azure, è possibile iniziare rapidamente con uno dei set di impostazioni predefiniti basati sulle procedure consigliate del settore, come illustrato nell'esercitazione [Streaming di file](stream-files-tutorial-with-api.md). In alternativa, si può compilare un set di impostazioni personalizzato per i requisiti di uno specifico scenario o dispositivo. 

> [!Note]
> In Servizi multimediali v3, tutte le velocità in bit di codifica sono in bit al secondo. Questo comportamento è diverso dalle impostazioni predefinite di Media Encoder Standard v2 REST. Ad esempio, una velocità in bit che in v2 viene specificata come 128, nella versione v3 sarà 128000.

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET Core completo usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
L'esempio di set di impostazioni personalizzato si trova nella cartella [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Creare una trasformazione con un set di impostazioni personalizzato 

Quando si crea un nuovo oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto **TransformOutput**, come illustrato nel codice seguente. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. L'oggetto **TransformOutput** seguente crea impostazioni di output personalizzate per codec e livello.

Quando si crea un oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente.  In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md) 
