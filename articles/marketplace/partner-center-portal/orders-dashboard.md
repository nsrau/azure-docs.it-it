---
title: Dashboard degli ordini del Centro per i partner nell'analisi per il marketplace commerciale
description: Informazioni su come accedere ai report analitici relativi agli ordini di offerte del marketplace in un formato grafico e scaricabile.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 3b4159d81ce1d202dd1a302e863e5392f51a9868
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747802"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Dashboard degli ordini nell'analisi per il marketplace commerciale

Questo articolo fornisce informazioni sul **Dashboard ordini** nel Centro per i partner. Il dashboard visualizza le informazioni sugli ordini in un formato grafico e scaricabile.

Per accedere a **Dashboard ordini** negli strumenti di analisi del Centro per i partner, aprire il **[dashboard di analisi](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** nel marketplace commerciale.

>[!NOTE]
> Per definizioni dettagliate della terminologia di analisi, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).

## <a name="orders-dashboard"></a>Dashboard Ordini

**Dashboard ordini** nel menu **Analizza** visualizza gli ordini correnti per tutte le offerte SaaS. È possibile visualizzare rappresentazioni grafiche degli elementi seguenti:

- [Riepilogo ordine](#order-summary)
- [Ordini per area geografica](#orders-by-geography)
- [Ordini per offerte](#orders-by-offers)
- [Tendenza ordini per sito e per postazione](#orders-trend-per-site-versus-per-seat)
- [Ordini per SKU](#orders-by-skus)
- [Tendenza ordini e postazioni](#orders-and-seats-trend)
- [Tabella Dettagli ordine](#order-details-table)

> [!NOTE]
> Esistono differenze tra il modo in cui i report di analisi vengono visualizzati nel portale Cloud Partner e il nuovo programma del marketplace commerciale nel Centro per i partner. Una, in particolare, consiste nel fatto che in **Informazioni dettagliate per il venditore** nel portale Cloud Partner è disponibile una scheda **Orders & Usage** (Ordini e utilizzo), che visualizza i dati relativi alle offerte basate sull'utilizzo e alle offerte non basate sull'utilizzo. Nel Centro per i partner la pagina **Ordini** dispone di una scheda separata per le offerte SaaS.

## <a name="order-dashboard-details"></a>Dettagli dashboard ordine

Questa sezione descrive in modo più dettagliato i report di analisi.

### <a name="order-summary"></a>Riepilogo ordine

La sezione Riepilogo ordine visualizza il numero di tutti gli ordini acquistati (esclusi gli ordini annullati), gli ordini annullati e le postazioni.

Il valore percentuale accanto a Totale ordini rappresenta la quantità di incremento dell'intervallo di date selezionato.

![Centro per i partner - Analizza - Riepilogo ordine](./media/order-summary.png)

- Un triangolo verde rivolto verso l'alto indica una tendenza di crescita positiva.
- Un triangolo rosso rivolto verso il basso indica una tendenza di crescita negativa rispetto al mese precedente.
- Le tendenze di crescita sono rappresentate da grafici a micro barre. È possibile visualizzare il valore per ogni mese passando il puntatore sulle colonne all'interno del grafico.
- Gli ordini annullati sono un numero di ordini acquistati in precedenza e quindi annullati durante l'intervallo di date selezionato.
- Le postazioni corrispondono al numero di postazioni create durante l'intervallo di date selezionato.

### <a name="orders-by-geography"></a>Ordini per area geografica

La mappa termica **Ordini per area geografica** visualizza il numero degli ordini in una mappa mondiale e mostra le postazioni mappate in base al paese o all'area del cliente. Questa mappa termica funziona allo stesso modo della mappa termica **[Clienti per area geografica](./customer-dashboard.md#customer-by-geography)** .

![Centro per i partner - Analizza - Ordini per area geografica](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Ordini per offerte

Il grafico ad anello **Ordini per offerte** organizza gli ordini (compresi gli ordini annullati) in base ai nomi delle offerte.

- Le offerte principali vengono visualizzate nel grafico, mentre le altre vengono raggruppate come "Sospendi tutto".
- È possibile selezionare offerte specifiche nella legenda per visualizzare solo queste nel grafico.
- Se si passa il puntatore del mouse su una sezione del grafico, viene visualizzato il numero di ordini e la percentuale di tale offerta rispetto al numero totale di ordini in tutte le offerte.
- La **tendenza degli ordini in base alle offerte** visualizza le tendenze di crescita mensili. La colonna del mese rappresenta il numero di ordini in base al nome dell'offerta. Il grafico a linee visualizza la tendenza percentuale di crescita tracciata sull'asse Z.
- È possibile usare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra e a sinistra sull'asse X e concentrarsi su punti dati specifici.
- È possibile visualizzare il grafico di tendenza selezionando un elemento specifico nella legenda.
- È anche possibile scegliere di visualizzare le tendenze e i dati relativi agli **ordini annullati**. Il grafico funzionerà nello stesso modo del grafico **Ordini per offerte**.

### <a name="orders-trend-per-site-versus-per-seat"></a>Tendenza ordini per sito e per postazione

Il grafico ad anello**per sito e per postazione** rappresenta il dettaglio degli ordini SaaS per sito e SaaS per postazione acquistati dai clienti (questo grafico include gli ordini annullati). L'istogramma rappresenta la tendenza degli ordini SaaS per sito e SaaS per postazione acquistati dai clienti (questo grafico include gli ordini annullati).

### <a name="orders-by-skus"></a>Ordini per SKU

Il grafico **Ordini per SKU** rappresenta la tendenza degli ordini a livello di codice di riferimento del prodotto (SKU, Stock Keeping Unit) per tutte le offerte (inclusi gli ordini annullati). Il grafico ad anello rappresenta il dettaglio dei primi cinque ordini dello SKU e l'istogramma rappresenta la tendenza degli ordini per i primi cinque SKU.

### <a name="orders-and-seats-trend"></a>Tendenza ordini e postazioni

Il grafico **Tendenza ordini e postazioni** presenta le prime 50 offerte con il maggior numero di ordini. Questi vengono visualizzati in una classifica e sono ordinati in base al numero massimo di ordini e alla percentuale di ordine.

- **Ordini per SKU**: selezionare un'offerta per visualizzare il dettaglio del numero di ordini per i primi cinque SKU nel grafico.
- **Postazioni per SKU**: la tendenza mensile delle postazioni per i primi cinque SKU. Se l'offerta selezionata non è un'offerta per postazione, in quest'area del grafico non vengono visualizzati dati.

### <a name="canceled-orders-by-offers"></a>Ordini annullati per offerte

Il grafico a torta **Ordini annullati per offerte** organizza tutti gli ordini annullati in base ai nomi delle offerte. Le offerte principali vengono visualizzate nel grafico, mentre le altre vengono raggruppate come "Sospendi tutto". È possibile selezionare offerte specifiche nella legenda per visualizzarle nel grafico.

- Se si passa il puntatore del mouse su una sezione del grafico, viene visualizzato il numero di ordini e la percentuale dell'offerta selezionata rispetto al numero totale di ordini in tutte le offerte.
- L'istogramma visualizza le tendenze mensili. Le colonne rappresentano il numero di ordini annullati in base al nome dell'offerta. È possibile usare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra e a sinistra sull'asse X e concentrarsi su punti dati specifici. È possibile visualizzare il grafico di tendenza selezionando un elemento specifico nella legenda.

### <a name="order-details-table"></a>Tabella Dettagli ordine

La tabella Dettagli ordine visualizza un elenco numerato dei 1000 ordini principali ordinati in base alla data di acquisizione.

- Ogni colonna della griglia è ordinabile.
- Se il numero dei record è inferiore a 1000, è possibile estrarre i dati in un file con estensione tsv.
- Se il numero dei record è superiore a 1000, i dati esportati verranno inseriti in modo asincrono in una pagina di download nei 30 giorni successivi.
- Alla **tabella Dettagli ordine** è possibile applicare filtri per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati per paese/area, tipo di licenza di Azure o del Marketplace, tipo di offerta, stato dell'ordine, versioni di valutazione gratuite, ID sottoscrizione del Marketplace, ID cliente e nome della società.
- Poiché le offerte SaaS acquistate tramite Azure Marketplace o AppSource non richiedono una sottoscrizione di Azure, l'ID sottoscrizione del Marketplace verrà visualizzato come 00000000-0000-0000-0000-000000000000 nella sezione **Dati ordini dettagliati**.

#### <a name="orders-page-filters"></a>Filtri della pagina Ordini

Questi filtri vengono applicati a livello di pagina.

È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si sceglie di visualizzare e i dati da visualizzare nella griglia/esportazione **Dati ordini dettagliati**. I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato nell'angolo in alto a destra della pagina degli ordini.

- I tipi di offerta e i nomi delle offerte vengono elencati solo per le offerte correlate a ordini effettuati durante l'intervallo di date selezionato. I nomi delle offerte nell'elenco vengono visualizzati per i tipi di offerta selezionati nell'elenco.
- I filtri applicati mostrano le metriche totali all'interno di ogni selezione per ogni filtro selezionato. I filtri applicati non vengono visualizzati quando si sceglie la selezione predefinita.
- Se si seleziona **Tutti** per uno degli elenchi a discesa, tutte le metriche nella pagina selezionata verranno aggregate. Ad esempio: "Tutti" nell'opzione di filtro per i tipi di offerta indica che tutti i tipi di offerta sono stati selezionati. Si tratta della selezione predefinita per gli elenchi a discesa. I filtri applicati non visualizzeranno alcun dato quando si seleziona **Tutti**.
- **Selezione di valori multipli**: tutte le metriche nella pagina verranno aggregate per tutte le selezioni effettuate nell'elenco a discesa. Se vengono effettuate più selezioni, il filtro applicato visualizzerà il numero delle selezioni effettuate. Per informazioni di riferimento, vedere l'immagine seguente.

    ![Centro per i partner - Analizza - Ordine con più valori applicati al filtro](./media/filters-applied.png)

- **Selezione di un singolo valore**: se si seleziona un valore, il filtro applicato visualizzerà il numero relativo all'unico filtro selezionato. Per informazioni di riferimento, vedere l'immagine seguente.

     ![Centro per i partner - Analizza - Ordine con un singolo valore applicato al filtro](./media/filters-applied-single.png)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel marketplace commerciale del Centro per i partner, vedere [Analisi per il marketplace commerciale nel Centro per i partner](./analytics.md).
- Per grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per le offerte, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Per le metriche di fatturazione a consumo e di utilizzo delle offerte di macchine virtuali, vedere [Dashboard di utilizzo nell'analisi per il marketplace commerciale](./usage-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi per il marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./downloads-dashboard.md).
- Per una visualizzazione consolidata del feedback dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard di valutazioni e recensioni nell'analisi per il marketplace commerciale](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del marketplace commerciale e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).
