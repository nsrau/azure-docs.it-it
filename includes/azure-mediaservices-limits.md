---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: de3fd8dc0d45ea10e64af8e2258682a9e98639dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180528"
---
>[!NOTE]
>Per le risorse che non sono corretti, aprire un ticket di supporto per richiedere un aumento di quote. Non creare altri account di servizi multimediali di Azure nel tentativo di ottenere limiti più elevati.

| Resource | Limite predefinito | 
| --- | --- | 
| Account di servizi multimediali di Azure in una singola sottoscrizione | 25 (fisso) |
| Media reserved unit per ogni account di servizi multimediali |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Job per ogni account di Servizi multimediali | 50.000<sup>2</sup> |
| Attività concatenate per processo | 30 (fisso) |
| Asset per ogni account di servizi multimediali | 1\.000.000|
| Asset per attività | 50 |
| Asset per processo | 100 |
| Localizzatori univoci associati a un asset contemporaneamente | 5<sup>4</sup> |
| Canali Live per ogni account di servizi multimediali |5|
| Programmi con stato arrestato per canale |50|
| Programmi con stato in esecuzione per canale |3|
| Gli endpoint vengono arrestate o in esecuzione per ogni account di servizi multimediali di streaming|2|
| Unità di streaming per endpoint di streaming |10 |
| Account di archiviazione | 1\.000<sup>5</sup> (fisso) |
| Criteri | 1,000,000<sup>6</sup> |
| Dimensioni complete| In alcuni scenari è previsto un limite per le dimensioni massime del file supportate per l'elaborazione in servizi multimediali. <sup>7</sup> |

<sup>1</sup>se si modifica il tipo, ad esempio da S2 a S1, i limiti di numero massimo di unità riservata vengono reimpostati.

<sup>2</sup>questo numero include i processi in coda, completati, attivi e annullati. Non include i processi eliminati. È possibile eliminare i processi obsoleti usando **Ijob** o nella **eliminare** richiesta HTTP.

A partire da 1 ° aprile 2017, qualsiasi record di processo antecedenti a 90 giorni l'account viene eliminato automaticamente, insieme ai record attività associati. L'eliminazione automatica si verifica anche se il numero totale di record è inferiore alla quota massima. Per archiviare le informazioni di attività e processi, usare il codice descritto nel [gestire gli asset con Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>quando si effettua una richiesta di elenco di entità job, viene restituito un massimo di 1.000 processi per ogni richiesta. Per tenere traccia dei processi inviati tutti, usare la parte superiore o ignorare le query, come descritto in [opzioni di query di sistema OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>i localizzatori non sono progettati per gestire il controllo di accesso per utente. Per concedere diritti di accesso diverse a singoli utenti, usare le soluzioni di digital rights management (DRM). Per altre informazioni, vedere [proteggere i contenuti con servizi multimediali di Azure](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>6</sup>è previsto un limite di 1.000.000 di criteri per i diversi criteri di servizi multimediali. Un esempio è per i criteri Locator o ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se sempre usare gli stessi giorni e le autorizzazioni di accesso, usare lo stesso ID di criteri. Per informazioni ed esempi, vedere [gestire gli asset con Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>le dimensioni massime supportate per un singolo blob è attualmente fino a 5 TB in archiviazione Blob di Azure. Applicati altri limiti in servizi multimediali di base alle dimensioni della macchina virtuale utilizzati dal servizio. Il limite delle dimensioni si applica ai file da caricare e anche i file che vengono generati come risultato di servizi multimediali di elaborazione (codifica o l'analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

La tabella seguente illustra i limiti sui supporti sono riservati unità S1, S2 e S3. Se il file di origine è maggiore di limiti definiti nella tabella, il processo di codifica avrà esito negativo. Se si codifica origini risoluzione 4K di lunga durata, ti viene richiesto di usare media reserved unit S3 per ottenere le prestazioni necessarie. Se si presentano contenuti 4K superano il limite di 260 GB sulle media reserved unit S3, contattare Microsoft all'indirizzo amshelp@microsoft.com per delle possibili soluzioni supportare lo scenario.

|Tipo di unità riservata multimediale   |Dimensioni massime input (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
