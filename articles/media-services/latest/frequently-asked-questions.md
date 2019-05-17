---
title: Domande frequenti su Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2019
ms.author: juliako
ms.openlocfilehash: 98e8c0ccd150776341e644f7565696e8fbd63e99
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556275"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Domande frequenti su Servizi multimediali di Azure v3

Questo articolo contiene le risposte alle domande frequenti su Servizi multimediali di Azure (AMS) v3.

## <a name="v3-apis"></a>API v3

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>I ruoli di Azure possono eseguire azioni sulle risorse di servizi multimediali di Azure? 

Visualizzare [controllo di accesso basato sui ruoli (RBAC) per gli account di servizi multimediali](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Come si esegue la configurazione di Media Reserved Units?

Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 MRU S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

Per informazioni dettagliate, vedere [Ridimensionamento dell'elaborazione di contenuti multimediali con l'interfaccia della riga di comando](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual è il metodo consigliato per l'elaborazione dei processi?

Usare [Trasformazioni](https://docs.microsoft.com/rest/api/media/transforms) per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni **trasformazione** descrive un recipe o un flusso di lavoro di attività per l'elaborazione dei file video o audio. Oggetto [processo](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva a servizi multimediali per applicare le **trasformare** in un contenuto video o audio input specificato. Dopo aver creato la trasformazione, è possibile inviare i processi usando le API di Servizi multimediali o uno degli SDK pubblicati. Per altre informazioni, vedere [Trasformazioni e processi](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Come funziona la paginazione?

Quando si usa la paginazione, usare sempre il collegamento seguente per enumerare la raccolta e non dipendere da una determinata dimensione di pagina. Per informazioni dettagliate ed esempi, vedere [Filtro, ordinamento, restituzione di più pagine](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quali funzionalità non sono ancora disponibili nella v3 di servizi multimediali di Azure?

Per informazioni dettagliate, vedere [funzionalità gap rispetto all'API v2](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

## <a name="live-streaming"></a>Streaming live 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Come è possibile inserire interruzioni/video e slate immagine durante lo streaming live?

La codifica live di Servizi multimediali v3 non supporta ancora l'inserimento di slate immagine o video durante lo streaming live. 

È possibile usare un [codificatore locale live](recommended-on-premises-live-encoders.md) per commutare il video di origine. Molte app offrono la possibilità di commutare le origini, tra cui Telestream Wirecast, Switcher Studio (in iOS), OBS Studio (app gratuita) e molte altre.

## <a name="content-protection"></a>Protezione del contenuto

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Come e dove si ottiene il token JWT prima di usarlo per richiedere la licenza o la chiave?

1. Per la produzione, è necessario avere un servizio Web del servizio token di sicurezza che emette il token JWT in corrispondenza di una richiesta HTTPS. Per i test, è possibile usare il codice illustrato nel metodo **GetTokenAsync** definito in [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Dopo l'autenticazione di un utente, il lettore dovrà richiedere al servizio token di sicurezza tale token e assegnarlo come valore del token. È possibile usare l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Per un esempio di esecuzione del servizio token di sicurezza, con una chiave di crittografia simmetrica e asimmetrica, vedere [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Per un esempio di lettore basato su Azure Media Player con tale token JWT, vedere [ https://aka.ms/amtest ](https://aka.ms/amtest) (espandere il collegamento "player_settings" per visualizzare l'input del token).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Come si autorizzano le richieste di streaming di video con la crittografia AES?

L'approccio corretto consiste nell'usare il servizio token di sicurezza:

Nel servizio token di sicurezza, a seconda del profilo dell'utente, aggiungere attestazioni diverse (ad esempio "Utente Premium", "Utente Basic", "Utente di prova gratuita"). Con diverse attestazioni in un token JWT, l'utente può visualizzare diversi tipi di contenuto. Naturalmente, per contenuto/risorse diverse, ContentKeyPolicyRestriction avrà il valore RequiredClaims corrispondente.

Usare API servizi multimediali di Azure per la configurazione di licenza/chiave recapito e crittografare l'asset (come illustrato nella [in questo esempio](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Per altre informazioni, vedere:

- [Panoramica della protezione del contenuto](content-protection-overview.md)
- [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>Servizi multimediali v2 e v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>È possibile usare il portale di Azure per gestire le risorse della versione v3?

Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-ref), l'[interfaccia della riga di comando](https://aka.ms/ams-v3-cli-ref) o uno degli [SDK](media-services-apis-overview.md#sdks) supportati.

### <a name="is-there-an-assetfile-concept-in-v3"></a>È presente un concetto AssetFile nella versione v3?

Le entità AssetFile sono state rimosse dall'API AMS per separare Servizi multimediali dalla dipendenza da Storage SDK. Le informazioni che appartengono a Storage SDK vengono ora conservate in Storage e non in Servizi multimediali. 

Per altre informazioni, vedere [Eseguire la migrazione a Servizi multimediali v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Dove è finita la crittografia di archiviazione lato client?

È ora consigliabile usare la crittografia di archiviazione lato server (che è attivata per impostazione predefinita). Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Servizi multimediali v3](media-services-overview.md)
