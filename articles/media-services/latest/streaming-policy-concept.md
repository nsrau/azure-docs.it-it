---
title: Criteri di streaming in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra le caratteristiche dei criteri di streaming e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120218"
---
# <a name="streaming-policies"></a>Criteri di streaming

In Servizi multimediali di Azure v3, i [criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) consentono di definire i protocolli di streaming e le opzioni di crittografia per [StreamingLocators](streaming-locators-concept.md). Servizi multimediali v3 offre che alcuni predefiniti i criteri di Streaming in modo che è possibile usarli direttamente per la versione di valutazione o di produzione. 

Attualmente disponibili Streaming criteri predefiniti:<br/>'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e 'Predefined_MultiDrmStreaming'.

Se si hanno requisiti speciali (ad esempio, se si desidera specificare protocolli diversi, è necessario utilizzare un servizio di distribuzione delle chiavi personalizzato o necessario usare una traccia audio), è possibile creare un criterio personalizzato di Streaming. 

 
> [!IMPORTANT]
> * Le proprietà dei **criteri di streaming** di tipo Datetime sono sempre in formato UTC.
> * È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per altre informazioni, vedere [Quote e limitazioni](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Albero delle decisioni

L'albero delle decisioni seguente consentirà di scegliere un criterio per lo scenario di Streaming predefinito.

Fare clic sull'immagine per visualizzarla a schermo intero.  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>Esempi

### <a name="not-encrypted"></a>Non crittografato

Se si vuole trasmettere il file in-the-clear (non crittografati), impostare i criteri predefiniti di streaming chiaro: a 'Predefined_ClearStreamingOnly' (in .NET, è possibile usare l'enumerazione PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Crittografato 

Se è necessario crittografare i contenuti con la crittografia busta e CENC, impostare i criteri su "Predefined_MultiDrmCencStreaming". Questo criterio indica che si vuole che vengano generate e impostate due chiavi simmetriche (busta e CENC) per il localizzatore. Viene quindi applicata la crittografia per la busta, PlayReady e Widevine (la chiave viene distribuita al client di riproduzione in base alle licenze DRM configurate).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Se si vuole anche crittografare il flusso con CBCS (FairPlay), usare "Predefined_MultiDrmStreaming".

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
* [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](protect-with-aes128.md)
* [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
