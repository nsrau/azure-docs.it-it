---
title: Un video di sottoclip durante la codifica con servizi multimediali di Azure
description: In questo argomento viene descritto come un video di sottoclip durante la codifica con servizi multimediali di Azure con .NET SDK
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
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305164"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Un video di sottoclip durante la codifica con servizi multimediali - .NET

È possibile tagliare o un video di sottoclip durante la codifica tramite un [processo](https://docs.microsoft.com/rest/api/media/jobs). Questa funzionalità funziona con qualsiasi [trasformare](https://docs.microsoft.com/rest/api/media/transforms) che viene compilato usando il [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) predefiniti, o il [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) i set di impostazioni.

Il seguente C# esempio viene creato un processo che rimuove un video in un Asset, come invia un processo di codifica. 

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo argomento, è necessario:

- [Creare un account Servizi multimediali di Azure](create-account-cli-how-to.md)
- Creare una trasformazione e input e gli asset di output. È possibile vedere come creare una trasformazione e gli asset di input e outpui del [caricare, codificare e trasmettere in streaming video con .NET](stream-files-tutorial-with-api.md) esercitazione.
- Rivedere le [concetto di codifica](encoding-concept.md) argomento.

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

[Come codificare con una trasformazione personalizzata](customize-encoder-presets-how-to.md) 
