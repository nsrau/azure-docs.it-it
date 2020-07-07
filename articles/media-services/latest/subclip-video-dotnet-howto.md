---
title: Sottoclip di un video durante la codifica con servizi multimediali di Azure
description: Questo argomento descrive come eseguire il clip di un video quando si esegue la codifica con servizi multimediali di Azure con .NET SDK
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67305164"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Sottoclip di un video durante la codifica con servizi multimediali-.NET

È possibile tagliare o sottotagliare un video quando lo si codifica usando un [processo](https://docs.microsoft.com/rest/api/media/jobs). Questa funzionalità può essere usata con qualsiasi [Trasformazione](https://docs.microsoft.com/rest/api/media/transforms) compilata mediante i set di impostazioni [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o i set di impostazioni [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset).

Nell'esempio C# seguente viene creato un processo che consente di tagliare un video in un asset durante l'invio di un processo di codifica. 

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo argomento, è necessario:

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md)
- Creare una trasformazione e un asset di input e output. Per informazioni su come creare asset di trasformazione e di input e output, vedere l'esercitazione [caricare, codificare e trasmettere in flusso i video usando .NET](stream-files-tutorial-with-api.md) .
- Esaminare l'argomento relativo al [concetto di codifica](encoding-concept.md) .

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
