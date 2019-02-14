---
title: Creare un input del processo di Servizi multimediali di Azure da un URL HTTPS | Microsoft Docs
description: Questo argomento illustra come creare un input del processo da un URL HTTP(s).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 6919bf49e46a65a02d2444277c8005fbb6f4ca1f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991651"
---
# <a name="create-a-job-input-from-an-https-url"></a>Creare un input del processo da un URL HTTPS

In Servizi multimediali v3, quando si inviano processi per elaborare i video, è necessario indicare a Servizi multimediali dove trovare il video di input. Una delle opzioni consiste nello specificare come processo di input un URL HTTP(s), come illustrato in questo esempio. Attualmente AMS v3 non supporta la codifica di trasferimenti in blocchi su URL HTTPS. Per un esempio completo, vedere questo [esempio di GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Esempio .NET

Il codice seguente mostra come creare un processo da un input URL HTTP(s).

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Passaggi successivi

[Creare un input del processo da un file locale](job-input-from-local-file-how-to.md).
