---

title: Panoramica degli endpoint di streaming dei Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento offre una panoramica degli endpoint di streaming dei Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 7e18325426bac86d89ff0235e18ddca9fd21e8de
ms.openlocfilehash: 41a58493c8931e8ce241ce31ecb1ca20b5a82cb3


---
# <a name="streaming-endpoints-overview"></a>Panoramica degli endpoint di streaming 

##<a name="overview"></a>Panoramica

In Servizi multimediali di Microsoft Azure (AMS) un **endpoint di streaming** rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete CDN (rete per la distribuzione di contenuti) per la successiva distribuzione. Servizi multimediali fornisce inoltre un'integrazione completa della rete CDN di Azure. Il flusso in uscita da un servizio StreamingEndpoint può essere costituito da un flusso live, da un "video on demand" o da un download progressivo dell'asset associato a un account di Servizi multimediali. Ogni account di Servizi multimediali di Azure include uno StreamingEndpoint predefinito. Nell'account è possibile creare altri StreamingEndpoint. Esistono due versioni di StreamingEndpoints, 1.0 e 2.0. A partire dal 10 gennaio 2017, ogni account di AMS appena creato includerà lo StreamingEndpoint **predefinito** della versione 2.0. Anche gli altri endpoint di streaming che verranno aggiunti a questo account avranno la versione 2.0. Questa modifica non influisce sugli account esistenti: gli StreamingEndpoint esistenti presenteranno la versione 1.0 e possono passare alla versione 2.0. Questa modifica influenzerà il comportamento, la fatturazione e le funzionalità (per altre informazioni, vedere la sezione **Tipologie e versioni di streaming** documentata di seguito).

Inoltre, a partire dalla versione 2.15 (rilasciata a gennaio 2017), i Servizi multimediali di Azure hanno aggiunto le proprietà seguenti all'entità endpoint di streaming: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Per una panoramica dettagliata di queste proprietà, vedere [questo articolo](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Quando si crea un account dei Servizi multimediali di Azure viene creato un endpoint di streaming standard predefinito nello stato **Interrotto**. Gli endpoint di streaming predefiniti non possono essere eliminati. In base alla disponibilità della rete CDN di Azure nell'area di destinazione, l'endpoint di streaming predefinito appena creato per impostazione predefinita include anche l'integrazione del provider CDN "StandardVerizon". 

>[!NOTE]
>L'integrazione della rete CDN di Azure può essere disabilitata prima di avviare l'endpoint di streaming.

Questo argomento riporta informazioni generali sulle funzionalità principali fornite dagli endpoint di streaming.

## <a name="streaming-types-and-versions"></a>Tipologie e versioni di streaming

### <a name="standardpremium-types-version-20"></a>Tipologia standard o Premium (versione 2.0)

A partire dalla versione dei Servizi multimediali rilasciata a gennaio 2017, ci sono due tipologie di streaming: **Standard** e **Premium**. Queste tipologie fanno parte della versione dell'endpoint di streaming "2.0".

Tipo|Descrizione
---|---
**Standard**|Questa è l'opzione predefinita che viene utilizzata nella maggior parte dei casi.<br/>Con questa opzione, si crea un contratto di servizio predefinito/limitato e i primi 15 giorni dopo l'avvio dell'endpoint di streaming sono gratuiti.<br/>Se si creano più di un endpoint di streaming, solo il primo è gratuito per i primi 15 giorni, gli altri vengono addebitati non appena vengono avviati. <br/>Si noti che la versione di valutazione gratuita è applicabile solo per gli account di servizi multimediali nuovi e per l'endpoint di streaming predefinito. Gli endpoint di streaming esistenti e gli endpoint di streaming creati in seguito non includono il periodo di prova gratuito anche se questi vengono aggiornati alla versione 2.0 o vengono creati con la versione 2.0.
**Premium**|Questa opzione è adatta ai professionisti che hanno bisogno di una maggiore scalabilità o di maggior controllo.<br/>Diversi tipi di contratto di servizio in base alla capacità dell'unità di streaming (SU) premium acquistata, endpoint di streaming live dedicati in un ambiente isolato e nessuna competizione per le risorse.

Per ulteriori informazioni, vedere la sezione **Confronto tra le tipologie di streaming** seguente.

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

|Tipo|StreamingEndpointVersion|ScaleUnits|RETE CDN|Fatturazione|Contratto di servizio| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Classico|1.0|0|ND|Free|ND|
|Endpoint di streaming Standard|2.0|0|Sì|A pagamento|Sì|
|Unità di streaming Premium|1.0|>0|Sì|A pagamento|Sì|
|Unità di streaming Premium|2.0|>0|Sì|A pagamento|Sì|

### <a name="features"></a>Funzionalità

Funzionalità|Standard|Premium
---|---|---
Gratis per i primi 15 giorni| Sì |No
Velocità effettiva |Fino a 600 Mbps quando non si usa la rete CDN di Azure. Scalabilità con la rete CDN.|200 Mbps per unità di streaming (SU). Scalabilità con la rete CDN.
Contratto di servizio | 99,9|99,9 (200 Mbps per SU).
RETE CDN|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.
Fatturazione con ripartizione proporzionale| Giornaliera|Giornaliera
Crittografia dinamica|Sì|Sì
creazione dinamica dei pacchetti|Sì|Sì
Scalabilità|Scalabilità automatica fino alla velocità effettiva di destinazione.|Unità di streaming aggiuntive
Host con filtro IP/G20/personalizzato|Sì|Sì
Download progressivo|Sì|Sì
Uso consigliato |Consigliato per la maggior parte dei casi di streaming.|Uso professionale.<br/>Per esigenze superiori alle funzionalità offerte dalla tipologia Standard. Se si prevede un numero di destinatari simultanei superiore a 50.000 visualizzatori, contattare Microsoft (amsstreaming all'indirizzo microsoft.com).


## <a name="migration-between-types"></a>Migrazione tra le tipologie

Da | To | Azione
---|---|---
Classico|Standard|È necessario fornire il consenso esplicito
Classico|Premium| Scalabilità (unità di streaming aggiuntive)
Standard/Premium|Classico|Non disponibile (se la versione dell'endpoint di streaming è 1.0. È consentito modificare la tipologia classica impostando Scaleunits su "0")
Standard (con/senza la rete CDN)|Premium con le stesse configurazioni|Consentito nello stato **avviato**. (tramite il Portale di Azure)
Premium (con/senza la rete CDN)|Standard con le stesse configurazioni|Consentito nello stato **avvio** (tramite il Portale di Azure)
Standard (con/senza la rete CDN)|Premium con diverse configurazioni|Consentito nello stato **interrotto** (tramite il Portale di Azure). Non consentito nello stato di esecuzione.
Premium (con/senza la rete CDN)|Standard con diverse configurazioni|Consentito nello stato **interrotto** (tramite il Portale di Azure). Non consentito nello stato di esecuzione.
Versione 1.0 con SU >=1 con la rete CDN|Standard/Premium con nessuna rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**.
Versione 1.0 con SU >=1 con la rete CDN|Standard con/senza la rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**. Verrà eliminata la rete CDN della versione 1.0 e ne verrà creata e avviata una nuova.
Versione 1.0 con SU >=1 con la rete CDN|Premium con/senza la rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**. Verrà eliminata la rete CDN della tipologia classica e ne verrà creata e avviata una nuova.

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO3-->


