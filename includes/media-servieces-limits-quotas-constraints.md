---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: d6ff823951e6474a35a514036f02c0aabb17bf01
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82997694"
---
> [!NOTE]
> Per le risorse che non sono fisse, aprire un ticket di supporto per richiedere un aumento delle quote. Non creare altri account di servizi multimediali di Azure in un tentativo di ottenere limiti più elevati.

### <a name="account-limits"></a>Limiti dell'account

| Risorsa | Limite predefinito | 
| --- | --- | 
| Account di Servizi multimediali in una singola sottoscrizione | 25 (fisso) |

### <a name="asset-limits"></a>Limiti degli asset

| Risorsa | Limite predefinito | 
| --- | --- | 
| Asset per account di servizi multimediali | 1\.000.000|

### <a name="storage-media-limits"></a>Limiti di archiviazione (supporti)

| Risorsa | Limite predefinito | 
| --- | --- | 
| Dimensione del file| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>1</sup> |
| Account di archiviazione | 100<sup>(2)</sup> (fisso) |

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. I limiti aggiuntivi si applicano a servizi multimediali in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite delle dimensioni si applica ai file caricati e anche ai file generati come risultato dell'elaborazione di servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

La tabella seguente illustra i limiti di media reserved Unit S1, S2 e S3. Se il file di origine è superiore ai limiti definiti nella tabella, il processo di codifica non riesce. Se si codificano origini di risoluzione 4K di lunga durata, è necessario usare le unità riservate S3 media per ottenere le prestazioni necessarie. Se si dispone di contenuto 4K superiore al limite di 260 GB per le unità riservate S3 media, aprire un ticket di supporto.

|Media reserved Unit Type|Dimensioni massime input (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> gli account di archiviazione devono provenire dalla stessa sottoscrizione di Azure.

### <a name="jobs-encoding--analyzing-limits"></a>Limiti di processi (codifica & analisi)

| Risorsa | Limite predefinito | 
| --- | --- | 
| Job per ogni account di Servizi multimediali | 500.000 <sup>(3)</sup> (fisso)|
| Input per processo | 50 (fisso)|
| Output processo per processo | 20 (fisso) |
| Transform per ogni account di Servizi multimediali | 100 (fisso)|
| Trasformare gli output in una trasformazione | 20 (fisso) |
| File per input del processo|10 (fisso)|

<sup>3</sup> questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

### <a name="live-streaming-limits"></a>Limiti di streaming live

| Risorsa | Limite predefinito | 
| --- | --- | 
| Eventi Live <sup>(4)</sup> per account di servizi multimediali |5|
| Output Live per evento Live |3 <sup>(5)</sup> |
| Durata max output Live | [Dimensioni della finestra DVR](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> per informazioni dettagliate sulle limitazioni degli eventi live, vedere [confronto e limitazioni dei tipi di eventi live](../articles/media-services/latest/live-event-types-comparison.md).

<sup>5</sup> gli output Live iniziano alla creazione e si interrompono quando vengono eliminati.

### <a name="packaging--delivery-limits"></a>Creazione di pacchetti di limiti di recapito &

| Risorsa | Limite predefinito | 
| --- | --- | 
| Endpoint di streaming (interrotti o in esecuzione) per account di Servizi multimediali|2 (fisso)|
| Filtri manifesto dinamico|100|
| Criteri di streaming | 100 <sup>(6)</sup> |
| Localizzatori di streaming univoci associati contemporaneamente a un asset | 100<sup>(7)</sup> (fisso) |

<sup>6</sup> quando si usa un [criterio di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizzato, è necessario progettare un set limitato di tali criteri per l'account del servizio multimediale e riutilizzarli per la StreamingLocators ogni volta che sono necessarie le stesse opzioni e protocolli di crittografia. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

<sup>7</sup> i localizzatori di streaming non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

### <a name="protection-limits"></a>Limiti di protezione

| Risorsa | Limite predefinito | 
| --- | --- | 
| Opzioni per criterio chiave simmetrica |30 | 
| Licenze al mese per ogni tipo di DRM nel servizio di distribuzione delle chiavi di servizi multimediali per account|1\.000.000|

### <a name="support-ticket"></a>Ticket di supporto

Per le risorse non corrette, è possibile richiedere la generazione delle quote aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.
