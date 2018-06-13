---
title: Quote e limitazioni in Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo argomento descrive le quote e le limitazioni in Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783090"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quote e limitazioni in Servizi multimediali di Azure v3

Questo argomento descrive le quote e le limitazioni in Servizi multimediali di Azure v3.

| Risorsa | Limite predefinito | 
| --- | --- | 
| Asset per ogni account di Servizi multimediali di Azure | 1.000.000|
| JobInput per ogni processo | 100 |
| JobOutput per ogni processo | 30 (fisso) |
| Dimensioni complete| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>(1)</sup> |
| Job per ogni account di Servizi multimediali | 50.000<sup>(2)</sup> |
| LiveEvent per ogni account di Servizi multimediali |5|
| Account di Servizi multimediali in una singola sottoscrizione | 25 (fisso) |
| StreamingPolicy | 1.000.000<sup>(3)</sup> |
| LiveOutput in esecuzione per ogni LiveEvent |3|
| LiveOutput arrestati per ogni LiveEvent |50|
| Account di archiviazione | 1000<sup>(4)</sup> (fisso) |
| Endpoint di streaming in esecuzione per ogni account di Servizi multimediali|2|
| Transform per ogni account di Servizi multimediali | 20 |
| StreamingLocator univoci associati contemporaneamente a un asset | 20<sup>(5)</sup> |
  
<sup>1</sup> Le dimensioni massime attualmente supportate per un singolo BLOB sono 5 TB in Archiviazione BLOB di Azure. Vengono tuttavia applicati altri limiti in Servizi multimediali di Azure in base alle dimensioni delle macchine virtuali usate dal servizio. Se il file di origine è superiore a 260 GB, il processo può avere esito negativo. Se le dimensioni dei contenuti 4K superano il limite di 260 GB, scrivere a amshelp@microsoft.com per delle possibili soluzioni che supportino questo scenario.

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

<sup>3</sup> È previsto un limite di 1.000.000 di voci di StreamingPolicy per i diversi criteri di Servizi multimediali (ad esempio, per i criteri di StreamingLocator o ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Se si utilizzano sempre gli stessi giorni, autorizzazioni di accesso e così via, è necessario utilizzare lo stesso ID criterio. 

<sup>4</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>5</sup> Gli oggetti StreamingLocator non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

## <a name="support-ticket"></a>Ticket di supporto

Per le risorse non fisse, è possibile richiedere l'aumento delle quote aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere informazioni dettagliate nella richiesta sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](media-services-overview.md)
