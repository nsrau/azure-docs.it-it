---
title: Clip secondario di un video durante la codifica con Servizi multimediali di AzureSubclip a video when encoding with Azure Media Services
description: Questo argomento descrive come ritagliare un video in un video durante la codifica con Servizi multimediali di Azure usando .NET SDK
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67305164"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Clip secondario di un video durante la codifica con Servizi multimediali - .NET

Potete tagliare o ritagliare un video durante la codifica utilizzando un [lavoro.](https://docs.microsoft.com/rest/api/media/jobs) Questa funzionalità funziona con qualsiasi [trasformazione](https://docs.microsoft.com/rest/api/media/transforms) compilata utilizzando i predefiniti [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

Nell'esempio in Cè riportato di seguito viene creato un processo che taglia un video in un asset durante l'invio di un processo di codifica. 

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo argomento, è necessario:

- [Creare un account Servizi multimediali di Azure](create-account-cli-how-to.md)
- Creare una trasformazione e un asset di input e output. È possibile vedere come creare una trasformazione e gli asset di input e output nel [caricare, codificare e riprodurre in streaming i video utilizzando .NET](stream-files-tutorial-with-api.md) esercitazione.
- Esaminare l'argomento [Concetto di codifica.](encoding-concept.md)

## <a name="example"></a>Esempio

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Passaggi successivi

[Come codificare con una trasformazione personalizzataHow to encode with a custom transform](customize-encoder-presets-how-to.md) 
