---
title: Quote e limitazioni in Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo argomento descrive quote e limitazioni in Servizi multimediali di Microsoft Azure v3.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888428"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quote e limitazioni in Servizi multimediali di Azure v3

Questo articolo descrive le quote e le limitazioni in Servizi multimediali di Azure v3.

| Risorsa | Limite predefinito | 
| --- | --- | 
| Asset per ogni account di Servizi multimediali di Azure | 1.000.000|
| Filtri manifesto dinamico|100|
| JobInput per ogni processo | 50 (fisso)|
| JobOutput per ogni processo | 20 (fisso) |
| TransformOutputs in a Transform | 20 (fisso) |
| File per ogni JobInput|10 (fisso)|
| Dimensione del file| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>(1)</sup> |
| Job per ogni account di Servizi multimediali | 500.000 <sup>(2)</sup> (fisso)|
| Eventi live per ogni account di Servizi multimediali |5|
| Account di Servizi multimediali in una singola sottoscrizione | 25 (fisso) |
| Uscite dal vivo per evento live |3 <sup>(3)</sup> |
| Durata massima dell'uscita live | 25 ore |
| Account di archiviazione | 100<sup>(4)</sup> (fisso) |
| Endpoint di streaming (interrotti o in esecuzione) per account di Servizi multimediali|2 (fisso)|
| Criteri di streaming | 100 <sup>(5)</sup> |
| Transform per ogni account di Servizi multimediali | 100 (fisso)|
| Localizzatori di streaming univoci associati contemporaneamente a un asset | 100<sup>(6)</sup> (fisso) |
| Opzioni per criteri chiave simmetricaOptions per Content Key Policy |30 | 
| Licenze al mese per ognuno dei tipi DRM nel servizio di distribuzione delle chiavi di Servizi multimediali per account|1.000.000|

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. In Servizi multimediali si applicano limiti aggiuntivi in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite di dimensione si applica ai file caricati e anche ai file generati a seguito dell'elaborazione di Servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

Nella tabella seguente vengono illustrati i limiti delle unità riservate ai supporti S1, S2 e S3. Se il file di origine supera i limiti definiti nella tabella, il processo di codifica ha esito negativo. Se si codificano origini di risoluzione 4K di lunga durata, è necessario utilizzare unità riservate ai supporti S3 per ottenere le prestazioni necessarie. Se hai contenuti 4K superiori al limite di 260 GB per le unità riservate ai supporti S3, apri un ticket di supporto.

|Tipo di unità riservata ai supporti   |Dimensione massima di input (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Questo numero include processi in coda, completati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

<sup>3</sup> Live Outputs iniziano al momento della creazione e si interrompono quando vengono eliminati.

<sup>4</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>5</sup> Quando si utilizza un criterio di [streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizzato, è consigliabile progettare un set limitato di tali criteri per l'account del servizio multimediale e riutilizzarli per streamingLocators ogni volta che sono necessarie le stesse opzioni e protocolli di crittografia. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

<sup>6</sup> I localizzatori di streaming non sono progettati per la gestione del controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

## <a name="support-ticket"></a>Ticket di supporto

Per le risorse che non sono fisse, è possibile richiedere l'aumento delle quote, aprendo un ticket di [supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](media-services-overview.md)
