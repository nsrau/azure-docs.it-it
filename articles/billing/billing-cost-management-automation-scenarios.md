---
title: Scenari di automazione per la fatturazione e la gestione dei costi di Azure | Microsoft Docs
description: Informazioni sul mapping di scenari comuni di fatturazione e gestione dei costi ad API diverse.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: cb7a13d9abcf7c677d51f03df002ea06b543014e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232472"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scenari di automazione per la fatturazione e la gestione dei costi

In questo articolo vengono elencati gli scenari comuni per la fatturazione e la gestione dei costi di Azure. Gli scenari sono associati alle API che è possibile usare. In ogni mapping scenario-API è possibile trovare un riepilogo delle API e delle funzionalità disponibili.

## <a name="common-scenarios"></a>Scenari comuni

È possibile usare le API di fatturazione e gestione dei costi in diversi scenari, per rispondere a domande correlate al costo e all'uso. Di seguito sono riepilogati gli scenari più comuni:

- **Riconciliazione delle fatture**: l'importo addebitato da Microsoft è corretto?  Qual è la fattura? È possibile calcolarla personalmente?

- **Addebiti incrociati**: l'importo addebitato è chiaro, ma chi è il responsabile del pagamento all'interno dell'organizzazione?

- **Ottimizzazione dei costi**: conoscendo l'importo dell'addebito, come è possibile sfruttare al massimo il denaro investito in Azure?

- **Verifica dei costi**: come è possibile verificare quanto si spende e quanto si usa Azure nel tempo? Quali sono le tendenze? Come è possibile ottenere un miglioramento?

- **Spesa di Azure durante il mese**: qual è la spesa mensile corrente fino a oggi? Sono necessarie rettifiche alla spesa e/o all'utilizzo di Azure? Qual è il periodo del mese in cui l'utilizzo di Azure è maggiore?

- **Avvisi**: come è possibile impostare avvisi relativi al consumo basato sulle risorse o basati sull'importo monetario?

## <a name="scenario-to-api-mapping"></a>Mapping scenario-API

|         API        | Riconciliazione di fatture    | Addebiti incrociati    | Ottimizzazione dei costi    | Verifica dei costi    | Spesa a metà mese    | Avvisi    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budget                     |                           |                  |           X          |                  |                    |     X     |
| Addebiti per Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Elenco prezzi                 |             X             |         X        |           X          |         X        |          X         |           |
| Raccomandazioni di prenotazione |                           |                  |           X          |                  |                    |           |
| Dettagli di prenotazione         |                           |                  |           X          |         X        |                    |           |
| Riepiloghi di prenotazioni       |                           |                  |           X          |         X        |                    |           |
| Dettagli utilizzo               |             X             |         X        |           X          |         X        |          X         |     X     |
| Periodi di fatturazione             |             X             |         X        |           X          |         X        |                    |           |
| Fatture                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Utilizzo non valutato               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Il mapping scenario-API non include le API per il consumo Enterprise. Se possibile, usare le API per il consumo di utilizzo generale per i nuovi scenari di sviluppo.

## <a name="api-summaries"></a>Riepiloghi API

### <a name="consumption"></a>Consumo
I clienti con accesso Web diretto ed Enterprise possono usare tutte le API seguenti, salvo diversa indicazione:

-   [API Budget](https://docs.microsoft.com/rest/api/consumption/budgets) (*solo clienti Enterprise*): creare budget di costo o di utilizzo per risorse, gruppi di risorse o contatori di fatturazione. Dopo aver creato i budget, è possibile configurare avvisi per ricevere una notifica in caso di superamento delle soglie di budget definite. È anche possibile configurare azioni da eseguire quando gli importi dei budget vengono raggiunti.

-   [API Addebiti per Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): Ottenere dati sull'utilizzo e sui costi per tutte le risorse di Azure Marketplace (offerte dei partner di Azure). È possibile usare questi dati per sommare i costi di tutte le risorse Marketplace o analizzare i costi e/o l'utilizzo per risorse specifiche.

-   [API Elenco prezzi](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*solo clienti Enterprise*): ottenere i prezzi personalizzati per tutti i contatori. Combinando questi dati con i dettagli sull'utilizzo e le informazioni sull'utilizzo di Marketplace, le aziende sono in grado di calcolare i costi. 

-   [API Raccomandazioni di prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): ottenere consigli per l'acquisto di istanze di macchina virtuale riservate. Le raccomandazioni consentono l'analisi dei risparmi previsti sui costi e degli importi degli acquisti. Per altre informazioni, vedere [APIs for Azure reservation automation](billing-reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-   [API Dettagli di prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): visualizzare le informazioni relative a prenotazioni di macchine virtuali acquistate in precedenza, ad esempio il consumo prenotato rispetto all'utilizzo effettivo. È possibile visualizzare i dati a livello di dettaglio di macchina virtuale. Per altre informazioni, vedere [APIs for Azure reservation automation](billing-reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-   [API Riepiloghi di prenotazioni](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): visualizzare informazioni aggregate relative alle prenotazioni di macchine virtuali acquistate dall'organizzazione, ad esempio il consumo riservato rispetto all'utilizzo effettivo nell'aggregazione. Per altre informazioni, vedere [APIs for Azure reservation automation](billing-reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-   [API Dettagli sull'utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails): ottenere informazioni sui costi e l'utilizzo per tutte le risorse di Azure da Microsoft. Le informazioni sono presentate sotto forma di record di dettagli di utilizzo, che attualmente vengono generati una volta al giorno per ogni contatore. È possibile usare le informazioni per sommare i costi di tutte le risorse o analizzare i costi e/o l'utilizzo per risorse specifiche.

-   [API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): i clienti con accesso Web diretto possono ottenere le tariffe dei propri contatori. Possono quindi usare le informazioni restituite con le informazioni sull'utilizzo delle risorse per calcolare manualmente la fattura prevista. 

-   [API per i dati di utilizzo senza classificazione](/previous-versions/azure/reference/mt219003(v=azure.100)): ottenere informazioni non elaborate sull'utilizzo prima che Azure esegua qualsiasi misurazione/addebito.

### <a name="billing"></a>Fatturazione
-   [API per i periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): determinare un periodo di fatturazione da analizzare, insieme agli ID fattura per il periodo. È possibile usare gli ID fattura con l'API Fatture.

-   [API Fatture](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices): ottenere l'URL di download di una fattura per un periodo di fatturazione in formato PDF.

### <a name="enterprise-consumption"></a>Consumo Enterprise
Le API seguenti sono disponibili solo per i clienti Enterprise:

-   [API di riepilogo saldi](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): ottenere un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace e spese per modifiche ed eccedenze. È possibile ottenere queste informazioni per il periodo di fatturazione corrente o per qualsiasi periodo in passato. Le aziende possono usare questi dati per confrontare gli addebiti calcolati manualmente. Questa API non fornisce informazioni specifiche per risorsa o una visualizzazione aggregata dei costi.

-   [API Dettagli sull'utilizzo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): ottenere le informazioni sull'utilizzo di Azure (delle offerte Microsoft) per il mese corrente, per un periodo di fatturazione specifico o per un intervallo di date personalizzato. Le aziende possono usare questi dati per calcolare manualmente le fatture in base alla tariffa e al consumo. Possono anche usare le informazioni relative ai reparti o alle organizzazioni per attribuire i costi alle diverse organizzazioni. I dati forniscono una visualizzazione dei costi e dell'utilizzo specifica per risorsa.

-   [API per le spese in Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): ottenere le informazioni sull'utilizzo di Azure (delle offerte dei partner) per il mese corrente, per un periodo di fatturazione specifico o per un intervallo di date personalizzato. Le aziende possono usare questi dati per calcolare manualmente le fatture in base alla tariffa e al consumo. Possono anche usare le informazioni relative ai reparti o alle organizzazioni per attribuire i costi alle diverse organizzazioni. Questa API fornisce una visualizzazione dei costi e dell'utilizzo specifica per risorsa.

-   [API Elenco prezzi](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): ottenere la tariffa applicabile per ogni contatore per la registrazione e il periodo di fatturazione specificati. È possibile usare le informazioni sulla tariffa in combinazione con i dettagli sull'utilizzo e con le informazioni sull'utilizzo di Marketplace per calcolare manualmente la fattura prevista.

-   [API per i periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): ottenere un elenco dei periodi di fatturazione. L'API offre inoltre una proprietà che punta alla route API per i quattro set di dati dell'API Enterprise che riguardano il periodo di fatturazione: BalanceSummary, UsageDetails, MarketplaceCharges e PriceSheet.

-   [API per le raccomandazioni sulle prenotazioni di istanze riservate](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): esaminare 7, 30 o 60 giorni di utilizzo della macchina virtuale e ottenere raccomandazioni per acquisti singoli e condivisi. È possibile usare questa API per l'analisi dei risparmi previsti sui costi e degli importi degli acquisti consigliati. Per altre informazioni, vedere [APIs for Azure reservation automation](billing-reservation-apis.md) (API per l'automazione della prenotazione in Azure).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual è la differenza tra le API di creazione di report per clienti Enterprise e le API per il consumo? In quali casi usare ciascuna?
Queste API hanno un set di funzionalità simile e possono rispondere allo stesso vasto insieme di domande nello spazio di gestione delle fatturazione e dei costi. Sono tuttavia appropriate per destinatari diversi: 

- Le API di creazione di report per clienti Enterprise sono disponibili per i clienti che hanno sottoscritto un contratto Enterprise Agreement con Microsoft che concede loro accesso a impegni monetari negoziati e prezzi personalizzati. Le API richiedono una chiave, che può essere ottenuta tramite [Enterprise Portal](https://ea.azure.com). Per una descrizione di queste API, vedere [Panoramica delle API di creazione di report per i clienti Enterprise](billing-enterprise-api.md).

- Le API per il consumo sono disponibili per tutti i clienti, con alcune eccezioni. Per altre informazioni, vedere [Panoramica delle API per il consumo di Azure](billing-consumption-api-overview.md) e [Azure Consumption API reference](https://docs.microsoft.com/rest/api/consumption/) (Informazioni di riferimento sulle API per il consumo di Azure). È consigliabile usare le API fornite per gli scenari di sviluppo più recenti. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual è la differenza tra l'API Dettagli di utilizzo e l'API per il consumo?
Queste API forniscono essenzialmente dati diversi:

- l'[API Dettagli di utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornisce informazioni su utilizzo e costi di Azure per ogni istanza di contatore. I dati forniti sono già stati trasmessi al sistema di misurazione dei costi di Azure e ai dati è già stato applicato un costo, che tiene conto anche di altre possibili modifiche:

   - Modifiche all'account per l'uso di impegni monetari prepagati
   - Modifiche all'account per discrepanze di utilizzo individuate da Azure

- L'[API per il consumo](/previous-versions/azure/reference/mt219003(v=azure.100)) fornisce informazioni sull'utilizzo di Azure non elaborate prima che queste vengano trasmesse al sistema di misurazione dei costi di Azure. Questi dati potrebbero non avere alcuna correlazione con l'importo per l'utilizzo o di addebito visualizzato dopo il sistema di misurazione degli addebiti di Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual è la differenza tra l'API di fatturazione e l'API Dettagli di utilizzo?
Queste API forniscono una visualizzazione diversa degli stessi dati:

- L'[API di fatturazione](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) è destinata solo ai clienti con accesso Web diretto. Fornisce un rollup mensile della fattura in base agli addebiti aggregati per ogni tipo di contatore. 

- L'[API Dettagli sull'utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornisce una visualizzazione dettagliata dei record sull'utilizzo e sui costi per ogni giorno. Può essere usata sia dai clienti Enterprise che da quelli con accesso Web diretto.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Qual è la differenza tra l'API Elenco prezzi e l'API RateCard?
Queste API forniscono set simili di dati, ma hanno destinatari diversi:

- l'[API Elenco prezzi](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornisce i prezzi personalizzati che sono stati negoziati per un cliente Enterprise.

- L'[API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) fornisce i prezzi pubblici applicati ai clienti con accesso Web diretto.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso delle API di Azure per ottenere informazioni dettagliate a livello di codice relativamente all'utilizzo di Azure, vedere [Panoramica delle API per il consumo di Azure](billing-consumption-api-overview.md) e [Panoramica delle API di fatturazione di Azure](billing-usage-rate-card-overview.md).

- Per confrontare la fattura con il file dettagliato sull'utilizzo giornaliero e i report di gestione dei costi nel portale di Azure, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

- Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
