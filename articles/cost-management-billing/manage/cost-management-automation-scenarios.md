---
title: Scenari di automazione per la fatturazione e la gestione dei costi di Azure | Microsoft Docs
description: Informazioni sul mapping di scenari comuni di fatturazione e gestione dei costi ad API diverse.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c9de7d5f7661e4083d3a2f5b53368616d0e6655a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992816"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scenari di automazione per la fatturazione e la gestione dei costi

In questo articolo vengono elencati gli scenari comuni per la fatturazione e la gestione dei costi di Azure. Gli scenari sono associati alle API che è possibile usare. In ogni mapping scenario-API è possibile trovare un riepilogo delle API e delle funzionalità disponibili.

## <a name="common-scenarios"></a>Scenari comuni

È possibile usare le API di fatturazione e gestione dei costi in diversi scenari, per rispondere a domande correlate al costo e all'uso. Di seguito sono riepilogati gli scenari più comuni:

- **Riconciliazione delle fatture**: Microsoft ha addebitato il giusto importo?  Qual è la fattura? È possibile calcolarla personalmente?

- **Addebiti incrociati**: ora che so quanto mi viene addebitato, chi deve pagare?

- **Ottimizzazione dei costi**: so quanto è stato addebitato. come è possibile sfruttare al massimo il denaro investito in Azure?

- **Gestione dei costi**: voglio vedere quanto spendo e utilizzo Azure nel tempo. Quali sono le tendenze? Come è possibile ottenere un miglioramento?

- **Spesa di Azure durante il mese**: qual è la spesa per il mese corrente? Sono necessarie rettifiche alla spesa e/o all'utilizzo di Azure? Qual è il periodo del mese in cui l'utilizzo di Azure è maggiore?

- **Avvisi**: come è possibile configurare un consumo basato sulle risorse o avvisi basati su valuta?

## <a name="scenario-to-api-mapping"></a>Mapping scenario-API

|         API SmartBear Ready!        | Riconciliazione di fatture    | Addebiti incrociati    | Ottimizzazione dei costi    | Verifica dei costi    | Spesa a metà mese    | Avvisi    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budget                     |                           |                  |           X          |                  |                    |     X     |
| Addebiti per Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Elenco prezzi                 |             X             |         X        |           X          |         X        |          X         |           |
| Raccomandazioni di prenotazione |                           |                  |           X          |                  |                    |           |
| Dettagli di prenotazione         |                           |                  |           X          |         X        |                    |           |
| Riepiloghi di prenotazioni       |                           |                  |           X          |         X        |                    |           |
| Dettagli sull'utilizzo               |             X             |         X        |           X          |         X        |          X         |     X     |
| Periodi di fatturazione             |             X             |         X        |           X          |         X        |                    |           |
| Fatture                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Utilizzo non valutato               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Il mapping scenario-API non include le API per il consumo Enterprise. Se possibile, usare le API per il consumo di utilizzo generale per i nuovi scenari di sviluppo.

## <a name="api-summaries"></a>Riepiloghi API

### <a name="consumption"></a>Consumo
I clienti con accesso Web diretto ed Enterprise possono usare tutte le API seguenti, salvo diversa indicazione:

-   [API](https://docs.microsoft.com/rest/api/consumption/budgets) per i budget (*solo per clienti aziendali*): creare budget di costi o di utilizzo per risorse, gruppi di risorse o contatori di fatturazione. Dopo aver creato i budget, è possibile configurare avvisi per ricevere una notifica in caso di superamento delle soglie di budget definite. È anche possibile configurare azioni da eseguire quando gli importi dei budget vengono raggiunti.

-   [API addebiti](https://docs.microsoft.com/rest/api/consumption/marketplaces)per il Marketplace: ottenere i dati di addebito e di utilizzo in tutte le risorse di Azure Marketplace (offerte di partner Azure). È possibile usare questi dati per sommare i costi di tutte le risorse Marketplace o analizzare i costi e/o l'utilizzo per risorse specifiche.

-   [API elenco prezzi](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*solo per clienti aziendali*): Ottieni prezzi personalizzati per tutti i contatori. Combinando questi dati con i dettagli sull'utilizzo e le informazioni sull'utilizzo di Marketplace, le aziende sono in grado di calcolare i costi.

-   [API raccomandazioni di prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): ottenere consigli per l'acquisto di istanze di VM riservate. Le raccomandazioni consentono l'analisi dei risparmi previsti sui costi e degli importi degli acquisti. Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-   [API dettagli prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): vedere le informazioni sulle prenotazioni di macchine virtuali acquistate in precedenza, ad esempio la quantità di consumo riservata rispetto a quanto utilizzato. È possibile visualizzare i dati a livello di dettaglio di macchina virtuale. Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-   [API riepiloghi prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): vedere le informazioni aggregate sulle prenotazioni di macchine virtuali acquistate dall'organizzazione, ad esempio la quantità di consumo riservata rispetto alla quantità usata nell'aggregazione. Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-   [API dettagli utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails): ottenere informazioni su costi e utilizzo per tutte le risorse di Azure da Microsoft. Le informazioni sono presentate sotto forma di record di dettagli di utilizzo, che attualmente vengono generati una volta al giorno per ogni contatore. È possibile usare le informazioni per sommare i costi di tutte le risorse o analizzare i costi e/o l'utilizzo per risorse specifiche.

-   [API tariffario](/previous-versions/azure/reference/mt219005(v=azure.100)): Ottieni tariffe per i contatori se sei un cliente diretto Web. Possono quindi usare le informazioni restituite con le informazioni sull'utilizzo delle risorse per calcolare manualmente la fattura prevista.

-   [API di utilizzo senza classificazione](/previous-versions/azure/reference/mt219003(v=azure.100)): consente di ottenere informazioni di utilizzo non elaborate prima di eseguire operazioni di misurazione/ricarica da Azure.

### <a name="billing"></a>Fatturazione
-   [API periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): determinare un periodo di fatturazione da analizzare, insieme agli ID fattura per tale periodo. È possibile usare gli ID fattura con l'API Fatture.

-   [API fatture](/rest/api/billing/2019-10-01-preview/invoices): ottenere l'URL di download per una fattura per un periodo di fatturazione in formato PDF.

### <a name="enterprise-consumption"></a>Consumo Enterprise
Le API seguenti sono disponibili solo per i clienti Enterprise:

-   [API Riepilogo bilanciamento](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Ottieni un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per i servizi di Azure Marketplace, rettifiche e addebiti per le eccedenze. È possibile ottenere queste informazioni per il periodo di fatturazione corrente o per qualsiasi periodo in passato. Le aziende possono usare questi dati per confrontare gli addebiti calcolati manualmente. Questa API non fornisce informazioni specifiche per risorsa o una visualizzazione aggregata dei costi.

-   [API dettagli di utilizzo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): ottenere informazioni sull'utilizzo di Azure (di offerte Microsoft) per il mese corrente, un periodo di fatturazione specifico o un periodo di data personalizzato. Le aziende possono usare questi dati per calcolare manualmente le fatture in base alla tariffa e al consumo. Possono anche usare le informazioni relative ai reparti o alle organizzazioni per attribuire i costi alle diverse organizzazioni. I dati forniscono una visualizzazione dei costi e dell'utilizzo specifica per risorsa.

-   [API di addebito per Store Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): ottenere informazioni sull'utilizzo di Azure (di offerte partner) per il mese corrente, un periodo di fatturazione specifico o un periodo di data personalizzato. Le aziende possono usare questi dati per calcolare manualmente le fatture in base alla tariffa e al consumo. Possono anche usare le informazioni relative ai reparti o alle organizzazioni per attribuire i costi alle diverse organizzazioni. Questa API fornisce una visualizzazione dei costi e dell'utilizzo specifica per risorsa.

-   [API elenco prezzi](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): ottenere la frequenza applicabile per ogni contatore per la registrazione e il periodo di fatturazione specificati. È possibile usare le informazioni sulla tariffa in combinazione con i dettagli sull'utilizzo e con le informazioni sull'utilizzo di Marketplace per calcolare manualmente la fattura prevista.

-   [API periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): ottenere un elenco di periodi di fatturazione. L'API fornisce anche una proprietà che punta alla route API per i quattro set di dati dell'API Enterprise relativi al periodo di fatturazione: BalanceSummary, UsageDetails, addebiti per il Marketplace e Pricesheets.

-   [API raccomandazioni per le istanze riservate](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): esaminare 7 giorni, 30 giorni o 60 giorni di utilizzo della macchina virtuale e ottenere consigli per l'acquisto singolo e condiviso. È possibile usare questa API per l'analisi dei risparmi previsti sui costi e degli importi degli acquisti consigliati. Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual è la differenza tra le API di creazione di report per clienti Enterprise e le API per il consumo? In quali casi usare ciascuna?
Queste API hanno un set di funzionalità simile e possono rispondere allo stesso vasto insieme di domande nello spazio di gestione delle fatturazione e dei costi. Sono tuttavia appropriate per destinatari diversi:

- Le API di creazione di report per clienti Enterprise sono disponibili per i clienti che hanno sottoscritto un contratto Enterprise Agreement con Microsoft che concede loro accesso a impegni monetari negoziati e prezzi personalizzati. Le API richiedono una chiave, che può essere ottenuta tramite [Enterprise Portal](https://ea.azure.com). Per una descrizione di queste API, vedere [Panoramica delle API di creazione di report per i clienti Enterprise](enterprise-api.md).

- Le API per il consumo sono disponibili per tutti i clienti, con alcune eccezioni. Per altre informazioni, vedere [Panoramica delle API per il consumo di Azure](consumption-api-overview.md) e [Azure Consumption API reference](https://docs.microsoft.com/rest/api/consumption/) (Informazioni di riferimento sulle API per il consumo di Azure). È consigliabile usare le API fornite per gli scenari di sviluppo più recenti.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual è la differenza tra l'API Dettagli di utilizzo e l'API per il consumo?
Queste API forniscono essenzialmente dati diversi:

- L' [API dettagli utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornisce informazioni sull'utilizzo e sui costi di Azure per ogni istanza del contatore. I dati forniti sono già stati trasmessi al sistema di misurazione dei costi di Azure e ai dati è già stato applicato un costo, che tiene conto anche di altre possibili modifiche:

   - Modifiche all'account per l'uso di impegni monetari prepagati
   - Modifiche all'account per discrepanze di utilizzo individuate da Azure

- L'[API per il consumo](/previous-versions/azure/reference/mt219003(v=azure.100)) fornisce informazioni sull'utilizzo di Azure non elaborate prima che queste vengano trasmesse al sistema di misurazione dei costi di Azure. Questi dati potrebbero non avere alcuna correlazione con l'importo per l'utilizzo o di addebito visualizzato dopo il sistema di misurazione degli addebiti di Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual è la differenza tra l'API di fatturazione e l'API Dettagli di utilizzo?
Queste API forniscono una visualizzazione diversa degli stessi dati:

- L'[API di fatturazione](/rest/api/billing/2019-10-01-preview/invoices) è destinata solo ai clienti con accesso Web diretto. Fornisce un rollup mensile della fattura in base agli addebiti aggregati per ogni tipo di contatore.

- L'[API Dettagli sull'utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornisce una visualizzazione dettagliata dei record sull'utilizzo e sui costi per ogni giorno. Può essere usata sia dai clienti Enterprise che da quelli con accesso Web diretto.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Qual è la differenza tra l'API Elenco prezzi e l'API RateCard?
Queste API forniscono set simili di dati, ma hanno destinatari diversi:

- l'[API Elenco prezzi](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornisce i prezzi personalizzati che sono stati negoziati per un cliente Enterprise.

- L'[API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) fornisce i prezzi pubblici applicati ai clienti con accesso Web diretto.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso delle API di Azure per ottenere informazioni dettagliate a livello di codice relativamente all'utilizzo di Azure, vedere [Panoramica delle API per il consumo di Azure](consumption-api-overview.md) e [Panoramica delle API di fatturazione di Azure](usage-rate-card-overview.md).

- Per confrontare la fattura con il file dettagliato sull'utilizzo giornaliero e i report di gestione dei costi nel portale di Azure, vedere [Comprendere la fattura per Microsoft Azure](../understand/review-individual-bill.md).

- In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
