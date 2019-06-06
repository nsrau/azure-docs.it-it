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
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a597ab3519f4ba1696e111622541bcab89488558
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425424"
---
# <a name="content-key-policies"></a>Criteri di chiave simmetrica

Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

Per specificare le opzioni di crittografia nel flusso, è necessario creare un [criteri di Streaming](streaming-policy-concept.md) e associalo con i [localizzatore di Streaming](streaming-locators-concept.md). Si crea il [Content Key Policy](https://docs.microsoft.com/rest/api/media/contentkeypolicies) per configurare come la chiave simmetrica (che fornisce l'accesso sicuro per il [asset](assets-concept.md)) sia resa disponibile per i client finali. È necessario impostare i requisiti (restrizioni) in Criteri di chiave simmetrica che devono essere soddisfatte affinché le chiavi con la configurazione specificata possa essere distribuita al client. I criteri questa chiave simmetrica non è necessaria per streaming o il download non crittografato. 

In genere, associano i **criteri di chiave simmetrica** con il [localizzatore di Streaming](streaming-locators-concept.md). In alternativa, è possibile specificare i criteri di chiave simmetrica all'interno di un [Streaming criteri](streaming-policy-concept.md) (quando la creazione di un criterio personalizzato di Streaming per scenari avanzati). 

È consigliabile per consentire a servizi multimediali di genera automaticamente chiavi simmetriche. In genere, si potrebbe usare una chiave lunga e verificare l'esistenza di criteri con **ottenere**. Per recuperare la chiave, è necessario chiamare un metodo di azione separato per ottenere i segreti o le credenziali. Vedere l'esempio riportato di seguito.

I **criteri di chiave simmetrica** possono essere aggiornati. Per l'aggiornamento e la selezione dei criteri aggiornati le cache di distribuzione delle chiavi possono impiegare al massimo 15 minuti. 

> [!IMPORTANT]
> * Le proprietà dei **criteri di chiave simmetrica** di tipo Datetime sono sempre in formato UTC.
> * È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare le stesse opzioni. Per altre informazioni, vedere [Quote e limitazioni](limits-quotas-constraints.md).

### <a name="example"></a>Esempio

Per ottenere la chiave, usare **GetPolicyPropertiesWithSecretsAsync**, come illustrato nella [ottenere una chiave di firma dai criteri esistenti](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) esempio.

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](protect-with-aes128.md)
* [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
