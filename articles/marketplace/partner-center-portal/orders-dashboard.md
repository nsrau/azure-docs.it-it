---
title: Dashboard Ordini del Centro per i partner nell'analisi di Commercial Marketplace
description: Scopri come accedere ai report analitici sul tuo marketplace in un formato grafico e scaricabile.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 148b80a6843168b11e4feb3558f6bf08eaeb3ace
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261262"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Dashboard degli ordini nell'analisi del mercato commerciale

In questo articolo vengono fornite informazioni sul **dashboard Ordini** nel Centro per i partner. Questo dashboard visualizza le informazioni sugli ordini in un formato grafico e scaricabile.

Per accedere al **dashboard Ordini** negli strumenti di analisi del Centro per i partner, aprire il **[dashboard Analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Commercial Marketplace.

>[!NOTE]
> Per le definizioni dettagliate della terminologia dell'analisi, vedere [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)del mercato commerciale.

## <a name="orders-dashboard"></a>Dashboard Ordini

Il **pannello Ordini** del menu **Analizza** visualizza gli ordini correnti per tutte le offerte SaaS. È possibile visualizzare le rappresentazioni grafiche dei seguenti elementi:

- [Riepilogo degli ordini](#order-summary)
- [Ordini per area geografica](#orders-by-geography)
- [Ordini per offerte](#orders-by-offers)
- [Tendenza degli ordini per sito rispetto a un posto](#orders-trend-per-site-versus-per-seat)
- [Ordini per SKU](#orders-by-skus)
- [Tendenza ordini e posti a sedere](#orders-and-seats-trend)
- [Tabella dei dettagli dell'ordine](#order-details-table)

> [!NOTE]
> Esistono differenze tra la visualizzazione dei report di analisi nel portale Cloud Partner (CPP) e il nuovo programma del marketplace commerciale nel Centro per i partner. Un modo specifico è che le **informazioni sul venditore** in CPP hanno una scheda **Ordini & Utilizzo,** che visualizza i dati per le offerte basate sull'utilizzo e le offerte non basate sull'utilizzo. Nel Centro per i partner, la pagina **Ordini** include una scheda separata per le offerte SaaS.

## <a name="order-dashboard-details"></a>Dettagli dashboard ordini

Questa sezione descrive i report di analisi in modo più dettagliato.

### <a name="order-summary"></a>Riepilogo dell'ordine

Nella sezione Riepilogo ordini viene visualizzato il conteggio di tutti gli ordini acquistati (esclusi gli ordini annullati), gli ordini annullati e le postazioni.

Il valore percentuale accanto a Ordini totali rappresenta l'importo della crescita dell'intervallo di date selezionato.

![Centro per i partner Analizzare il riepilogo degli ordini](./media/order-summary.png)

- Un triangolo verde che punta verso l'alto indica un trend di crescita positivo.
- Un triangolo rosso rivolto verso il basso indica una tendenza di crescita negativa rispetto al mese precedente.
- Le tendenze di crescita sono rappresentate da micro grafici a barre. È possibile visualizzare il valore di ogni mese passando il mouse sulle colonne all'interno del grafico.
- Gli ordini annullati sono un conteggio degli ordini acquistati in precedenza e quindi annullati durante l'intervallo di date selezionato.
- I posti a sedere sono un conteggio delle postazioni create durante l'intervallo di date selezionato.

### <a name="orders-by-geography"></a>Ordini per area geografica

La mappa di calore **Ordini per area** geografica mostra un conteggio degli ordini su una mappa del mondo e mostra le postazioni mappate in base al paese del cliente. Questa mappa termica funziona come il cliente per mappa di **[calore geografica](./customer-dashboard.md#customer-by-geography)**.

![Centro per i partner Analizzare gli ordini per area geografica](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Ordini per offerte

Il grafico **Ordini per offerte** ciambella organizza gli ordini (compresi gli ordini annullati) in base ai nomi delle loro offerte.

- Le offerte principali sono visualizzate nel grafico e le altre offerte sono raggruppate come 'Rest All'.
- È possibile selezionare offerte specifiche nella legenda per visualizzare solo quelle offerte nel grafico.
- Passando il mouse su una sezione nel grafico verrà visualizzato il numero di ordini e la percentuale di tale offerta rispetto al numero totale di ordini in tutte le offerte.
- **L'andamento degli ordini per offerte** mostra le tendenze di crescita mese per mese. La colonna del mese rappresenta il numero di ordini in base al nome dell'offerta. Il grafico a linee visualizza la tendenza percentuale di crescita tracciata su un asse z.
- È possibile utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere verso destra e sinistra lungo l'asse x e concentrarsi su punti dati specifici.
- È possibile visualizzare il grafico delle tendenze selezionando un elemento specifico nella legenda.
- È inoltre possibile scegliere di visualizzare tendenze e dati per **gli ordini annullati.** Il grafico funzionerà nello stesso modo in cui gli **ordini per grafico offerte.**

### <a name="orders-trend-per-site-versus-per-seat"></a>Ordini Tendenza per sito rispetto a per postazione

Il grafico della ciambella per **sito rispetto** al posto rappresenta la ripartizione degli ordini SaaS per sito e per postazione acquistati dai clienti (questo grafico include ordini annullati). L'istogramma rappresenta la tendenza degli ordini SaaS per sito e per postazione acquistati dai clienti (questo grafico include gli ordini annullati).

### <a name="orders-by-skus"></a>Ordini per SKU

Il grafico **Ordini per SKU** rappresenta la tendenza degli ordini a livello di unità di conservazione delle azioni (SKU) per tutte le offerte (inclusi gli ordini annullati). Il grafico ad anello rappresenta la scomposizione dei primi cinque ordini SKU e l'istogramma rappresenta la tendenza degli ordini per i primi cinque SKU.

### <a name="orders-and-seats-trend"></a>Ordine e seggi Trend

Il grafico **di tendenza degli ordini e dei posti** presenta le prime 50 offerte con il maggior numero di ordini. Questi vengono visualizzati in una classifica e sono ordinati in base al numero di ordine e alla percentuale di ordine più alti.

- **Ordini per SKU:** selezionare un'offerta per visualizzare la suddivisione del conteggio degli ordini per i primi cinque SKU nel grafico.
- **Posti per SKU**: L'andamento mensile dei posti per i primi cinque SKU. Se l'offerta selezionata non è un'offerta per posto, non verrà visualizzato alcun dato in questo grafico ad area.

### <a name="canceled-orders-by-offers"></a>Ordini annullati per offerte

Il grafico a torta **Ordini annullati per offerte** organizza tutti gli ordini annullati in base ai nomi delle offerte. Le offerte principali vengono visualizzate nel grafico e le altre offerte sono raggruppate come "Resto tutto". È possibile selezionare offerte specifiche nella legenda da visualizzare nel grafico.

- Passando il mouse su una sezione nel grafico verrà visualizzato il numero di ordini e la percentuale dell'offerta selezionata rispetto al numero totale di ordini in tutte le offerte.
- Nell'istogramma vengono visualizzate le tendenze mese per mese. Le colonne rappresentano il numero di ordini annullati in base al nome dell'offerta. È possibile utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere verso destra e sinistra lungo l'asse x e concentrarsi su punti dati specifici. È possibile visualizzare il grafico delle tendenze selezionando un elemento specifico nella legenda.

### <a name="order-details-table"></a>Tabella dei dettagli dell'ordine

Nella tabella Dettagli ordine viene visualizzato un elenco numerato dei 1000 ordini principali ordinati in base alla data di acquisizione.

- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file TSV se il conteggio dei record è inferiore a 1000.
- Se i record sono oltre 1000, i dati esportati verranno inseriti in modo asincrono in una pagina di download per i prossimi 30 giorni.
- I filtri possono essere applicati alla **tabella Dettagli ordine** per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati per Paese, Tipo di licenza di Azure, Tipo di licenza Marketplace, Tipo di offerta, Stato ordine, Percorsi gratuiti, ID sottoscrizione Marketplace, ID cliente e Nome società.

#### <a name="orders-page-filters"></a>Ordini Filtri pagina

Questi filtri vengono applicati a livello di pagina.

È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si sceglie di visualizzare e dei dati che si desidera visualizzare nella griglia/esportazione dei dati **dettagliati dell'ordine.** I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato nell'angolo in alto a destra della pagina degli ordini.

- I tipi di offerta e i nomi delle offerte sono elencati solo per le offerte per cui hai ordini durante l'intervallo di date selezionato. I nomi delle offerte nell'elenco vengono visualizzati per i tipi di offerte selezionati nell'elenco.
- I filtri applicati mostrano le metriche totali all'interno di ogni selezione per ogni filtro selezionato. I filtri applicati non vengono visualizzati quando si preleva la selezione predefinita.
- Se è selezionata l'opzione **Tutto** per uno degli elenchi a discesa, tutte le metriche nella pagina selezionata verranno aggregate. Ad esempio: "Tutti" nell'opzione di filtro dei tipi di offerta indica che sono stati selezionati tutti i tipi di offerta. Questa è la selezione predefinita per gli elenchi a discesa. I filtri applicati non mostrano nulla quando è selezionata **l'opzione Tutto.**
- **Selezione di**più valori: tutte le metriche nella pagina verranno aggregate per tutte le selezioni effettuate nell'elenco a discesa. Se vengono effettuate più selezioni, il filtro applicato mostrerà il conteggio di tutte le selezioni effettuate. Vedere l'immagine seguente come riferimento.

    ![Centro per i partner Analizzare l'ordine con più valori applicati per filtrare](./media/filters-applied.png)

- **Selezione a valore singolo**: se è selezionato un valore, il filtro applicato mostrerà il conteggio di un filtro selezionato. Vedere sotto l'immagine per riferimento.

     ![Centro per i partner Analizzare l'ordine con un singolo valore applicato al filtro](./media/filters-applied-single.png)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel marketplace commerciale del Centro per i partner, vedere Analisi per il marketplace commerciale nel Centro per i [partner.](./analytics.md)
- Per i grafici, le tendenze e i valori dei dati aggregati che riepilogano l'attività del marketplace per la tua offerta, vedi [Dashboard di riepilogo nell'analisi](./summary-dashboard.md)del marketplace commerciale.
- Per la macchina virtuale (VM) offre l'utilizzo e le metriche di fatturazione a consumo, vedere [Dashboard di utilizzo nell'analisi del marketplace commerciale.](./usage-dashboard.md)
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi](./customer-dashboard.md)del mercato commerciale .
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard download nell'analisi](./downloads-dashboard.md)del marketplace commerciale .
- Per visualizzare una visualizzazione consolidata dei commenti e suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Valutazioni e recensioni del dashboard nell'analisi](./ratings-reviews.md)del marketplace commerciale.
- Per le domande frequenti sull'analisi del mercato commerciale e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)del mercato commerciale.
