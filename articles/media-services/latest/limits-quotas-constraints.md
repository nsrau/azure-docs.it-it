---
title: Quote e limitazioni in Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo argomento descrive le quote e le limitazioni in Servizi multimediali di Microsoft Azure V3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 5a4f7e31cb17f47e8796ab99c1f8a089339903df
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888428"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quote e limitazioni in Servizi multimediali di Azure v3

Questo articolo descrive le quote e le limitazioni in Servizi multimediali di Azure v3.

| Gruppi | Limite predefinito | 
| --- | --- | 
| Asset per ogni account di Servizi multimediali di Azure | 1\.000.000|
| Filtri manifesto dinamico|100|
| JobInput per ogni processo | 50 (fisso)|
| JobOutput per ogni processo | 20 (fisso) |
| TransformOutputs in una trasformazione | 20 (fisso) |
| File per ogni JobInput|10 (fisso)|
| Dimensioni complete| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>(1)</sup> |
| Job per ogni account di Servizi multimediali | 500.000 <sup>(2)</sup> (fisso)|
| Eventi live per ogni account di Servizi multimediali |5|
| Account di Servizi multimediali in una singola sottoscrizione | 25 (fisso) |
| Output Live per evento Live |3 <sup>(3)</sup> |
| Durata max output Live | 25 ore |
| Account di archiviazione | 100<sup>(4)</sup> (fisso) |
| Endpoint di streaming (interrotti o in esecuzione) per account di Servizi multimediali|2 (fisso)|
| Criteri di streaming | 100 <sup>(5)</sup> |
| Transform per ogni account di Servizi multimediali | 100 (fisso)|
| Localizzatori di streaming univoci associati contemporaneamente a un asset | 100<sup>(6)</sup> (fisso) |
| Opzioni per criterio chiave simmetrica |30 | 
| Licenze al mese per ogni tipo di DRM nel servizio di distribuzione delle chiavi di servizi multimediali per account|1\.000.000|

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. I limiti aggiuntivi si applicano a servizi multimediali in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite delle dimensioni si applica ai file caricati e anche ai file generati come risultato dell'elaborazione di servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

La tabella seguente illustra i limiti di media reserved Unit S1, S2 e S3. Se il file di origine è superiore ai limiti definiti nella tabella, il processo di codifica non riesce. Se si codificano origini di risoluzione 4K di lunga durata, è necessario usare le unità riservate S3 media per ottenere le prestazioni necessarie. Se si dispone di contenuto 4K superiore al limite di 260 GB per le unità riservate S3 media, aprire un ticket di supporto.

|Media reserved Unit Type   |Dimensioni massime input (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

<sup>3</sup> gli output Live iniziano alla creazione e si interrompono quando vengono eliminati.

<sup>4</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>5</sup> quando si usano [criteri di flusso](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizzati, è necessario progettare un set limitato di tali criteri per l'account del servizio multimediale e riutilizzarli per la StreamingLocators ogni volta che sono necessarie le stesse opzioni e protocolli di crittografia. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

<sup>6</sup> localizzatori di streaming non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

## <a name="support-ticket"></a>Ticket di supporto

Per le risorse non fisse, è possibile richiedere l'aumento delle quote aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](media-services-overview.md)
