---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: de3fd8dc0d45ea10e64af8e2258682a9e98639dc
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67180528"
---
>[!NOTE]
>Per le risorse che non sono fisse, aprire un ticket di supporto per richiedere un aumento delle quote. Non creare altri account di servizi multimediali di Azure in un tentativo di ottenere limiti più elevati.

| Risorsa | Limite predefinito | 
| --- | --- | 
| Account di servizi multimediali di Azure in una singola sottoscrizione | 25 (fisso) |
| Media reserved Unit per account di servizi multimediali |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Job per ogni account di Servizi multimediali | 50.000<sup>2</sup> |
| Attività concatenate per processo | 30 (fisso) |
| Asset per account di servizi multimediali | 1\.000.000|
| Asset per attività | 50 |
| Asset per processo | 100 |
| Localizzatori univoci associati a un asset contemporaneamente | 5<sup>4</sup> |
| Canali live per ogni account di servizi multimediali |5|
| Programmi con stato arrestato per canale |50|
| Programmi con stato in esecuzione per canale |3|
| Endpoint di streaming interrotti o in esecuzione per ogni account di servizi multimediali|2|
| Unità di streaming per endpoint di streaming |10 |
| Account di archiviazione | 1\.000<sup>5</sup> (fisso) |
| Criteri | 1,000,000<sup>6</sup> |
| Dimensione file| In alcuni scenari è previsto un limite per le dimensioni massime del file supportate per l'elaborazione in servizi multimediali. <sup>7</sup> |

<sup>1</sup> Se si modifica il tipo, ad esempio da S2 a S1, vengono reimpostati i limiti massimi di unità riservate.

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include i processi eliminati. È possibile eliminare i vecchi processi usando **IJob. Delete** o la richiesta HTTP **Delete** .

A partire dal 1 ° aprile 2017, tutti i record di processo nell'account più vecchi di 90 giorni vengono eliminati automaticamente, insieme ai record attività associati. L'eliminazione automatica si verifica anche se il numero totale di record è inferiore alla quota massima. Per archiviare le informazioni relative a processi e attività, usare il codice descritto in [gestire gli asset con Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> Quando si effettua una richiesta per elencare le entità processo, viene restituito un massimo di 1.000 processi per ogni richiesta. Per tenere traccia di tutti i processi inviati, usare le query Top o Skip come descritto in [Opzioni di query di sistema OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup> I localizzatori non sono progettati per gestire il controllo di accesso per utente. Per concedere diritti di accesso diversi a singoli utenti, utilizzare soluzioni di Digital Rights Management (DRM). Per altre informazioni, vedere [proteggere i contenuti con servizi multimediali di Azure](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> Gli account di archiviazione devono provenire dalla stessa sottoscrizione di Azure.

<sup>6</sup> È previsto un limite di 1 milione criteri per i diversi criteri di servizi multimediali. Un esempio è per i criteri Locator o ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se si usano sempre gli stessi giorni e le stesse autorizzazioni di accesso, usare lo stesso ID criterio. Per informazioni e un esempio, vedere [gestire gli asset con l'SDK di servizi multimediali per .NET](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> Le dimensioni massime supportate per un singolo BLOB sono attualmente fino a 5 TB nell'archivio BLOB di Azure. I limiti aggiuntivi si applicano a servizi multimediali in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite delle dimensioni si applica ai file caricati e anche ai file generati come risultato dell'elaborazione di servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

La tabella seguente illustra i limiti di media reserved Unit S1, S2 e S3. Se il file di origine è superiore ai limiti definiti nella tabella, il processo di codifica non riesce. Se si codificano origini di risoluzione 4K di lunga durata, è necessario usare le unità riservate S3 media per ottenere le prestazioni necessarie. Se il contenuto di 4K è superiore al limite di 260 GB per le unità riservate S3, contattaci all'indirizzo amshelp@microsoft.com per prevenire potenziali mitigazioni per il supporto dello scenario.

|Media reserved Unit Type   |Dimensioni massime input (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
