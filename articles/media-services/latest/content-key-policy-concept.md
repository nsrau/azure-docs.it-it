---
title: Criteri di chiave simmetrica in Servizi multimediali - Azure | Microsoft Docs
description: Questo articolo illustra le caratteristiche dei criteri di chiave simmetrica e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745122"
---
# <a name="content-key-policies"></a>Criteri di chiave simmetrica

Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati.

Per specificare le opzioni di crittografia nel flusso, è necessario creare i [criteri di chiave simmetrica](https://docs.microsoft.com/rest/api/media/contentkeypolicies) e associarli al **localizzatore di streaming**. I **criteri di chiave simmetrica** configurano le modalità di recapito della chiave simmetrica ai client finali usando il componente di distribuzione delle chiavi di Servizi multimediali. È possibile consentire a Servizi multimediali di generare automaticamente la chiave simmetrica. In genere si usa una chiave di lunga durata e si verifica l'esistenza dei criteri con Get. Per recuperare la chiave, è necessario chiamare un metodo di azione separato per ottenere i segreti o le credenziali. Vedere l'esempio riportato di seguito.

I **criteri di chiave simmetrica** possono essere aggiornati. Ad esempio, è possibile decidere di aggiornare i criteri se è necessario eseguire una rotazione delle chiavi. È possibile aggiornare la chiave di verifica primaria e l'elenco delle chiavi di verifica alternative nei criteri esistenti. Per l'aggiornamento e la selezione dei criteri aggiornati le cache di distribuzione delle chiavi possono impiegare al massimo 15 minuti. 

> [!IMPORTANT]
> * Le proprietà dei **criteri di chiave simmetrica** di tipo Datetime sono sempre in formato UTC.
> * È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare le stesse opzioni. 

## <a name="example"></a>Esempio

Per ottenere la chiave, usare **GetPolicyPropertiesWithSecretsAsync**, come illustrato nell'esempio seguente.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, restituzione di più pagine

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](protect-with-aes128.md)
* [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
