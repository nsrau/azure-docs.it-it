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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 5c0fbf396faa0e07ecca4ae16c775a39404c6fc9
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376517"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quote e limitazioni in Servizi multimediali di Azure v3

Questo articolo descrive le quote e le limitazioni in Servizi multimediali di Azure v3.

| Risorsa | Limite predefinito | 
| --- | --- | 
| Asset per ogni account di Servizi multimediali di Azure | 1.000.000|
| Filtri manifesto dinamico|100|
| JobInput per ogni processo | 50 (fisso)|
| JobOutputs per ogni processo/TransformOutputs in una trasformazione | 20 (fisso) |
| File per ogni JobInput|10 (fisso)|
| Dimensioni complete| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>(1)</sup> |
| Job per ogni account di Servizi multimediali | 500.000 <sup>(2)</sup> (fisso)|
| Elenco di trasformazioni|Impaginare la risposta, con 1000 trasformazioni per ogni pagina|
| Elenco di processi|Impaginare la risposta, con 500 processi per ogni pagina|
| LiveEvent per ogni account di Servizi multimediali |5|
| Account di Servizi multimediali in una singola sottoscrizione | 25 (fisso) |
| LiveOutput in esecuzione per ogni LiveEvent |3|
| Account di archiviazione | 100<sup>(4)</sup> (fisso) |
| Endpoint di streaming in esecuzione per ogni account di Servizi multimediali|2|
| StreamingPolicy | 100 <sup>(3)</sup> |
| Transform per ogni account di Servizi multimediali | 100 (fisso)|
| StreamingLocator univoci associati contemporaneamente a un asset | 100<sup>(5)</sup> (fisso) |

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. Vengono tuttavia applicati altri limiti in Servizi multimediali di Azure in base alle dimensioni delle macchine virtuali usate dal servizio. Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. Se le dimensioni dei contenuti 4K superano il limite di 260 GB, scrivere all'indirizzo amshelp@microsoft.com per possibili soluzioni di attenuazione per consentire il supporto di questo scenario.

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

<sup>3</sup> Se si usa un oggetto [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per gli oggetti StreamingLocator ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. Evitare quindi di creare un nuovo oggetto StreamingPolicy per ogni StreamingLocator.

<sup>4</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>5</sup> Gli oggetti StreamingLocator non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

## <a name="support-ticket"></a>Ticket di supporto

Per le risorse non fisse, è possibile richiedere l'aumento delle quote aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="next-steps"></a>Passaggi successivi

[Overview](media-services-overview.md)
