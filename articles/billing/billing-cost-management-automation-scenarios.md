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
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: f84071577e9636e40d621093e2c57e3e9adf4913
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247667"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Scenari di automazione della fatturazione e della gestione dei costi

Gli scenari più comuni di fatturazione e gestione dei costi sono identificati di seguito, con il mapping ad API diverse utilizzabili in tali scenari. È disponibile un riepilogo di tutte le API disponibili. È possibile trovare le funzionalità offerte da queste nel mapping API-scenario. 

## <a name="common-scenarios"></a>Scenari comuni 

È possibile usare le API di fatturazione e gestione dei costi in un'ampia varietà di scenari, per rispondere a domande correlate al costo e all'utilizzo.  Di seguito viene presentata una struttura di scenari comuni.

- **Riconciliazione di fatture**: Microsoft ha addebitato l'importo corretto?  Qual è la fattura? È possibile calcolarla personalmente?

- **Cross-charge**: dopo la comunicazione dell'importo da pagare, chi all'interno dell'organizzazione deve pagare?

- **Ottimizzazione dei costi**: conoscendo l'importo da pagare, come è possibile sfruttare al massimo l'importo speso per Azure?

- **Verifica dei costi**: si vuole verificare quanto si spende per Azure e quanto si usa questo servizio nel tempo. Quali sono le tendenze? Come si può ottenere una maggiore efficienza?

- **Spesa di Azure durante il mese**: qual è la spesa mensile corrente fino a oggi? Sono necessarie rettifiche alla spesa e/o all'utilizzo di Azure? Qual è il periodo del mese in cui l'utilizzo di Azure è maggiore?

- **Impostazione di avvisi**: se si vogliono impostare avvisi relativi al consumo basato sulle risorse o basati sull'importo monetario.

## <a name="scenario-to-api-mappings"></a>Mapping scenario-API

|         API/Scenario        | Riconciliazione di fatture    | Cross-charge    | Ottimizzazione dei costi    | Verifica dei costi    | Spesa a metà mese    | Avvisi    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budget                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
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
> I mapping scenario-API riportati di seguito non includono le API per il consumo Enterprise. Dove possibile, utilizzare le API per il consumo generali per risolvere nuovi scenari di sviluppo man mano che si va avanti.

## <a name="api-summaries"></a>Riepiloghi API

### <a name="consumption"></a>Consumo
(*Clienti Accesso Web diretto + Enterprise per tutte le API ad eccezione di quelle richiamate di seguito*)

-   **Budget** (*SOLO clienti Enterprise*): usare l'[API Budget](https://docs.microsoft.com/rest/api/consumption/budgets) per creare budget di costo o di utilizzo per risorse, gruppi di risorse o contatori di fatturazione.  Dopo aver creato i budget, è possibile configurare alcuni avvisi per l'invio di notifiche quando le soglie definite dall'utente per i budget vengono superate. È anche possibile configurare azioni da eseguire quando gli importi dei budget vengono raggiunti.
-   **Marketplace**: usare l'[API Addebiti per Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces) per ottenere dati sull'utilizzo e sui costi per tutte le risorse Marketplace (offerte Azure di terze parti). Questi dati sono utilizzabili per sommare i costi di tutte le risorse Marketplace o analizzare i costi e/o l'utilizzo per risorse specifiche.
-   **Elenco prezzi** (*SOLO clienti Enterprise*): i clienti Enterprise possono usare l'[API Elenco prezzi](https://docs.microsoft.com/rest/api/consumption/pricesheet) per recuperare i prezzi loro applicati per tutti i contatori. Combinando questi dati con i dettagli sull'utilizzo e le informazioni sull'utilizzo dei Marketplace, le aziende sono in grado di calcolare i costi. 
-   **Raccomandazioni di prenotazione**: utilizzare l'[API Raccomandazioni di prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) per ottenere raccomandazioni per l'acquisto di istanze riservate di macchine virtuali. Le raccomandazioni hanno lo scopo di consentire ai clienti l'analisi dei risparmi previsti sui costi e degli importi degli acquisti.
-   **Dettagli di prenotazione**: usare l'[API Dettagli di prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) per visualizzare le informazioni relative a prenotazioni di macchine virtuali acquistate in precedenza, ad esempio il consumo prenotato rispetto all'utilizzo effettivo. È possibile visualizzare i dati a livello di dettaglio di macchina virtuale.
-   **Riepiloghi di prenotazioni**: usare l'[API Riepiloghi di prenotazioni](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) per visualizzare informazioni aggregate relative a prenotazioni di macchine virtuali acquistate in precedenza, ad esempio il consumo prenotato rispetto all'utilizzo effettivo nell'aggregazione. 
-   **Dettagli sull'utilizzo**: usare l'[API Dettagli sull'utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails) per ottenere le informazioni su addebito e utilizzo su tutte le risorse Azure di prime parti. Le informazioni sono presentate sotto forma di record di dettagli di utilizzo, che attualmente vengono generati una volta al giorno per ogni contatore. Le informazioni sono utilizzabili per sommare i costi di tutte le risorse o analizzare i costi e/o l'utilizzo per risorse specifiche.
-   **RateCard**: i clienti Accesso Web diretto possono usare l'[API RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) per ottenere le tariffe dei contatori. Possono quindi usare le informazioni restituite con le informazioni sull'utilizzo delle risorse per calcolare manualmente la fattura prevista. 
-   **Utilizzo non valutato**: È possibile usare l'[API Utilizzo non valutato](https://msdn.microsoft.com/library/azure/mt219003.aspx) per ottenere informazioni non elaborate sull'utilizzo, prima di qualsiasi misurazione o addebito eseguito da Azure.

### <a name="billing"></a>Fatturazione
-   **Periodi di fatturazione**: usare l'[API Periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/billingperiods) per determinare un periodo di fatturazione per l'analisi, insieme agli ID fattura per il periodo. Gli ID fattura possono essere usati con l'API Fatture seguente. 
-   **Fatture**: usare l'[API Fatture](https://docs.microsoft.com/rest/api/billing/invoices) per ottenere l'URL di download di una fattura per un periodo di fatturazione specifico in formato PDF.

### <a name="enterprise-consumption"></a>Consumo Enterprise
*(Tutte le API SOLO Enterprise)*

-   **Riepilogo saldi**: usare l'[API Riepilogo saldi](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) per ottenere un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace e spese per modifiche e da pagare in eccedenza. È possibile ottenere queste informazioni per il periodo di fatturazione corrente o per qualsiasi periodo in passato. Le aziende possono usare questi dati per eseguire un confronto con il riepilogo degli addebiti calcolati manualmente. Questa API non fornisce informazioni specifiche per risorsa o una visualizzazione aggregata dei costi.
-   **Dettagli sull'utilizzo**: usare l'[API Dettagli sull'utilizzo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) per ottenere i dettagli sull'utilizzo di Azure di prime parti per il mese corrente, per un periodo di fatturazione specifico o per un intervallo di date personalizzato. Le aziende possono usare questi dati per calcolare manualmente la fattura in base alla tariffa e al consumo. Possono anche usare le informazioni presenti nei reparti o nelle organizzazioni per attribuire i costi alle diverse organizzazioni. I dati forniscono una visualizzazione dei costi e dell'utilizzo specifica per risorsa.
-   **Spese Marketplace Store**: usare l'[API Spese Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) per ottenere i dettagli sull'utilizzo di Azure di terze parti per il mese corrente, per un periodo di fatturazione specifico o per un intervallo di date personalizzato. Le aziende possono usare questi dati per calcolare manualmente la fattura in base alla tariffa e al consumo. Possono anche usare le informazioni presenti nei reparti o nelle organizzazioni per attribuire i costi alle diverse organizzazioni. L'API Spese Marketplace Store fornisce una visualizzazione dei costi e dell'utilizzo specifica per risorsa.
-   **Elenco prezzi**: l'[API Elenco prezzi](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) presenta la tariffa applicabile per ogni contatore per la registrazione e il periodo di fatturazione specificati. Le informazioni sulla tariffa sono utilizzabili in combinazione con i dettagli sull'utilizzo e con le informazioni sull'utilizzo Marketplace per calcolare manualmente la fattura prevista.
-   **Periodi di fatturazione**: usare l'[API Periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) per ottenere un elenco di periodi di fatturazione insieme a una proprietà che indicala route dell'API per i quattro set di dati API Enterprise relativi a tali periodi di fatturazione: BalanceSummary, UsageDetails, MarketplaceCharges e PriceSheet.
-   **Raccomandazioni istanza riservata**: l'[API Raccomandazioni istanza riservata](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) prende in esame 7, 30 o 60 giorni di utilizzo della macchina virtuale da parte del cliente e offre raccomandazioni per acquisti singoli e condivisi. L'API di istanza riservata consente ai clienti l'analisi dei risparmi previsti sui costi e degli importi degli acquisti consigliati.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso delle API di fatturazione di Azure ottenere informazioni dettagliate a livello di codice sull'utilizzo di Azure, vedere [Panoramica dell'API di fatturazione di Azure](billing-usage-rate-card-overview.md).
