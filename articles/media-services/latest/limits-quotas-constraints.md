---
title: Quote e limitazioni in Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo argomento descrive le quote e le limitazioni in Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.openlocfilehash: 42b8c4caa53ffa6b3bc1148544c75602597ac452
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153833"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quote e limitazioni in Servizi multimediali di Azure v3

Questo articolo descrive le quote e le limitazioni in Servizi multimediali di Azure v3.

| Resource | Limite predefinito | 
| --- | --- | 
| Asset per ogni account di Servizi multimediali di Azure | 1.000.000|
| Filtri manifesto dinamico|100|
| JobInput per ogni processo | 50 (fisso)|
| JobOutput per ogni processo | 20 (fisso) |
| TransformOutputs in una forma Trasforma | 20 (fisso) |
| File per ogni JobInput|10 (fisso)|
| Dimensioni complete| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>(1)</sup> |
| Job per ogni account di Servizi multimediali | 500.000 <sup>(2)</sup> (fisso)|
| Elenco di trasformazioni|Impaginare la risposta, con 1000 trasformazioni per ogni pagina|
| Elenco di processi|Impaginare la risposta, con 500 processi per ogni pagina|
| Eventi live per ogni account di Servizi multimediali |5|
| Account di Servizi multimediali in una singola sottoscrizione | 25 (fisso) |
| Output in tempo reale per ogni evento in tempo reale |3 <sup>(3)</sup> |
| Durata max Live Output | 25 ore |
| Account di archiviazione | 100<sup>(4)</sup> (fisso) |
| Endpoint di streaming (interrotti o in esecuzione) per account di Servizi multimediali|2 (fisso)|
| Criteri di streaming | 100 <sup>(5)</sup> |
| Transform per ogni account di Servizi multimediali | 100 (fisso)|
| Localizzatori di streaming univoci associati contemporaneamente a un asset | 100<sup>(6)</sup> (fixed) |
| Criteri di chiave simmetrica |30 | 

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. Applicati altri limiti in servizi multimediali di base alle dimensioni della macchina virtuale utilizzati dal servizio. Il limite delle dimensioni si applica ai file da caricare e anche i file che vengono generati come risultato di servizi multimediali di elaborazione (codifica o l'analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

La tabella seguente illustra i limiti sui supporti sono riservati unità S1, S2 e S3. Se il file di origine è maggiore di limiti definiti nella tabella, il processo di codifica avrà esito negativo. Se si codifica origini risoluzione 4K di lunga durata, ti viene richiesto di usare media reserved unit S3 per ottenere le prestazioni necessarie. Se si presentano contenuti 4K superano il limite di 260 GB sulle media reserved unit S3, contattare Microsoft all'indirizzo amshelp@microsoft.com per delle possibili soluzioni supportare lo scenario.

|Tipo di unità riservata multimediale   |Dimensioni massime input (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

<sup>3</sup> live output iniziano durante la creazione e terminano eliminato.

<sup>4</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>5</sup> quando si usa un oggetto personalizzato [Streaming criteri](https://docs.microsoft.com/rest/api/media/streamingpolicies), è necessario progettare un set limitato di tali criteri per l'account di servizi multimediali e riutilizzarli per i StreamingLocators ogni volta che la crittografia stessa opzioni e i protocolli sono necessari. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

<sup>6</sup> i localizzatori di streaming non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

## <a name="support-ticket"></a>Ticket di supporto

Per le risorse non fisse, è possibile richiedere l'aumento delle quote aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="next-steps"></a>Passaggi successivi

[Overview](media-services-overview.md)
