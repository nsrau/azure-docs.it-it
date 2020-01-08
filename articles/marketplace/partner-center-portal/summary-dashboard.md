---
title: Dashboard di riepilogo per partner Center Analytics nel Marketplace commerciale
description: Informazioni su come accedere a grafici, tendenze e valori di dati aggregati che riepilogano l'attività del Marketplace dal dashboard di riepilogo nel centro per i partner.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e20f3d156df139cfaf1935bae7fc7babd3e35fc5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480359"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard di riepilogo in analisi del Marketplace commerciale

Questo articolo fornisce informazioni sul dashboard di riepilogo nel centro per i partner. Questo dashboard Visualizza i grafici, le tendenze e i valori dei dati aggregati che riepilogano le attività del Marketplace per le offerte.

Per accedere al dashboard di riepilogo, aprire il **[Dashboard analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Marketplace commerciale.

>[!NOTE]
> Per le definizioni dettagliate della terminologia di analisi, vedere [le domande frequenti e la terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).

## <a name="summary-dashboard"></a>Dashboard Riepilogo

Il dashboard di **Riepilogo** presenta una panoramica basata su ogni tipo di offerta. **Insights** Mostra informazioni critiche a colpo d'occhio e fornisce un'ampia panoramica dell'attività di vendita delle offerte. È possibile visualizzare questi report usando il dashboard di **Riepilogo** . Questo articolo illustra in dettaglio tutti gli elementi seguenti:

- [Intervallo di date](#date-range)
- [Sezione Riepilogo](#summary-section)
- [Clienti per geografia](#customers-by-geography)
- [Grafici delle tendenze di crescita](#growth-trend-charts)
- [Tabellone clienti](#customer-leaderboard)
- [Tendenza conteggio sedili](#seat-count-trend)
- [Tendenze degli ordini SaaS gratuiti per le versioni di valutazione](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementi del dashboard di riepilogo

Le sezioni seguenti descrivono come usare il dashboard di riepilogo e come leggere i dati.

### <a name="date-range"></a>Intervallo di date

È possibile trovare la selezione di un intervallo di date nell'angolo superiore destro di ogni pagina. Personalizzare l'output dei grafici della pagina di **Riepilogo** selezionando un intervallo di date basato sugli ultimi 3, 6 o 12 mesi oppure selezionando un intervallo di date personalizzato con una durata massima di 12 mesi. L'intervallo di date predefinito è di sei mesi.

![Dashboard di analisi del centro per i partner](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Sezione Riepilogo

Nella sezione Riepilogo viene visualizzato il conteggio di tutti gli ordini creati, i clienti acquisiti e l'utilizzo indicati durante l'intervallo di date selezionato. Il mese corrente parziale verrà escluso dal calcolo di queste metriche. Ad esempio, se è stato selezionato il frame di tempo 6M, le ore di utilizzo vengono calcolate per i sei mesi precedenti al mese corrente. Se viene selezionato un intervallo di date personalizzato, un importo parziale del mese corrente verrà escluso dal calcolo.

![Tendenze di crescita nel dashboard di riepilogo](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Lettura della sezione di riepilogo

- **Ordini**: numero di tutti gli ordini acquistati (esclusi gli ordini annullati) per le offerte pubblicate fino a questo momento.
- **Clienti**: conteggio di tutti i clienti che hanno acquistato le offerte e hanno almeno un ordine non annullato.
- **Ore di utilizzo normalizzate**: definite come ore di utilizzo normalizzate per tenere conto del numero di core VM ([numero di core VM] x [ore di utilizzo non elaborato]). Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore [Number of VM Cores].
- **Ore di utilizzo non elaborato**: quantità di macchine virtuali in esecuzione in termini di ore. Il valore percentuale accanto a **ordini totali**, **totale clienti**, **ore di utilizzo normalizzato**, **ore di utilizzo non elaborato**, visite di **pagina**e **chiamate a azioni** rappresentano la quantità di crescita dell'utilizzo per l'intervallo di date selezionato ([utilizzo del mese precedente, utilizzo del primo mese])/utilizzo del primo mese. Come descritto in precedenza, una quantità parziale del mese corrente verrà esclusa da questa metrica.
- **Tendenze di crescita**: se si passa il mouse sulle colonne del grafico, i grafici a barre visualizzano il valore per ogni mese.
- **Triangolo verde che punta verso l'alto**: indica una tendenza di crescita positiva.
- **Triangolo rosso che punta verso il basso**: indica una tendenza di crescita negativa rispetto al mese precedente.

### <a name="customers-by-geography"></a>Clienti per geografia

I **clienti di geography** mappa termica visualizzano un numero di clienti in una mappa mondiale.

![Clienti per geografia nel dashboard di riepilogo](./media/summary-customers-by-geography.png)

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile ingrandire una posizione specifica.
- Mappa termica dispone di una griglia supplementare per visualizzare i dettagli relativi al numero di clienti, al numero di ordini e alle ore di utilizzo normalizzato nella posizione specifica.
- È possibile cercare e selezionare un paese nella griglia per ingrandire la posizione della mappa. Ripristinare la visualizzazione originale premendo il pulsante **Home** nella mappa.
- Un **nuovo** cliente ha acquistato una delle offerte per la prima volta durante il mese entro l'intervallo di date selezionato.

### <a name="growth-trend-charts"></a>Grafici delle tendenze di crescita

È possibile visualizzare le tendenze in base alla crescita degli **ordini acquistati** (include gli ordini annullati), **i clienti acquisiti** (inclusi i clienti smarriti) e l' **utilizzo** riportato, che vengono visualizzati mese per mese in base all'intervallo di date selezionato. È possibile analizzare ulteriormente queste tendenze selezionando i collegamenti al di sotto del grafico, che passano alle pagine relative a **ordine**, **utilizzo**, **cliente**o **Marketplace Insights** .

Per Azure Marketplace e AppSource in due schede vengono visualizzate le pagine relative all'offerta del Marketplace **e le chiamate ai grafici di** tendenza delle azioni.

![Pagine visite e chiamate a azioni grafici di tendenza nel dashboard di riepilogo](./media/summary-page-visits-and-cta.png)

Il grafico **Orders by offers** consente di organizzare gli ordini in base al nome dell'offerta.

Il grafico a torta **Orders by Sales Channel** organizza gli ordini (inclusi gli ordini annullati dai clienti) nell'intervallo di date selezionato, dal canale di vendita. Canale di vendita è il tipo di contratto di licenza usato dai clienti per acquistare Azure, ovvero Cloud Solution Provider (CSP), Enterprise, Enterprise tramite Reseller, GTM e con pagamento in base al consumo.

Le **offerte** e l'utilizzo da parte dei grafici a torta del **canale di vendita** presentano rispettivamente la suddivisione dell'utilizzo in base alle offerte e ai canali di vendita. Il grafico a torta interno rappresenta l'utilizzo non elaborato e il grafico a torta esterno rappresenta l'utilizzo normalizzato.

I grafici a torta dei tipi di licenza **Orders by Marketplace** e **Usage by Marketplace** visualizzano una suddivisione degli ordini e l'utilizzo in base al rispettivo tipo di licenza. I tipi di licenza includono:

- **Fatturazione tramite Azure**: Microsoft fattura i clienti per conto dell'utente quando si sceglie di vendere l'offerta tramite Microsoft con questo tipo di licenza. I tipi di pagamento includono il pagamento in base al consumo tramite carta di credito o fatturazione aziendale.
- **Bring your own License**: Microsoft non fattura i clienti per l'utilizzo con questo tipo di offerta Marketplace. Questo utilizzo è elencato come **Get it Now (gratuito)** nel Marketplace.
- **Gratuito**: Microsoft non fattura i clienti per l'utilizzo con questo tipo di offerta Marketplace. Questo utilizzo è elencato come **versione di valutazione gratuita** nel Marketplace.
- **Microsoft As Reseller**: rappresenta le offerte vendute da Microsoft Resellers come parte del **programma Cloud Solution Provider (CSP)** .

### <a name="customer-leaderboard"></a>Tabellone clienti

I primi 50 clienti con il maggior numero di ordini vengono visualizzati in una *bacheca*, ordinati in base al numero massimo di ordini e alla percentuale di ordine.

- Consente di selezionare un cliente per visualizzare i dettagli del profilo, gli ordini organizzati per offerta o gli ordini organizzati in base al tipo di licenza e al canale dei prezzi di Azure.
- Il grafico ad anello **Offerte per ordini** presenta le prime quattro offerte (per numero di ordini) e le offerte rimanenti raggruppate come ' Rest all'.
- Il grafico ad anello **utilizzo normalizzato per offerta** presenta le prime cinque offerte in base all'utilizzo.

> [!NOTE]
> Le informazioni personali del cliente verranno presentate solo se il cliente ha fornito il consenso. È possibile visualizzare queste informazioni se è stato effettuato l'accesso con un ruolo **proprietario** a livello di autorizzazioni. Gli utenti con il ruolo **collaboratore** non saranno in grado di visualizzare queste informazioni. [Altre informazioni sui ruoli utente e le autorizzazioni](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Tendenza conteggio sedili

Il grafico **Orders by per postazione/per sito** presenta la suddivisione di tutti gli ordini acquistati in base al modello di prezzi. Il grafico di **tendenza del numero** di postazioni presenta le postazioni rispetto agli ordini acquistati per tutte le offerte SaaS (software as a Service) per postazione.

### <a name="free-trials-saas-orders-trend"></a>Tendenze degli ordini SaaS gratuiti per le versioni di valutazione

Il grafico di **tendenza degli ordini SaaS della versione di valutazione gratuita** presenta la tendenza degli ordini per le offerte SaaS gratuite di valutazione con un periodo di valutazione di 30 giorni.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale del centro per i partner, vedere [analisi per il Marketplace commerciale nel centro per i partner](./analytics.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard Orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Per la macchina virtuale (VM) offre metriche di fatturazione di utilizzo e a consumo, vedere [dashboard di utilizzo in analisi del Marketplace commerciale](./usage-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [dashboard dei clienti in analisi del Marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere la pagina relativa ai [download del dashboard in Commercial Marketplace Analytics](./downloads-dashboard.md).
- Per una visualizzazione consolidata dei suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard ratings and Reviews in Commercial Marketplace Analytics](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [le domande frequenti e la terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).
