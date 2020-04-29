---
title: Quote e limiti in servizi multimediali di Azure
description: Questo argomento descrive le quote e i limiti in Servizi multimediali di Microsoft Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2020
ms.author: juliako
ms.openlocfilehash: e9855a02a57d71793f3a5d6c5ce01cbfda341b70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632218"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Quote e limiti di servizi multimediali di Azure

Questo articolo elenca alcuni dei limiti di Servizi multimediali di Microsoft Azure più comuni, che sono anche detti quote.

> [!NOTE]
> Per le risorse che non sono fisse, aprire un ticket di supporto per richiedere un aumento delle quote. Non creare altri account di servizi multimediali di Azure in un tentativo di ottenere limiti più elevati.

## <a name="account-limits"></a>Limiti dell'account

| Risorsa | Limite predefinito | 
| --- | --- | 
| [Account di servizi multimediali](media-services-account-concept.md) in una singola sottoscrizione | 25 (fisso) |

## <a name="asset-limits"></a>Limiti degli asset

| Risorsa | Limite predefinito | 
| --- | --- | 
| [Asset](assets-concept.md) per account di servizi multimediali | 1.000.000|

## <a name="storage-limits"></a>Limiti relativi ad Archiviazione

| Risorsa | Limite predefinito | 
| --- | --- | 
| Dimensione del file| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>1</sup> |
| [Account di archiviazione](storage-account-concept.md) | 100<sup>(2)</sup> (fisso) |

<sup>1</sup> Le dimensioni massime supportate per un BLOB singolo corrispondono a 5 TB in Archiviazione BLOB di Azure. I limiti aggiuntivi si applicano a servizi multimediali in base alle dimensioni delle macchine virtuali usate dal servizio. Il limite delle dimensioni si applica ai file caricati e anche ai file generati come risultato dell'elaborazione di servizi multimediali (codifica o analisi). Se le dimensioni del file di origine sono maggiori di 260 GB, è probabile che il processo abbia esito negativo. 

La tabella seguente illustra i limiti di media reserved Unit S1, S2 e S3. Se il file di origine è superiore ai limiti definiti nella tabella, il processo di codifica non riesce. Se si codificano origini di risoluzione 4K di lunga durata, è necessario usare le unità riservate S3 media per ottenere le prestazioni necessarie. Se si dispone di contenuto 4K superiore al limite di 260 GB per le unità riservate S3 media, aprire un ticket di supporto.

|Media reserved Unit Type|Dimensioni massime input (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> gli account di archiviazione devono provenire dalla stessa sottoscrizione di Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Limiti di processi (codifica & analisi)

| Risorsa | Limite predefinito | 
| --- | --- | 
| [Processi](transforms-jobs-concept.md) per account di servizi multimediali | 500.000 <sup>(3)</sup> (fisso)|
| Input per processo | 50 (fisso)|
| Output processo per processo | 20 (fisso) |
| [Trasformazioni](transforms-jobs-concept.md) per ogni account di servizi multimediali | 100 (fisso)|
| Trasformare gli output in una trasformazione | 20 (fisso) |
| File per input del processo|10 (fisso)|

<sup>3</sup> questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. 

Tutti i record di processo presenti nell'account e in esecuzione da almeno 90 giorni verranno eliminati automaticamente, anche se il numero totale di record è inferiore alla quota massima. 

## <a name="live-streaming-limits"></a>Limiti di streaming live

| Risorsa | Limite predefinito | 
| --- | --- | 
| [Eventi live](live-events-outputs-concept.md) <sup>(4)</sup> per account di servizi multimediali |5|
| Output Live per evento Live |3 <sup>(5)</sup> |
| Durata max output Live | 25 ore |

<sup>4</sup> per informazioni dettagliate sui limiti degli eventi live, vedere [confronto e limiti di tipi di eventi live](live-event-types-comparison.md).

<sup>5</sup> gli output Live iniziano alla creazione e si interrompono quando vengono eliminati.

## <a name="packaging--delivery-limits"></a>Creazione di pacchetti di limiti di recapito &

| Risorsa | Limite predefinito | 
| --- | --- | 
| [Endpoint di streaming](streaming-endpoint-concept.md) (arrestati o in esecuzione) per ogni account di servizi multimediali|2 |
| [Filtri manifesto dinamico](filters-dynamic-manifest-overview.md)|100|
| [Criteri di streaming](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| [Localizzatori di streaming](streaming-locators-concept.md) univoci associati a un asset in una sola volta | 100<sup>(7)</sup> (fisso) |

<sup>6</sup> quando si usa un [criterio di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizzato, è necessario progettare un set limitato di tali criteri per l'account del servizio multimediale e riutilizzarli per la StreamingLocators ogni volta che sono necessarie le stesse opzioni e protocolli di crittografia. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

<sup>7</sup> i localizzatori di streaming non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

## <a name="protection-limits"></a>Limiti di protezione

| Risorsa | Limite predefinito | 
| --- | --- | 
| Opzioni per [criterio chiave](content-key-policy-concept.md) simmetrica |30 | 
| Licenze al mese per ogni tipo di DRM nel servizio di distribuzione delle chiavi di servizi multimediali per account|1.000.000|

## <a name="support-ticket"></a>Ticket di supporto

Per le risorse non corrette, è possibile richiedere la generazione delle quote aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Includere nella richiesta informazioni dettagliate sulle modifiche alle quote desiderate, sugli scenari di utilizzo e sulle aree necessarie. <br/>**Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](media-services-overview.md)
