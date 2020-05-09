---
title: Dashboard dei clienti in Microsoft Commercial Marketplace Analytics nel centro per i partner
description: Informazioni su come accedere alle informazioni sui clienti, incluse le tendenze di crescita, usando il dashboard dei clienti in analisi del Marketplace commerciale.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 4d9dc0315a5c87ed67390e8edb23d44ee55f242b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732768"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Dashboard dei clienti in analisi del Marketplace commerciale

Questo articolo fornisce informazioni sul **Dashboard del cliente** nel centro per i partner. Questo dashboard Visualizza le informazioni sui clienti, incluse le tendenze di crescita, presentate in un formato grafico e scaricabile.

Per accedere al **Dashboard del cliente**, aprire il dashboard **[analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Marketplace commerciale.

>[!NOTE]
> Per le definizioni dettagliate della terminologia di analisi, vedere [le domande frequenti e la terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).

## <a name="customer-dashboard"></a>Dashboard Cliente

Il **Dashboard del cliente** del menu **analizza** Visualizza i dati per i clienti che hanno acquisito le offerte. È possibile visualizzare le rappresentazioni grafiche degli elementi seguenti:

- [Riepilogo cliente](#customer-summary)
- [Customer by geography](#customer-by-geography)
- [Tendenze dei clienti](#customer-trends)
- [Clienti per ordine e utilizzo](#customers-by-orders-and-usage)
- [Clienti per SKU](#customers-by-skus)
- [Ordini e utilizzo per tipo di cliente](#orders-and-usage-by-customer-type)
- [Tabella Dettagli cliente](#customer-details-table)
- [Filtri della pagina dei clienti](#customer-page-filters)

### <a name="customer-summary"></a>Riepilogo cliente

Nella sezione Riepilogo del cliente viene visualizzato il conteggio di tutti i clienti, inclusi quelli nuovi, esistenti e in varianza, durante l'intervallo di date selezionato.

- Il totale dei clienti viene definito come il conteggio di tutti i clienti che hanno acquistato l'offerta e hanno almeno un ordine che non è stato annullato.
- La percentuale di crescita dei clienti rispetto al mese precedente è indicata dal numero e dall'indicatore ascendente in verde o verso il basso in rosso.
- Le tendenze di crescita sono rappresentate da grafici a barre e visualizzeranno il valore per ogni mese passando il mouse sulle colonne del grafico.

Sono disponibili tre **tipi di clienti**: nuovo, esistente e varianza.

- Un nuovo cliente ha acquisito una o più offerte per la prima volta entro il mese selezionato.
- Un cliente esistente ha acquisito una o più offerte prima del mese selezionato.
- Un cliente con varianza ha annullato tutte le offerte acquistate in precedenza.

### <a name="customer-by-geography"></a>Customer by geography

Il grafico **Customer by geography** Mostra i conteggi di tutti i clienti e i clienti acquisiti durante l'intervallo di date selezionato e vengono mappati in base al paese del cliente. Il colore da chiaro a scuro sulla mappa rappresenta il valore minimo e massimo del numero di clienti. Fare clic su un record nella tabella per eseguire lo zoom avanti su un paese.

Il mappa termica Visualizza il numero di clienti e% in base al paese del cliente. È possibile spostare la mappa per visualizzare il percorso esatto e ingrandire una posizione specifica. Questa mappa presenta una griglia supplementare che consente di visualizzare la percentuale di clienti in base alla località, oltre ai clienti appena aggiunti a tale località.

### <a name="customer-trends"></a>Tendenze dei clienti

Il grafico **tendenze dei clienti** Visualizza un conteggio di tutti i clienti, inclusi quelli nuovi, esistenti e in varianza, con una tendenza di crescita mensile per mese.

- Il grafico a linee rappresenta le percentuali di crescita complessive del cliente.
- La colonna Month rappresenta il conteggio dei clienti in pila per i clienti nuovi, esistenti e rilevati.
- Il numero di clienti varianza viene visualizzato sulla direzione negativa dell'asse Y.
- È possibile selezionare elementi di legenda specifici per visualizzare visualizzazioni più dettagliate. Ad esempio, selezionare nuovi clienti dalla legenda per visualizzare solo i nuovi clienti.
- È possibile utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra e a sinistra sull'asse x e concentrarsi su punti dati specifici per visualizzarli in modo più dettagliato.
- Se si passa il puntatore del mouse su una colonna del grafico, vengono visualizzati i dettagli solo per quel mese.

### <a name="customers-by-orders-and-usage"></a>Clienti per ordine e utilizzo

Il grafico **Customers by Orders/Usage** include tre schede, "Orders", "normalizzated Usage" e "Raw Usage". Il **primo percentile del cliente** viene visualizzato lungo l'asse x, come determinato dal numero di ordini. L'asse y Visualizza il numero di ordini del cliente. L'asse y secondario (grafico a linee) Visualizza la percentuale cumulativa del numero totale di ordini. È possibile visualizzare i dettagli posizionando il puntatore del mouse sui punti lungo il grafico a linee.

Per un esempio, vedere il grafico seguente per l'utilizzo normalizzato: il 30 ° percentile più alto dei clienti contribuisce al 87% dell'utilizzo normalizzato in modo cumulativo. Il 30 ° percentile dei clienti contribuisce solo a 1.570.000 ore di utilizzo.

### <a name="customers-by-skus"></a>Clienti per SKU

I grafici **Customers by SKU/Usage** sono descritti di seguito.

1. La lavagna leader presenta i dettagli dei primi 50 clienti classificati in base al numero di ordini. Dopo aver selezionato un cliente, i dettagli del cliente vengono presentati nelle sezioni 2, 3 e 4 di questa bacheca.
2. I dettagli del profilo cliente vengono visualizzati in questo spazio quando i Publisher sono connessi con un ruolo proprietario. Se i Publisher sono connessi con un ruolo Collaboratore, i dettagli in questa sezione non saranno disponibili.
3. Il grafico ad anello Orders by SKU Visualizza la suddivisione degli ordini acquistati per gli SKU. Vengono visualizzati i primi 5 SKU con il numero massimo di ordini, mentre gli altri ordini sono raggruppati in "Rest all".
4. Il diagramma ad anello seats by SKU Visualizza la suddivisione dei seggi ordinati per gli SKU. Vengono visualizzati i primi 5 SKU con le postazioni più elevate, mentre gli altri ordini sono raggruppati in rest all.

### <a name="orders-and-usage-by-customer-type"></a>Ordini e utilizzo per tipo di cliente

Il grafico **Orders/Usage by Customer Type** Visualizza il numero di ordini, l'utilizzo normalizzato e le ore di utilizzo non elaborate divise tra i nuovi clienti e i clienti esistenti. in base alla selezione di ordini, normalizzati e utilizzo non elaborato rispettivamente nel grafico.

- Un nuovo cliente ha acquisito una o più offerte o ha segnalato l'utilizzo per la prima volta nello stesso mese di calendario (asse y).
- Un cliente esistente ha precedentemente acquisito un'offerta o ha segnalato l'utilizzo prima del mese di calendario segnalato (sull'asse y).

### <a name="customer-details-table"></a>Tabella Dettagli cliente

La tabella **Customer Details** Visualizza un elenco numerato dei primi 1000 clienti ordinati in base alla data in cui è stata acquisita per la prima volta una delle offerte.

- Le informazioni personali del cliente saranno disponibili solo se il cliente ha fornito il consenso. È possibile visualizzare queste informazioni solo se è stato effettuato l'accesso con un livello di autorizzazione del ruolo proprietario. Altre informazioni sui ruoli utente e le autorizzazioni.
- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file TSV se il numero di record è inferiore a 1000.
- Se il numero di record è superiore a 1000, i dati esportati verranno posizionati in modo asincrono in una pagina di download per i successivi 30 giorni.
- I filtri possono essere applicati alla tabella per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati in base al nome della società, all'ID cliente, all'ID sottoscrizione del Marketplace, al tipo di licenza di Azure, alla data di acquisizione, alla data di smarrimento, alla posta elettronica del cliente, alla lingua del cliente, alla lingua e così via.

### <a name="customer-page-filters"></a>Filtri della pagina dei clienti

I filtri della **pagina Customers** vengono applicati a livello di pagina Customers. È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri scelti per la visualizzazione e i dati da visualizzare nella griglia o nell'esportazione dei dati degli ordini dettagliati. I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato nell'angolo superiore destro della pagina Orders.

>[!NOTE]
> Le definizioni dettagliate per ognuno dei campi nella griglia Customer, i filtri di pagina e le relative selezioni possibili si trovano nelle [domande frequenti e nella terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale del centro per i partner, vedere [analisi per il Marketplace commerciale nel centro per i partner](./analytics.md).
- Per i grafici, le tendenze e i valori dei dati aggregati che riepilogano l'attività del Marketplace per l'offerta, vedere [dashboard di riepilogo in analisi del Marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard Orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Per la macchina virtuale (VM) offre metriche di fatturazione di utilizzo e a consumo, vedere [dashboard di utilizzo in analisi del Marketplace commerciale](./usage-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere la pagina relativa ai [download del dashboard in Commercial Marketplace Analytics](./downloads-dashboard.md).
- Per una visualizzazione consolidata dei suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard ratings and Reviews in Commercial Marketplace Analytics](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [le domande frequenti e la terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).
