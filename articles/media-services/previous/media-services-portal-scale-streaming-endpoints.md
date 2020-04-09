---
title: Ridimensionare gli endpoint di streaming con il portale di Azure | Microsoft Docs
description: Questa esercitazione descrive i passaggi per ridimensionare gli endpoint di streaming mediante il portale di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 40820170bae275f090c5f898387698fc562e59ae
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985542"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Ridimensionare gli endpoint di streaming con il portale di Azure
## <a name="overview"></a>Panoramica

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere Versione di valutazione gratuita di Azure .For [details,](https://azure.microsoft.com/pricing/free-trial/)see Azure Free Trial . 
> 
> 

Gli endpoint di streaming **Premium** sono ideali per i carichi di lavoro avanzati, in quanto offrono una capacità di larghezza di banda dedicata e scalabile. Per impostazione predefinita, i clienti con un endpoint di streaming **Premium** ottengono un'unità di streaming. L'endpoint di streaming può essere ridimensionato aggiungendo unità di streaming. Ogni unità di streaming fornisce all'applicazione capacità di larghezza di banda aggiuntiva. Per altre informazioni sui tipi di endpoint di streaming e la configurazione della rete CDN, vedere l'argomento [Streaming Endpoint overview](media-services-streaming-endpoints-overview.md) (Panoramica sugli endpoint di streaming).
 
Questo argomento illustra come ridimensionare un endpoint di streaming.

Per informazioni sui prezzi, vedere [Dettagli prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Ridimensionare gli endpoint di streaming

Per modificare il numero di unità di streaming, seguire questa procedura:

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Nella finestra **Impostazioni** selezionare **Endpoint di streaming**.
3. Fare clic sull'endpoint di streaming da ridimensionare. 

    > [!NOTE] 
    > È possibile ridimensionare solo endpoint di streaming **Premium**.

4. Spostare il dispositivo di scorrimento per specificare il numero di unità di streaming.

    ![endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

