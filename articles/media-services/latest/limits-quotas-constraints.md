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
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: d5fc14adab956fae23aad24fa7bc488c8c2041e3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621686"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quote e limitazioni in Servizi multimediali di Azure v3

Questo articolo descrive le quote e le limitazioni in Servizi multimediali di Azure v3.

| Risorsa | Limite predefinito | 
| --- | --- | 
| Asset per ogni account di Servizi multimediali di Azure | 1.000.000|
| Filtri manifesto dinamico|100|
| JobInput per ogni processo | 50 (fisso)|
| JobOutput per ogni processo | 20 (fisso) |
| TransformOutputs in una forma Trasforma | 20 (fisso) |
| File per ogni JobInput|10 (fisso)|
| Dimensione file| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>(1)</sup> |
| Job per ogni account di Servizi multimediali | 500.000 <sup>(2)</sup> (fisso)|
| Elenco di trasformazioni|Impaginare la risposta, con 1000 trasformazioni per ogni pagina|
| Elenco di processi|Impaginare la risposta, con 500 processi per ogni pagina|
| Eventi live per ogni account di Servizi multimediali |5|
| Account di Servizi multimediali in una singola sottoscrizione | 25 (fisso) |
| Output in tempo reale in esecuzione per ogni evento Live |3|
| Durata max Live Output | 25 ore |
| Account di archiviazione | 100<sup>(4)</sup> (fisso) |
| Endpoint di streaming (interrotti o in esecuzione) per account di Servizi multimediali|2 (fisso)|
| Criteri di streaming | 100 <sup>(3)</sup> |
| Transform per ogni account di Servizi multimediali | 100 (fisso)|
| Localizzatori di streaming univoci associati contemporaneamente a un asset | 100<sup>(5)</sup> (fisso) |
| Criteri di chiave simmetrica |30 | 

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. Vengono tuttavia applicati altri limiti in Servizi multimediali di Azure in base alle dimensioni delle macchine virtuali usate dal servizio. Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. Se le dimensioni dei contenuti 4K superano il limite di 260 GB, scrivere all'indirizzo amshelp@microsoft.com per possibili soluzioni di attenuazione per consentire il supporto di questo scenario.

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

<sup>3</sup> Se si usano [criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzati, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per StreamingLocators ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

<sup>4</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>5</sup> I localizzatori di streaming non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

## <a name="support-ticket"></a>Ticket di supporto

Per le risorse non fisse, è possibile richiedere l'aumento delle quote aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](media-services-overview.md)
