---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334863"
---
>[!NOTE]
>Per le risorse che non sono fisse, apri un ticket di supporto per richiedere un aumento delle quote. Non creare altri account di Servizi multimediali di Azure nel tentativo di ottenere limiti più elevati.

| Risorsa | Limite | 
| --- | --- | 
| Account di Servizi multimediali di Azure in un'unica sottoscrizioneAzure Media Services accounts in a single subscription | 25 (fisso) |
| Unità riservate multimediali per account di Servizi multimediali |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Job per ogni account di Servizi multimediali | 50.000<sup>2</sup> |
| Attività concatenate per processo | 30 (fisso) |
| Risorse per account di Servizi multimediali | 1.000.000|
| Asset per attività | 50 |
| Asset per processo | 100 |
| Localizzatori univoci associati a un asset contemporaneamente | 5<sup>4</sup> |
| Canali in tempo reale per account Servizi multimediali |5|
| Programmi con stato arrestato per canale  |50|
| Programmi con stato in esecuzione per canale  |3|
| Endpoint di streaming arrestati o in esecuzione per account di Servizi multimedialiStreaming endpoints that are stopped or running per Media Services account|2|
| Unità di streaming per endpoint di streaming  |10 |
| Account di archiviazione | 1.000<sup>5</sup> (fisso) |
| Criteri | 1,000,000<sup>6</sup> |
| Dimensione del file| In alcuni scenari, esiste un limite alla dimensione massima del file supportata per l'elaborazione in Servizi multimediali. <sup>7 (in</sup> questo stato |

<sup>1 : il</sup> nome del Se si modifica il tipo, ad esempio da S2 a S1, i limiti massimi di unità riservati vengono reimpostati.

<sup>2 Il</sup> nome del sistema Questo numero include i processi in coda, completati, attivi e annullati. Non include i processi eliminati. È possibile eliminare i processi precedenti utilizzando **IJob.Delete** o la richiesta HTTP **DELETE.**

A partire dal 1 aprile 2017, qualsiasi record di lavoro nel tuo account più vecchio di 90 giorni viene eliminato automaticamente, insieme ai record di attività associati. L'eliminazione automatica si verifica anche se il numero totale di record è inferiore alla quota massima. Per archiviare le informazioni sul processo e sulle attività, utilizzare il codice descritto in [Gestire le risorse con Servizi multimediali .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3 (COM del</sup> nome Quando si effettua una richiesta di elenco delle entità processo, viene restituito un massimo di 1.000 processi per richiesta. Per tenere traccia di tutti i processi inviati, utilizzare le query principali o ignorate come descritto in Opzioni query di [sistema OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4 DEL psu'</sup> I localizzatori non sono progettati per la gestione del controllo di accesso per utente. Per assegnare diritti di accesso diversi ai singoli utenti, utilizzare soluzioni DRM (Digital Rights Management). Per altre informazioni, vedere Proteggere il contenuto con Servizi multimediali di Azure.For more information, see [Protect your content with Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

5 Del numero <sup>3(</sup> Gli account di archiviazione devono provenire dalla stessa sottoscrizione di Azure.The storage accounts must be from the same Azure subscription.

6 È <sup>possibile:</sup> Esiste un limite di 1.000.000 di criteri per i diversi criteri di Servizi multimediali. Un esempio è per il criterio Locator o ContentKeyAuthorizationPolicy.An example is for the Locator policy or ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se si utilizzano sempre gli stessi giorni e le stesse autorizzazioni di accesso, utilizzare lo stesso ID criterio. Per informazioni e un esempio, consultate [Gestire le risorse con Servizi multimediali .NET SDK.](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)

<sup>7 (in</sup> questo stato La dimensione massima supportata per un singolo BLOB è attualmente fino a 5 TB in Archiviazione BLOB di Azure.The maximum size supported for a single blob is currently up to 5 TB in Azure Blob Storage. In Servizi multimediali si applicano limiti aggiuntivi in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite di dimensione si applica ai file caricati e anche ai file generati a seguito dell'elaborazione di Servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

Nella tabella seguente vengono illustrati i limiti delle unità riservate ai supporti S1, S2 e S3. Se il file di origine supera i limiti definiti nella tabella, il processo di codifica ha esito negativo. Se si codificano origini di risoluzione 4K di lunga durata, è necessario utilizzare unità riservate ai supporti S3 per ottenere le prestazioni necessarie. Se hai contenuti 4K superiori al limite di 260 GB per le unità riservate ai supporti S3, apri un ticket di supporto.

|Tipo di unità riservata ai supporti    |Dimensione massima di input (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
