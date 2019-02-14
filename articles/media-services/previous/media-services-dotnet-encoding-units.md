---
title: Ridimensionare l'elaborazione multimediale aggiungendo unità di codifica - Azure | Documentazione Microsoft
description: Informazioni su come aggiungere unità di codifica mediante .NET
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako;milangada;
ms.openlocfilehash: e97f2c4985d41626829c7899d70391cc155f01b4
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990395"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Come scalare la codifica con .NET SDK
> [!div class="op_single_selector"]
> * [Portale](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Panoramica
> [!IMPORTANT]
> Per altre informazioni sul ridimensionamento dell'elaborazione multimediale, vedere questo argomento di [panoramica](media-services-scale-media-processing-overview.md).
> 
> 

Per cambiare il tipo di unità riservata e il numero di unità riservate di codifica mediante l'SDK per .NET, seguire questa procedura:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Apertura di un ticket di supporto

Per impostazione predefinita, ogni account di Servizi multimediali può includere fino a 10 MRU (Media Reserved Unit) S2 o S3 o 25 MRU S1 e cinque unità riservate di streaming on demand. È possibile richiedere l'applicazione di un limite superiore mediante l'apertura di un ticket di supporto.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

