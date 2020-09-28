---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: c315c071aeb36eea0bd1af84b1344cf2fef5f703
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329661"
---
>[!NOTE]
>Per le risorse non fisse aprire un ticket di supporto e richiedere l'aumento delle quote. Non creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

| Risorsa | Limite | 
| --- | --- | 
| Account di Servizi multimediali di Azure in una singola sottoscrizione | 25 (fisso) |
| Unità riservate di codifica per ogni account di Servizi multimediali |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Job per ogni account di Servizi multimediali | 50.000<sup>2</sup> |
| Attività concatenate per processo | 30 (fisso) |
| Asset per ogni account di Servizi multimediali | 1\.000.000|
| Asset per attività | 50 |
| Asset per processo | 100 |
| Localizzatori univoci associati a un asset contemporaneamente | 5<sup>4</sup> |
| Canali live per ogni account di Servizi multimediali |5|
| Programmi con stato arrestato per canale  |50|
| Programmi con stato in esecuzione per canale  |3|
| Endpoint di streaming arrestati o in esecuzione per ogni account di Servizi multimediali|2|
| Unità di streaming per endpoint di streaming  |10 |
| Account di archiviazione | 100<sup>5</sup> (fisse) |
| Criteri | 1,000,000<sup>6</sup> |
| Dimensione del file| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali.<sup>7</sup> |

<sup>1</sup>Se si cambia il tipo, ad esempio si passa da S2 a S1, i limiti massimi per le unità riservate vengono reimpostati.

<sup>2</sup>Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. È possibile eliminare i processi obsoleti usando **IJob.Delete** o la richiesta HTTP **DELETE**.

A partire dal 1° aprile 2017, tutti i record di processo presenti nell'account e più vecchi di 90 giorni vengono eliminati automaticamente, insieme ai record attività associati. L'eliminazione automatica viene eseguita anche se il numero totale di record è inferiore alla quota massima. Per archiviare le informazioni su processi e attività, usare il codice descritto in [Gestire gli asset con Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>Quando si effettua una richiesta per elencare le entità processo, vengono restituiti al massimo 1.000 processi per ogni richiesta. Per tenere traccia di tutti i processi inviati, usare le opzioni top o skip descritte nell'argomento [Opzioni delle query di sistema OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>I localizzatori non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management). Per altre informazioni, vedere [Proteggere i contenuti con Servizi multimediali di Azure](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>6</sup>È previsto un limite di 1.000.000 di criteri per i diversi criteri di Servizi multimediali, ad esempio per i criteri Locator o per ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se si usano sempre gli stessi giorni e le stesse autorizzazioni di accesso, usare lo stesso ID criteri. Per informazioni e per un esempio, vedere [Gestire asset con Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. Vengono applicati altri limiti in Servizi multimediali in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite delle dimensioni si applica ai file caricati, nonché ai file generati come risultato dell'elaborazione di Servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

La tabella seguente mostra i limiti per le unità riservata di codifica S1, S2 e S3. Se le dimensioni del file di origine superano i limiti definiti nella tabella, il processo di codifica non riesce. Se si esegue la codifica di origini risoluzione 4K di lunga durata, è necessario usare unità riservate di codifica S3 per ottenere le prestazioni necessarie. Se le dimensioni dei contenuti 4K superano il limite di 260 GB per le unità riservate di codifica S3, aprire un ticket di supporto.

|Tipo di unità riservata di codifica    |Dimensioni massime di input (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
