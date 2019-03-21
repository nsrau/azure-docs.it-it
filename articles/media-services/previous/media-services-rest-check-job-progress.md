---
title: Come controllare lo stato dei processi mediante l'API REST | Microsoft Docs
description: Informazioni su come tenere traccia dello stato dei processi.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a1a1f956-c035-448a-af9c-5ac15fcce9dd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a7b61ba05aea817d65e7cac991ad460d708a5ea7
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294162"
---
# <a name="how-to-check-job-progress"></a>Procedura: Controllare lo stato dei processi
> [!div class="op_single_selector"]
> * [Portale](media-services-portal-check-job-progress.md)
> * [.NET](media-services-check-job-progress.md)
> * [REST](media-services-rest-check-job-progress.md)
> 
> 

Quando si esegue un processo, spesso è necessario monitorarne l'avanzamento. È possibile trovare lo stato dell'entità Job mediante la relativa proprietà State. Per altre informazioni sulla proprietà State, vedere [Proprietà dell'entità Job](https://docs.microsoft.com/rest/api/media/operations/job#job_entity_properties).

## <a name="connect-to-media-services"></a>Connettersi a Servizi multimediali

Per informazioni su come connettersi all'API AMS, vedere [Accedere all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="check-job-progress"></a>Controllare lo stato dei processi

Richiesta:

    GET https://media.windows.net/api/Jobs()?$filter=Id%20eq%20'nb%3Ajid%3AUUID%3Af3c43f94-327f-2347-90bb-3bf79f8559f1'&$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    Host: media.windows.net

Risposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 450
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: d9b83c57-e26c-4d10-a20b-2be634c4b6a8
    request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
    x-ms-request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains

    {"odata.metadata":"https://media.windows.net/api/$metadata#Jobs","value":[{"Id":"nb:jid:UUID:f3c43f94-327f-2347-90bb-3bf79f8559f1","Name":"Encoding BigBuckBunny into to H264 Adaptive Bitrate MP4 Set 720p","Created":"2015-02-11T01:46:08.897","LastModified":"2015-02-11T01:46:08.897","EndTime":null,"Priority":0,"RunningDuration":0.0,"StartTime":"2015-02-11T01:46:16.58","State":2,"TemplateId":null,"JobNotificationSubscriptions":[]}]} 


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vedere anche 

[Informazioni generali sull'API REST di Servizi multimediali](media-services-rest-how-to-use.md)
