---
title: Scenari di automazione della fatturazione e della gestione dei costi di Azure | Microsoft Docs
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
ms.openlocfilehash: 09ecd46837a12f5f1a8278b1644dc099701bcd00
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584724"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Scenari di automazione della fatturazione e della gestione dei costi

Gli scenari più comuni di fatturazione e gestione dei costi sono identificati di seguito, con il mapping ad API diverse utilizzabili in tali scenari. È disponibile un riepilogo di tutte le API disponibili. È possibile trovare le funzionalità offerte da queste nel mapping API-scenario. 

## <a name="common-scenarios"></a>Scenari comuni

È possibile usare le API di fatturazione e gestione dei costi in un'ampia varietà di scenari, per rispondere a domande correlate al costo e all'utilizzo.  Di seguito viene presentata una struttura di scenari comuni.

- **Riconciliazione delle fatture**: l'importo addebitato da Microsoft è corretto?  Qual è la fattura? È possibile calcolarla personalmente?

- **Addebiti incrociati**: l'importo addebitato è chiaro, ma chi è il responsabile del pagamento all'interno dell'organizzazione?

- **Ottimizzazione dei costi**: conoscendo l'importo da pagare, come è possibile sfruttare al massimo l'importo speso per Azure?

- **Verifica dei costi**: come si fa a verificare quanto si spende per Azure, quanto si usa questo servizio nel tempo e Quali sono le tendenze? Come si può ottenere una maggiore efficienza?

- **Spesa di Azure durante il mese**: qual è la spesa mensile corrente fino a oggi? Sono necessarie rettifiche alla spesa e/o all'utilizzo di Azure? Qual è il periodo del mese in cui l'utilizzo di Azure è maggiore?

- **Impostazione di avvisi**: se si vogliono impostare avvisi relativi al consumo basato sulle risorse o basati sull'importo monetario.

## <a name="scenario-to-api-mappings"></a>Mapping scenario-API

|         API/Scenario        | Riconciliazione di fatture    | Cross-charge    | Ottimizzazione dei costi    | Verifica dei costi    | Spesa a metà mese    | Avvisi    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budget                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Elenco prezzi                 |             X             |         X        |           X          |         X        |          X         |           |
| Consigli sulle prenotazioni |                           |                  |           X          |                  |                    |           |
| Dettagli prenotazioni         |                           |                  |           X          |         X        |                    |           |
| Riepiloghi delle prenotazioni       |                           |                  |           X          |         X        |                    |           |
| Dettagli di utilizzo               |             X             |         X        |           X          |         X        |          X         |     X     |
| Periodi di fatturazione             |             X             |         X        |           X          |         X        |                    |           |
| Fatture                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Utilizzo senza classificazione               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> I mapping scenario-API riportati di seguito non includono le API per il consumo Enterprise. Dove possibile, utilizzare le API per il consumo generali per risolvere nuovi scenari di sviluppo man mano che si va avanti.

## <a name="api-summaries"></a>Riepiloghi API

### <a name="consumption"></a>Consumo
(*Clienti Accesso Web diretto + Enterprise per tutte le API ad eccezione di quelle richiamate di seguito*)

-   **Budget** (*SOLO clienti Enterprise*): usare l'[API Budget](https://docs.microsoft.com/rest/api/consumption/budgets) per creare budget di costo o di utilizzo per risorse, gruppi di risorse o contatori di fatturazione.  Dopo aver creato i budget, è possibile configurare alcuni avvisi per l'invio di notifiche quando le soglie definite dall'utente per i budget vengono superate. È anche possibile configurare azioni da eseguire quando gli importi dei budget vengono raggiunti.
-   **Marketplace**: usare l'[API Addebiti per Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces) per ottenere dati sull'utilizzo e sui costi per tutte le risorse Marketplace (offerte Azure di terze parti). Questi dati sono utilizzabili per sommare i costi di tutte le risorse Marketplace o analizzare i costi e/o l'utilizzo per risorse specifiche.
-   **Elenco prezzi** (*SOLO clienti Enterprise*): i clienti Enterprise possono usare l'[API Elenco prezzi](https://docs.microsoft.com/rest/api/consumption/pricesheet) per recuperare informazioni sui prezzi personalizzati cui sono soggetti per tutti i contatori. Combinando questi dati con i dettagli sull'utilizzo e le informazioni sull'utilizzo dei Marketplace, le aziende sono in grado di calcolare i costi. 
-   **Consigli sulle prenotazioni**: utilizzare l'[API per i consigli sulle prenotazioni](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) per ottenere consigli relativi all'acquisto di istanze di macchina virtuale riservate. Le raccomandazioni hanno lo scopo di consentire ai clienti l'analisi dei risparmi previsti sui costi e degli importi degli acquisti.
-   **Dettagli prenotazione**: usare l'[API Dettagli prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) per visualizzare le informazioni relative a prenotazioni di macchine virtuali acquistate in precedenza, ad esempio il consumo riservato rispetto all'utilizzo effettivo. È possibile visualizzare i dati a livello di dettaglio di macchina virtuale.
-   **Riepiloghi delle prenotazioni**: usare l'[API per i riepiloghi delle prenotazioni](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) per visualizzare informazioni aggregate relative a prenotazioni di macchine virtuali acquistate in precedenza, ad esempio il consumo riservato rispetto all'utilizzo effettivo nell'aggregazione. 
-   **Dettagli di utilizzo**: usare l'[API Dettagli di utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails) per ottenere informazioni su addebito e utilizzo per tutte le risorse di Azure. Le informazioni sono presentate sotto forma di record di dettagli di utilizzo, che attualmente vengono generati una volta al giorno per ogni contatore. Le informazioni sono utilizzabili per sommare i costi di tutte le risorse o analizzare i costi e/o l'utilizzo per risorse specifiche.
-   **RateCard**: i clienti Accesso Web diretto possono usare l'[API RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) per ottenere le tariffe dei contatori. Possono quindi usare le informazioni restituite con le informazioni sull'utilizzo delle risorse per calcolare manualmente la fattura prevista. 
-   **Utilizzo senza classificazione**: è possibile usare l'[API per i dati di utilizzo senza classificazione](https://msdn.microsoft.com/library/azure/mt219003.aspx) per ottenere informazioni sull'utilizzo non elaborate, prima di qualsiasi misurazione/addebito eseguito da Azure.

### <a name="billing"></a>Fatturazione
-   **Periodi di fatturazione**: usare l'[API per i periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/billingperiods) per determinare un periodo di fatturazione da analizzare, insieme agli ID fattura per il periodo. Gli ID fattura possono essere usati con l'API Fatture seguente. 
-   **Fatture**: usare l'[API Fatture](https://docs.microsoft.com/rest/api/billing/invoices) per ottenere l'URL di download di una fattura per un periodo di fatturazione specifico in formato PDF.

### <a name="enterprise-consumption"></a>Consumo Enterprise
*(Tutte le API SOLO Enterprise)*

-   **Riepilogo saldi**: usare l'[API di riepilogo saldi](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) per ottenere un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace e spese per modifiche ed eccedenze. È possibile ottenere queste informazioni per il periodo di fatturazione corrente o per qualsiasi periodo in passato. Le aziende possono usare questi dati per eseguire un confronto con il riepilogo degli addebiti calcolati manualmente. Questa API non fornisce informazioni specifiche per risorsa o una visualizzazione aggregata dei costi.
-   **Dettagli di utilizzo**: usare l'[API Dettagli di utilizzo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) per ottenere i dettagli sull'utilizzo di Azure per il mese corrente, per un periodo di fatturazione specifico o per un intervallo di date personalizzato. Le aziende possono usare questi dati per calcolare manualmente la fattura in base alla tariffa e al consumo. Possono anche usare le informazioni presenti nei reparti o nelle organizzazioni per attribuire i costi alle diverse organizzazioni. I dati forniscono una visualizzazione dei costi e dell'utilizzo specifica per risorsa.
-   **Spese per Marketplace Store**: usare l'[API per le spese in Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) per ottenere i dettagli sull'utilizzo di Azure di terze parti per il mese corrente, per un periodo di fatturazione specifico o per un intervallo di date personalizzato. Le aziende possono usare questi dati per calcolare manualmente la fattura in base alla tariffa e al consumo. Possono anche usare le informazioni presenti nei reparti o nelle organizzazioni per attribuire i costi alle diverse organizzazioni. L'API Spese Marketplace Store fornisce una visualizzazione dei costi e dell'utilizzo specifica per risorsa.
-   **Elenco prezzi**: l'[API Elenco prezzi](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) presenta la tariffa applicabile per ogni contatore per il periodo di registrazione e fatturazione specificato. Le informazioni sulla tariffa sono utilizzabili in combinazione con i dettagli sull'utilizzo e con le informazioni sull'utilizzo Marketplace per calcolare manualmente la fattura prevista.
-   **Periodi di fatturazione**: usare l'[API per i periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) per ottenere un elenco di periodi di fatturazione insieme a una proprietà che indica la route dell'API per i quattro set di dati API Enterprise relativi ai periodi di fatturazione: BalanceSummary, UsageDetails, MarketplaceCharges e PriceSheet.
-   **Consigli sulle prenotazioni di Azure**: l'[API per i consigli sulle prenotazioni di istanze riservate](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) prende in esame 7, 30 o 60 giorni di utilizzo della macchina virtuale da parte del cliente e offre raccomandazioni per acquisti singoli e condivisi. L'API di istanza riservata consente ai clienti l'analisi dei risparmi previsti sui costi e degli importi degli acquisti consigliati.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-is-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual è la differenza tra le API di creazione di report per clienti Enterprise e le API per il consumo? In quali casi usare ciascuna?
Queste API hanno un set di funzionalità simile e possono rispondere allo stesso vasto insieme di domande nello spazio di gestione delle fatturazione e dei costi. Tuttavia, ogni API è appropriata per destinatari diversi: 

- **API di creazione di report per clienti Enterprise**: queste API sono disponibili per i clienti che hanno sottoscritto un Contratto Enterprise con Microsoft che concede loro accesso a impegni monetari negoziati e prezzi personalizzati. Le API richiedono l'uso di una chiave, che può essere ottenuta tramite [Enterprise Portal](https://ea.azure.com). Per una descrizione di queste API, vedere [Panoramica delle API di creazione di report per i clienti Enterprise](billing-enterprise-api.md).

- **API per il consumo**: queste API sono disponibili per tutti i clienti, con alcune eccezioni. Per altre informazioni, vedere [Panoramica delle API per il consumo di Azure](billing-consumption-api-overview.md) e [Azure Consumption API reference](https://docs.microsoft.com/rest/api/consumption/) (Informazioni di riferimento sulle API per il consumo di Azure). Le API fornite sono la soluzione consigliata per gli scenari di sviluppo più recenti. 

### <a name="what-is-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual è la differenza tra l'API Dettagli di utilizzo e l'API per il consumo?
Queste API forniscono essenzialmente dati diversi:

- **Dettagli di utilizzo**: l'[API Dettagli di utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornisce informazioni su utilizzo e costi di Azure per ogni istanza di contatore. I dati forniti sono già stati trasmessi al sistema di misurazione dei costi di Azure e ai dati è già stato applicato un costo, che tiene conto anche di altre possibili modifiche:

    - Modifiche all'account per l'uso di impegni monetari prepagati
    - Modifiche all'account per discrepanze di utilizzo individuate da Azure

- **Consumo**: l'[API per il consumo](https://msdn.microsoft.com/library/Mt219003.aspx) fornisce informazioni sull'utilizzo di Azure non elaborate prima che queste vengano trasmesse al sistema di misurazione dei costi di Azure. Questi dati potrebbero non avere alcuna correlazione con l'importo per l'utilizzo e/o di addebito visualizzato dopo il sistema di misurazione degli addebiti di Azure.

### <a name="what-is-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual è la differenza tra l'API di fatturazione e l'API Dettagli di utilizzo?
Queste API forniscono una visualizzazione diversa degli stessi dati. L'[API di fatturazione](https://docs.microsoft.com/rest/api/billing/invoices) è per i clienti con accesso Web diretto e fornisce un aggiornamento mensile della fattura in base agli addebiti aggregati per ogni tipo di contatore. L'[API Dettagli di utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails) offre una visualizzazione granulare dei record su utilizzo/costo per ogni giorno e può essere usata sia dai clienti Enterprise sia da quelli con accesso Web diretto.

### <a name="what-is-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Qual è la differenza tra l'API Elenco prezzi e l'API RateCard?
Queste API forniscono set simili di dati, ma hanno destinatari diversi. Le informazioni vengono fornite di seguito.

- API Elenco prezzi: l'[API Elenco prezzi](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornisce i prezzi personalizzati che sono stati negoziati per un cliente Enterprise.
- API RateCard: l'[API RateCard](https://msdn.microsoft.com/library/mt219005.aspx) fornisce i prezzi pubblici applicati ai clienti con accesso Web diretto.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso delle API di Azure per ottenere informazioni dettagliate a livello di codice relativamente all'utilizzo di Azure, vedere [Panoramica delle API per il consumo di Azure](billing-consumption-api-overview.md) e [Panoramica delle API di fatturazione di Azure](billing-usage-rate-card-overview.md).
- Per confrontare la fattura con il file dettagliato sull'utilizzo giornaliero e i report di gestione dei costi nel portale di Azure, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md)
- In caso di domande, creare una [Richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per assistenza.
