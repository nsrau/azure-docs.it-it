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
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8809bf25c3bcfb26fb0ad251a2b09dfdca2a3e04
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679198"
---
# <a name="content-key-policies"></a>Criteri di chiave simmetrica

Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

Per specificare le opzioni di crittografia nel flusso, è necessario creare un [criterio di flusso](streaming-policy-concept.md) e associarlo al [localizzatore di streaming](streaming-locators-concept.md). I criteri della [chiave](https://docs.microsoft.com/rest/api/media/contentkeypolicies) simmetrica vengono creati per configurare il modo in cui la chiave simmetrica (che fornisce l'accesso sicuro agli [Asset](assets-concept.md)) viene fornita ai client finali. È necessario impostare i requisiti (restrizioni) per i criteri della chiave simmetrica che devono essere soddisfatti per poter recapitare le chiavi con la configurazione specificata ai client. I criteri della chiave simmetrica non sono necessari per cancellare lo streaming o il download. 

In genere, i criteri della chiave simmetrica vengono associati al localizzatore di [streaming](streaming-locators-concept.md). In alternativa, è possibile specificare i criteri della chiave simmetrica in un [criterio di flusso](streaming-policy-concept.md) , quando si creano criteri di flusso personalizzati per gli scenari avanzati. 

> [!NOTE]
> Le proprietà dei criteri della chiave simmetrica di `Datetime` tipo sono sempre in formato UTC.

## <a name="best-practices-and-considerations"></a>Procedure consigliate e considerazioni

> [!IMPORTANT]
> Esaminare i consigli seguenti.

* È consigliabile progettare un set limitato di criteri per l'account del servizio multimediale e riutilizzarli per i localizzatori di streaming ogni volta che sono necessarie le stesse opzioni. Per altre informazioni, vedere [Quote e limitazioni](limits-quotas-constraints.md).
* I criteri chiave simmetrica sono aggiornabili. Possono essere necessari fino a 15 minuti per aggiornare le cache di distribuzione delle chiavi e selezionare i criteri aggiornati. 

   Se si aggiornano i criteri, si sta sovrascrivendo la cache della rete CDN esistente, che potrebbe causare un problema di riproduzione per i clienti che usano contenuto memorizzato nella cache.  
* Si consiglia di non creare nuovi criteri della chiave simmetrica per ogni asset. I principali vantaggi della condivisione degli stessi criteri della chiave simmetrica tra gli asset che richiedono le stesse opzioni dei criteri sono:
   
   * È più semplice gestire un numero ridotto di criteri.
   * Se è necessario aggiornare i criteri della chiave simmetrica, le modifiche vengono applicate immediatamente a tutte le nuove richieste di licenze.
* Se è necessario creare un nuovo criterio, è necessario creare un nuovo localizzatore di streaming per l'asset.
* È consigliabile consentire a servizi multimediali di generare automaticamente la chiave simmetrica. 

   In genere, si usa una chiave di lunga durata e si verifica l'esistenza dei criteri della chiave simmetrica con [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Per recuperare la chiave, è necessario chiamare un metodo di azione separato per ottenere i segreti o le credenziali. Vedere l'esempio riportato di seguito.

## <a name="example"></a>Esempio

Per ottenere la chiave, usare `GetPolicyPropertiesWithSecretsAsync`, come illustrato nell'esempio [ottenere una chiave di firma da un criterio esistente](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](protect-with-aes128.md)
* [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
