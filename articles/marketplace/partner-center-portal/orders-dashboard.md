---
title: Dashboard degli ordini del centro per i partner in analisi del Marketplace commerciale
description: Informazioni su come accedere ai report analitici sugli ordini di offerte del Marketplace in un formato grafico e scaricabile.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 148b80a6843168b11e4feb3558f6bf08eaeb3ace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261262"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Dashboard degli ordini in analisi del Marketplace commerciale

Questo articolo fornisce informazioni sul **Dashboard Orders** nel centro per i partner. Questo dashboard Visualizza le informazioni sugli ordini in un formato grafico e scaricabile.

Per accedere al **dashboard degli ordini** negli strumenti di analisi del centro per i partner, aprire il **[Dashboard analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Marketplace commerciale.

>[!NOTE]
> Per le definizioni dettagliate della terminologia di analisi, vedere [le domande frequenti e la terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).

## <a name="orders-dashboard"></a>Dashboard Ordini

Il **Dashboard Orders** del menu **Analyze** Visualizza gli ordini correnti per tutte le offerte SaaS. È possibile visualizzare le rappresentazioni grafiche degli elementi seguenti:

- [Riepilogo degli ordini](#order-summary)
- [Ordini per geografia](#orders-by-geography)
- [Ordini per offerte](#orders-by-offers)
- [Tendenza degli ordini per sito rispetto alla postazione](#orders-trend-per-site-versus-per-seat)
- [Ordini per SKU](#orders-by-skus)
- [Tendenza degli ordini e delle sedi](#orders-and-seats-trend)
- [Tabella Dettagli ordine](#order-details-table)

> [!NOTE]
> Esistono differenze tra il modo in cui i report di analisi vengono visualizzati nel portale Cloud Partner (CPP) e il nuovo programma commerciale Marketplace nel centro per i partner. Un modo specifico è che il **venditore Insights** in CPP dispone di una scheda **Orders & Usage** , che Visualizza i dati per le offerte basate sull'utilizzo e le offerte non basate sull'utilizzo. Nel centro per i partner la pagina **Orders** include una scheda separata per le offerte SaaS.

## <a name="order-dashboard-details"></a>Ordinare i dettagli del dashboard

In questa sezione vengono descritti in modo più dettagliato i report di analisi.

### <a name="order-summary"></a>Riepilogo degli ordini

La sezione Order Summary Visualizza il conteggio di tutti gli ordini acquistati (esclusi gli ordini annullati), gli ordini annullati e le postazioni.

Il valore percentuale accanto a ordini totali rappresenta la quantità di incremento dell'intervallo di date selezionato.

![Riepilogo dell'ordine di analisi del centro per i partner](./media/order-summary.png)

- Un triangolo verde che punta verso l'alto indica una tendenza di crescita positiva.
- Un triangolo rosso che punta verso il basso indica una tendenza di crescita negativa rispetto al mese precedente.
- Le tendenze di crescita sono rappresentate da grafici a micro barre. È possibile visualizzare il valore per ogni mese passando il mouse sulle colonne all'interno del grafico.
- Gli ordini annullati sono un conteggio degli ordini acquistati in precedenza e quindi annullati durante l'intervallo di date selezionato.
- Le postazioni corrispondono al numero di postazioni create durante l'intervallo di date selezionato.

### <a name="orders-by-geography"></a>Ordini per geografia

Il mappa termica **Orders by geography** Visualizza un conteggio degli ordini in una mappa mondiale e Mostra le postazioni mappate in base al paese del cliente. Questa mappa termica funziona allo stesso modo del **[cliente da geography mappa termica](./customer-dashboard.md#customer-by-geography)**.

![Il centro per i partner analizza gli ordini per geografia](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Ordini per offerte

Il grafico **ad anello Orders by consente di** organizzare gli ordini (compresi gli ordini annullati) in base ai nomi delle offerte.

- Le offerte principali vengono visualizzate nel grafico e il resto delle offerte viene raggruppato come "Rest all".
- È possibile selezionare offerte specifiche nella legenda per visualizzare solo le offerte nel grafico.
- Se si passa il puntatore del mouse su una sezione del grafico, viene visualizzato il numero di ordini e la percentuale di tale offerta rispetto al numero totale di ordini in tutte le offerte.
- La **tendenza Orders by offers** Visualizza tendenze di crescita mensili per mese. La colonna Month rappresenta il numero di ordini in base al nome dell'offerta. Il grafico a linee Visualizza la tendenza percentuale di crescita tracciata su un asse z.
- È possibile utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra e a sinistra lungo l'asse x e concentrarsi su punti dati specifici.
- È possibile visualizzare il grafico di tendenza selezionando un elemento specifico nella legenda.
- È anche possibile scegliere di visualizzare le tendenze e i dati per **gli ordini annullati**. Il grafico funzionerà nello stesso modo del grafico **Orders by offers** .

### <a name="orders-trend-per-site-versus-per-seat"></a>Tendenza degli ordini per sito rispetto alla postazione

Il grafico ad anello **per sito rispetto a ogni postazione** rappresenta la suddivisione degli ordini SaaS per sito SaaS e per postazione acquistati dai clienti (questo grafico include gli ordini annullati). L'istogramma rappresenta la tendenza degli ordini SaaS per sito SaaS e per postazione acquistati dai clienti (questo grafico include gli ordini annullati).

### <a name="orders-by-skus"></a>Ordini per SKU

Il grafico **Orders by SKU** rappresenta la tendenza degli ordini al livello dell'unità di mantenimento dei titoli (SKU) per tutte le offerte (incluse le ordinazioni annullate). Il grafico ad anello rappresenta la suddivisione dei primi cinque ordini dello SKU e l'istogramma rappresenta la tendenza degli ordini per i primi cinque SKU.

### <a name="orders-and-seats-trend"></a>Tendenza degli ordini e delle sedi

Il grafico di **tendenza ordini e sedili** presenta le prime 50 offerte con il maggior numero di ordini. Questi vengono visualizzati in una bacheca e sono ordinati in base al numero massimo di ordini e alla percentuale di ordine.

- **Orders by SKUS**: selezionare un'offerta per visualizzare la suddivisione del conteggio degli ordini per i primi cinque SKU nel grafico.
- **Postazioni per SKU**: la tendenza mensile delle postazioni per i primi cinque SKU. Se l'offerta selezionata non è un'offerta per postazione, i dati non vengono visualizzati in questo grafico ad area.

### <a name="canceled-orders-by-offers"></a>Ordini annullati per offerte

Il grafico **a torta ordini annullati per offerte** consente di organizzare tutti gli ordini annullati in base ai nomi delle offerte. Le offerte principali vengono visualizzate nel grafico e il resto delle offerte viene raggruppato come "Rest all". È possibile selezionare offerte specifiche nella legenda da visualizzare nel grafico.

- Se si passa il puntatore del mouse su una sezione del grafico, viene visualizzato il numero di ordini e la percentuale dell'offerta selezionata rispetto al numero totale di ordini in tutte le offerte.
- Nell'istogramma vengono visualizzate le tendenze di mese per mese. Le colonne rappresentano il numero di ordini annullati in base al nome dell'offerta. È possibile utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra e a sinistra lungo l'asse x e concentrarsi su punti dati specifici. È possibile visualizzare il grafico di tendenza selezionando un elemento specifico nella legenda.

### <a name="order-details-table"></a>Tabella Dettagli ordine

Nella tabella Order Details viene visualizzato un elenco numerato dei 1000 ordini principali ordinati in base alla data di acquisizione.

- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file TSV se il numero di record è inferiore a 1000.
- Se il numero di record è superiore a 1000, i dati esportati verranno posizionati in modo asincrono in una pagina di download per i successivi 30 giorni.
- I filtri possono essere applicati alla **tabella Order Details** per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati in base a paese, tipo di licenza di Azure, tipo di licenza Marketplace, tipo di offerta, stato dell'ordine, tracce gratuite, ID sottoscrizione del Marketplace, ID cliente e nome della società.

#### <a name="orders-page-filters"></a>Filtri pagina ordini

Questi filtri vengono applicati a livello di pagina.

È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri scelti per la visualizzazione e i dati da visualizzare nella griglia/esportazione **dettagliata degli ordini** . I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato nell'angolo superiore destro della pagina Orders.

- I tipi di offerta e i nomi delle offerte sono elencati solo per le offerte per cui si hanno gli ordini durante l'intervallo di date selezionato. I nomi delle offerte nell'elenco vengono visualizzati per i tipi di offerte selezionati nell'elenco.
- I filtri applicati mostrano le metriche totali all'interno di ogni selezione per ogni filtro selezionato. I filtri applicati non vengono visualizzati quando si sceglie la selezione predefinita.
- Se è selezionata l'opzione **tutti** per uno degli elenchi a discesa, tutte le metriche nella pagina selezionata verranno aggregate. Ad esempio: "tutti" nell'opzione filtro tipi di offerta significa che tutti i tipi di offerta sono stati selezionati. Si tratta della selezione predefinita per gli elenchi a discesa. I filtri applicati non visualizzano nulla quando è selezionato **tutto** .
- **Selezione di più valori**: tutte le metriche nella pagina verranno aggregate per tutte le selezioni effettuate nell'elenco a discesa. Se vengono effettuate più selezioni, il filtro applicato visualizzerà il conteggio di tutte le selezioni effettuate. Per informazioni di riferimento, vedere l'immagine seguente.

    ![Ordine di analisi del centro per i partner con più valori applicati al filtro](./media/filters-applied.png)

- **Selezione valore singolo**: se è selezionato un valore, il filtro applicato visualizzerà il numero di un filtro selezionato. Vedere l'immagine di seguito per riferimento.

     ![Ordine di analisi del centro per i partner con valore singolo applicato al filtro](./media/filters-applied-single.png)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale del centro per i partner, vedere [analisi per il Marketplace commerciale nel centro per i partner](./analytics.md).
- Per i grafici, le tendenze e i valori dei dati aggregati che riepilogano l'attività del Marketplace per l'offerta, vedere [dashboard di riepilogo in analisi del Marketplace commerciale](./summary-dashboard.md).
- Per la macchina virtuale (VM) offre metriche di fatturazione di utilizzo e a consumo, vedere [dashboard di utilizzo in analisi del Marketplace commerciale](./usage-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [dashboard dei clienti in analisi del Marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere la pagina relativa ai [download del dashboard in Commercial Marketplace Analytics](./downloads-dashboard.md).
- Per una visualizzazione consolidata dei suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard ratings and Reviews in Commercial Marketplace Analytics](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [le domande frequenti e la terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).
