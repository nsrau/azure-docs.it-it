---
title: Quote e limiti in servizi multimediali di Azure
description: Questo argomento descrive le quote e i limiti in Servizi multimediali di Microsoft Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: d6ca7a444f2a3d4babe220548edb10bd37784be7
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678106"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Quote e limiti di servizi multimediali di Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo elenca alcuni dei limiti di Servizi multimediali di Microsoft Azure più comuni, che sono anche detti quote.

> [!NOTE]
> Per le risorse non fisse aprire un ticket di supporto e richiedere l'aumento delle quote. Non creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="account-limits"></a>Limiti per account

| Risorsa | Limite predefinito |
| --- | --- |
| [Account di servizi multimediali](media-services-account-concept.md) in una singola sottoscrizione | 100 (fisso) |

## <a name="asset-limits"></a>Limiti per asset

| Risorsa | Limite predefinito |
| --- | --- |
| [Asset](assets-concept.md) per account di servizi multimediali | 1\.000.000|

## <a name="storage-limits"></a>Limiti relativi ad Archiviazione

| Risorsa | Limite predefinito | 
| --- | --- | 
| Dimensione del file| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>(1)</sup> |
| [Account di archiviazione](storage-account-concept.md) | 100<sup>(2)</sup> (fisso) |

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. Vengono applicati altri limiti in Servizi multimediali in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite delle dimensioni si applica ai file caricati, nonché ai file generati come risultato dell'elaborazione di Servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

La tabella seguente mostra i limiti per le unità riservata di codifica S1, S2 e S3. Se le dimensioni del file di origine superano i limiti definiti nella tabella, il processo di codifica non riesce. Se si esegue la codifica di origini risoluzione 4K di lunga durata, è necessario usare unità riservate di codifica S3 per ottenere le prestazioni necessarie. Se le dimensioni dei contenuti 4K superano il limite di 260 GB per le unità riservate di codifica S3, aprire un ticket di supporto.

|Tipo di unità riservata di codifica|Dimensioni massime di input (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Limiti per processi (codifica e analisi)

| Risorsa | Limite predefinito | 
| --- | --- | 
| [Processi](transforms-jobs-concept.md) per account di servizi multimediali | 500.000 <sup>(3)</sup> (fisso)|
| Input di processi per processo | 50 (fisso)|
| Output di processi per processo | 20 (fisso) |
| [Trasformazioni](transforms-jobs-concept.md) per ogni account di servizi multimediali | 100 (fisso)|
| Output di trasformazione in una trasformazione | 20 (fisso) |
| File per input di processo|10 (fisso)|

<sup>3</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

## <a name="live-streaming-limits"></a>Limiti per lo streaming live

| Risorsa | Limite predefinito | 
| --- | --- | 
| [Eventi live](live-events-outputs-concept.md) <sup>(4)</sup> per account di servizi multimediali |5|
| Output live per evento live |3 <sup>(5)</sup> |
| Durata max degli output live | [Dimensioni della finestra DVR](live-event-cloud-dvr.md) |

<sup>4</sup> per informazioni dettagliate sui limiti degli eventi live, vedere [confronto e limiti di tipi di eventi live](live-event-types-comparison.md).

<sup>5</sup> Gli output live iniziano al momento della creazione e terminano quando vengono eliminati.

## <a name="packaging--delivery-limits"></a>Limiti per la creazione e la distribuzione di pacchetti

| Risorsa | Limite predefinito |
| --- | --- |
| [Endpoint di streaming](streaming-endpoint-concept.md) (arrestati o in esecuzione) per ogni account di servizi multimediali | 2 |
| Unità di streaming Premium | 10 |
| [Filtri manifesto dinamico](filters-dynamic-manifest-overview.md)|100|
| [Criteri di streaming](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| [Localizzatori di streaming](streaming-locators-concept.md) univoci associati a un asset in una sola volta | 100<sup>(7)</sup> (fisso) |

<sup>6</sup> Se si usano [criteri di streaming](/rest/api/media/streamingpolicies) personalizzati, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per StreamingLocators ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

<sup>7</sup> I localizzatori di streaming non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

## <a name="protection-limits"></a>Limiti di protezione

| Risorsa | Limite predefinito |
| --- | --- |
| Opzioni per [criterio chiave](content-key-policy-concept.md) simmetrica |30 |
| Licenze al mese per ogni tipo di DRM nel servizio di distribuzione di chiavi di Servizi multimediali per account|1\.000.000|

## <a name="support-ticket"></a>Ticket di supporto

Per le risorse non fisse, è possibile richiedere l'aumento delle quote aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="next-steps"></a>Passaggi successivi

[Overview](media-services-overview.md)
