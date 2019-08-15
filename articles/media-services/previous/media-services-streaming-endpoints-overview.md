---
title: Panoramica degli endpoint di streaming dei Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento offre una panoramica degli endpoint di streaming dei Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ac9c9a73e52c678c8a6d9b1e1779d9ec75cab2c8
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "69016456"
---
# <a name="streaming-endpoints-overview"></a>Panoramica degli endpoint di streaming  

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](https://docs.microsoft.com/azure/media-services/latest/). Vedere anche [linee guida sulla migrazione da V2 a V3](../latest/migrate-from-v2-to-v3.md)

In Servizi multimediali di Microsoft Azure (AMS) un **endpoint di streaming** rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete CDN (rete per la distribuzione di contenuti) per la successiva distribuzione. Servizi multimediali fornisce inoltre un'integrazione completa della rete CDN di Azure. Il flusso in uscita da un servizio StreamingEndpoint può essere costituito da un flusso live, da un "video on demand" o da un download progressivo dell'asset associato a un account di Servizi multimediali. Ogni account di Servizi multimediali di Azure include un servizio StreamingEndpoint predefinito. Nell'account è possibile creare altri servizi StreamingEndpoint. Esistono due versioni di servizi StreamingEndpoint, ovvero 1.0 e 2.0. A partire dal 10 gennaio 2017, ogni account di AMS appena creato includerà lo StreamingEndpoint **predefinito** della versione 2.0. Anche gli altri endpoint di streaming che verranno aggiunti a questo account avranno la versione 2.0. Questa modifica non influisce sugli account esistenti: gli StreamingEndpoint esistenti presenteranno la versione 1.0 e possono passare alla versione 2.0. Questa modifica influenzerà il comportamento, la fatturazione e le funzionalità (per altre informazioni, vedere la sezione **Tipologie e versioni di streaming** documentata di seguito).

A Servizi multimediali di Azure sono state aggiunte all'entità endpoint di streaming le proprietà seguenti: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Per una panoramica dettagliata di queste proprietà, vedere [questo articolo](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Quando si crea un account dei Servizi multimediali di Azure viene creato un endpoint di streaming standard predefinito nello stato **Interrotto**. Gli endpoint di streaming predefiniti non possono essere eliminati. In base alla disponibilità della rete CDN di Azure nell'area di destinazione, l'endpoint di streaming predefinito appena creato per impostazione predefinita include anche l'integrazione del provider CDN "StandardVerizon". 
                
> [!NOTE]
> L'integrazione della rete CDN di Azure può essere disabilitata prima di avviare l'endpoint di streaming. `hostname` e l'URL di streaming rimangono invariati indipendentemente dal fatto che venga abilitata o meno la rete CDN.

Questo argomento riporta informazioni generali sulle funzionalità principali fornite dagli endpoint di streaming.

## <a name="naming-conventions"></a>Convenzioni di denominazione

Per l'endpoint predefinito: `{AccountName}.streaming.mediaservices.windows.net`

Per qualsiasi altro endpoint: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Tipologie e versioni di streaming

### <a name="standardpremium-types-version-20"></a>Tipologia standard o Premium (versione 2.0)

A partire dalla versione dei Servizi multimediali rilasciata a gennaio 2017, ci sono due tipologie di streaming: **Standard** (anteprima) e **Premium**. Queste tipologie fanno parte della versione dell'endpoint di streaming "2.0".


|Type|Descrizione|
|--------|--------|  
|**Standard**|L'endpoint di streaming predefinito è un tipo **standard** , può essere modificato nel tipo Premium regolando le unità di streaming.|
|**Premium** |Questa opzione è adatta ai professionisti che hanno bisogno di una maggiore scalabilità o di maggior controllo. Si passa a un tipo **Premium** modificando le unità di streaming.<br/>Gli endpoint di streaming dedicati si trovano in un ambiente isolato e non competono per le risorse.|

Per i clienti che desiderano distribuire contenuti a destinatari Internet di grandi dimensioni, è consigliabile abilitare la rete CDN nell'endpoint di streaming.

Per ulteriori informazioni, vedere la sezione [Confronto tra le tipologie di streaming](#comparing-streaming-types) seguente.

### <a name="classic-type-version-10"></a>Tipologia classica (versione 1.0)

Per gli utenti che hanno creato gli account AMS prima della versione del 10 gennaio 2017, la tipologia di endpoint di streaming disponibile è quella **classica**. Questa tipologia fa parte della versione dell'endpoint di streaming "1.0".

Se l'endpoint di streaming **versione "1.0"** ha una o più unità di streaming (SU) premium, si tratta di un endpoint di streaming premium che offre tutte le funzionalità AMS (come le tipologie **Standard/Premium**) senza richiedere altri passaggi di configurazione.

>[!NOTE]
>Gli endpoint di streaming **classici** (versione "1.0" e 0 SU), offrono funzionalità limitate e non includono un contratto di servizio. È consigliabile eseguire la migrazione alla tipologia **Standard** per ottenere una migliore esperienza e usare funzionalità come la creazione dinamica dei pacchetti o la crittografia e le altre funzionalità offerte con la tipologia **Standard**. Per migrare alla tipologia **Standard**, andare al [Portale di Azure](https://portal.azure.com/) e selezionare **Acconsenti esplicitamente al piano Standard**. Per altre informazioni, consultare la sezione sulla [migrazione](#migration-between-types).
>
>Tenere presente che questa operazione non può essere sottoposta a rollback e influisce sul prezzo.
>
 
## <a name="comparing-streaming-types"></a>Confronto tra le tipologie di streaming

### <a name="versions"></a>Versioni

|Type|StreamingEndpointVersion|ScaleUnits|RETE CDN|Fatturazione|
|--------------|----------|-----------------|-----------------|-----------------|
|Classica|1.0|0|ND|Gratuito|
|Endpoint di streaming standard (anteprima)|2.0|0|Sì|A pagamento|
|Unità di streaming Premium|1.0|>0|Sì|A pagamento|
|Unità di streaming Premium|2.0|>0|Yes|A pagamento|

### <a name="features"></a>Funzionalità

Funzionalità|Standard|Premium
---|---|---
Velocità effettiva |Fino a 600 Mbps e possono fornire una velocità effettiva molto più elevata quando si usa una rete CDN.|200 Mbps per unità di streaming (SU). Può fornire una velocità effettiva molto più elevata quando viene usata una rete CDN.
RETE CDN|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.
Fatturazione con ripartizione proporzionale| Ogni giorno|Ogni giorno
Crittografia dinamica|Yes|Sì
creazione dinamica dei pacchetti|Sì|Yes
Scalabilità|Scalabilità automatica fino alla velocità effettiva di destinazione.|Unità di streaming aggiuntive.
Filtro IP/G20/host personalizzato <sup>1</sup>|Yes|Sì
Download progressivo|Yes|Sì
Uso consigliato |Consigliato per la maggior parte dei casi di streaming.|Uso professionale. 

<sup>1</sup> usato direttamente sull'endpoint di streaming quando la rete CDN non è abilitata nell'endpoint.<br/>

Per informazioni sul contratto di contratto, vedere [prezzi e contratto di contratto](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migrazione tra le tipologie

Da | A | Azione
---|---|---
Classica|Standard|È necessario fornire il consenso esplicito
Classica|Premium| Scalabilità (unità di streaming aggiuntive)
Standard/Premium|Classica|Non disponibile (se la versione dell'endpoint di streaming è 1.0. È consentito modificare la tipologia classica impostando Scaleunits su "0")
Standard (con/senza la rete CDN)|Premium con le stesse configurazioni|Consentito nello stato **avviato**. (tramite il Portale di Azure)
Premium (con/senza la rete CDN)|Standard con le stesse configurazioni|Consentito nello stato **avvio** (tramite il Portale di Azure)
Standard (con/senza la rete CDN)|Premium con diverse configurazioni|Consentito nello stato **interrotto** (tramite il Portale di Azure). Non consentito nello stato di esecuzione.
Premium (con/senza la rete CDN)|Standard con diverse configurazioni|Consentito nello stato **interrotto** (tramite il Portale di Azure). Non consentito nello stato di esecuzione.
Versione 1.0 con SU >=1 con la rete CDN|Standard/Premium con nessuna rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**.
Versione 1.0 con SU >=1 con la rete CDN|Standard con/senza la rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**. Verrà eliminata la rete CDN della versione 1.0 e ne verrà creata e avviata una nuova.
Versione 1.0 con SU >=1 con la rete CDN|Premium con/senza la rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**. Verrà eliminata la rete CDN della tipologia classica e ne verrà creata e avviata una nuova.

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

