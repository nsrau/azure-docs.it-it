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
ms.openlocfilehash: 85a9cad80156dc6ac40e78610c91805d485ff3df
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586001"
---
# <a name="content-key-policies"></a>Criteri di chiave simmetrica

Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

Per specificare le opzioni di crittografia nel flusso, è necessario creare un criterio di [streaming](streaming-policy-concept.md) e associarlo a [Streaming Locator](streaming-locators-concept.md). È possibile creare i [criteri chiave](https://docs.microsoft.com/rest/api/media/contentkeypolicies) di contenuto per configurare la modalità di recapito della chiave simmetrica (che fornisce l'accesso sicuro alle [risorse)](assets-concept.md)ai client finali. È necessario impostare i requisiti (restrizioni) sui criteri chiave del contenuto che devono essere soddisfatti affinché le chiavi con la configurazione specificata vengano recapitate ai client. I criteri della chiave simmetrica non sono necessari per lo streaming o il download non crittografato. 

In genere, i criteri della chiave simmetrica vengono associati a [Streaming Locator](streaming-locators-concept.md). In alternativa, è possibile specificare i criteri della chiave di contenuto all'interno di un criterio di [streaming](streaming-policy-concept.md) (quando si creano criteri di streaming personalizzati per scenari avanzati). 

## <a name="best-practices-and-considerations"></a>Procedure consigliate e considerazioni

> [!IMPORTANT]
> Si prega di rivedere le seguenti raccomandazioni.

* È consigliabile progettare un set limitato di criteri per l'account del servizio multimediale e riutilizzarli per i localizzatori di streaming ogni volta che sono necessarie le stesse opzioni. Per ulteriori informazioni, vedere [Quote e limiti](limits-quotas-constraints.md).
* I criteri delle chiavi del contenuto sono aggiornabili. L'aggiornamento e la ripresa dei criteri aggiornati per le cache di recapito delle chiavi possono richiedere fino a 15 minuti. 

   Aggiornando i criteri, si sovrascrive la cache della rete CDN esistente che potrebbe causare problemi di riproduzione per i clienti che utilizzano contenuto memorizzato nella cache.  
* È consigliabile non creare un nuovo criterio chiave simmetrica per ogni risorsa. I vantaggi principali della condivisione della stessa politica della chiave di contenuto tra le risorse che richiedono le stesse opzioni dei criteri sono:
   
   * È più facile gestire un numero limitato di criteri.
   * Se è necessario apportare aggiornamenti ai criteri della chiave simmetrica, le modifiche diventano effettive su tutte le nuove richieste di licenza quasi immediatamente.
* Se è necessario creare un nuovo criterio, è necessario creare un nuovo localizzatore di streaming per l'asset.
* Si consiglia di consentire a Servizi multimediali di generare automaticamente la chiave simmetrica. 

   In genere, si utilizza una chiave di lunga durata e si verifica l'esistenza dei criteri della chiave simmetrica con [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Per recuperare la chiave, è necessario chiamare un metodo di azione separato per ottenere i segreti o le credenziali. Vedere l'esempio riportato di seguito.

## <a name="example"></a>Esempio

Per accedere alla chiave, utilizzare `GetPolicyPropertiesWithSecretsAsync`, come illustrato nell'esempio Get a signing from the existing [policy.](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="additional-notes"></a>Note aggiuntive

* Le proprietà dei criteri chiave `Datetime` di contenuto di tipo sono sempre in formato UTC.
* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="next-steps"></a>Passaggi successivi

* [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](protect-with-aes128.md)
* [Utilizzare la crittografia dinamica DRM e il servizio di distribuzione delle licenze](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
