---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: 654af2c9e57bca9368eb0a5cdf32e692852ba522
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80573227"
---
> [!NOTE]
> Per le risorse che non sono fisse, apri un ticket di supporto per richiedere un aumento delle quote. Non creare altri account di Servizi multimediali di Azure nel tentativo di ottenere limiti più elevati.

### <a name="account-limits"></a>Limiti dell'account

| Risorsa | Limite predefinito | 
| --- | --- | 
| Account di Servizi multimediali in una singola sottoscrizione | 25 (fisso) |

### <a name="asset-limits"></a>Limiti delle risorse

| Risorsa | Limite predefinito | 
| --- | --- | 
| Risorse per account di Servizi multimediali | 1.000.000|

### <a name="storage-limits"></a>Limiti relativi ad Archiviazione

| Risorsa | Limite predefinito | 
| --- | --- | 
| Dimensione del file| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>(1)</sup> |
| Account di archiviazione | 100<sup>(2)</sup> (fisso) |

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. In Servizi multimediali si applicano limiti aggiuntivi in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite di dimensione si applica ai file caricati e anche ai file generati a seguito dell'elaborazione di Servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

Nella tabella seguente vengono illustrati i limiti delle unità riservate ai supporti S1, S2 e S3. Se il file di origine supera i limiti definiti nella tabella, il processo di codifica ha esito negativo. Se si codificano origini di risoluzione 4K di lunga durata, è necessario utilizzare unità riservate ai supporti S3 per ottenere le prestazioni necessarie. Se hai contenuti 4K superiori al limite di 260 GB per le unità riservate ai supporti S3, apri un ticket di supporto.

|Tipo di unità riservata ai supporti|Dimensione massima di input (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> Gli account di archiviazione devono provenire dalla stessa sottoscrizione di Azure.2 The storage accounts must be from the same Azure subscription.

### <a name="jobs-encoding--analyzing-limits"></a>Limiti di processi (codifica & analisi)

| Risorsa | Limite predefinito | 
| --- | --- | 
| Job per ogni account di Servizi multimediali | 500.000 <sup>(3)</sup> (fisso)|
| Input di lavoro per processo | 50 (fisso)|
| Output dei processi per processo | 20 (fisso) |
| Transform per ogni account di Servizi multimediali | 100 (fisso)|
| Transform outputs in a Transform | 20 (fisso) |
| File per input di lavoro|10 (fisso)|

<sup>3</sup> Questo numero include processi in coda, completati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

### <a name="live-streaming-limits"></a>Limiti dello streaming live

| Risorsa | Limite predefinito | 
| --- | --- | 
| Eventi live <sup>(4)</sup> per account di Servizi multimediali |5|
| Uscite dal vivo per evento live |3 <sup>(5)</sup> |
| Durata massima dell'uscita live | 25 ore |

<sup>4</sup> Per informazioni dettagliate sulle limitazioni degli eventi live, consultate Confronto e limitazioni dei [tipi di evento live.](../articles/media-services/latest/live-event-types-comparison.md)

<sup>5</sup> Live Outputs iniziano al momento della creazione e si fermano quando vengono eliminati.

### <a name="packaging--delivery-limits"></a>Limiti di imballaggio & consegna

| Risorsa | Limite predefinito | 
| --- | --- | 
| Endpoint di streaming (interrotti o in esecuzione) per account di Servizi multimediali|2 (fisso)|
| Filtri manifesto dinamico|100|
| Criteri di streaming | 100 <sup>(6)</sup> |
| Localizzatori di streaming univoci associati contemporaneamente a un asset | 100<sup>(7)</sup> (fisso) |

<sup>6</sup> Quando si utilizza un criterio di [streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizzato, è consigliabile progettare un set limitato di tali criteri per l'account del servizio multimediale e riutilizzarli per gli streamingLocator ogni volta che sono necessarie le stesse opzioni e protocolli di crittografia. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

<sup>7</sup> I localizzatori di streaming non sono progettati per la gestione del controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

### <a name="protection-limits"></a>Limiti di protezione

| Risorsa | Limite predefinito | 
| --- | --- | 
| Opzioni per criteri chiave simmetricaOptions per Content Key Policy |30 | 
| Licenze al mese per ognuno dei tipi DRM nel servizio di distribuzione delle chiavi di Servizi multimediali per account|1.000.000|

### <a name="support-ticket"></a>Ticket di supporto

Per le risorse che non sono fisse, è possibile richiedere l'aumento delle quote, aprendo un ticket di [supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.
