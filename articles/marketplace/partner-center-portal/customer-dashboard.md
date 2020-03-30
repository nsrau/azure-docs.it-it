---
title: Dashboard dei clienti nell'analisi di Commercial Marketplace nel Centro per i partner
description: Scopri come accedere alle informazioni sui tuoi clienti, incluse le tendenze di crescita, utilizzando il dashboard dei clienti nell'analisi di Commercial Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 7fe56a81bc82f9d49cf9f81b30986882b6d13cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286116"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Dashboard dei clienti nell'analisi di Marketplace commerciale

In questo articolo vengono fornite informazioni sul **dashboard Cliente** nel Centro per i partner. Questo dashboard visualizza le informazioni sui clienti, incluse le tendenze di crescita, presentate in un formato grafico e scaricabile.

Per accedere al **dashboard Cliente,** aprire il dashboard **[Analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Mercato commerciale.

>[!NOTE]
> Per le definizioni dettagliate della terminologia dell'analisi, vedere [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)di Commercial Marketplace.

## <a name="customer-dashboard"></a>Dashboard Cliente

Il **dashboard Cliente** del menu **Analizza** visualizza i dati relativi ai clienti che hanno acquisito le offerte. È possibile visualizzare le rappresentazioni grafiche dei seguenti elementi:

- [Riepilogo clienti](#customer-summary)
- [Cliente per area geografica](#customer-by-geography)
- [Tendenze dei clienti](#customer-trends)
- [Clienti per ordini e utilizzo](#customers-by-orders-and-usage)
- [Clienti per SKU](#customers-by-skus)
- [Ordini e utilizzo per tipo di cliente](#orders-and-usage-by-customer-type)
- [Tabella Dettagli cliente](#customer-details-table)
- [Filtri di pagina dei clienti](#customer-page-filters)

### <a name="customer-summary"></a>Riepilogo clienti

Nella sezione Riepilogo clienti viene visualizzato un conteggio di tutti i clienti, inclusi nuovi, esistenti e sfornati, durante l'intervallo di date selezionato.

- Il totale dei clienti è definito come il conteggio di tutti i clienti che hanno acquistato l'offerta e hanno almeno un ordine che non è stato annullato.
- La percentuale di crescita dei clienti rispetto al mese precedente è indicata dall'indicatore del numero e dell'indicatore verso l'alto in rosso o verde.
- Le tendenze di crescita sono rappresentate da grafici a barre e visualizzeranno il valore di ogni mese passando il mouse sopra le colonne del grafico.

Ci sono tre **tipi di clienti:** nuovi, esistenti e sfornato.

- Un nuovo cliente ha acquisito una o più delle tue offerte per la prima volta entro il mese selezionato.
- Un cliente esistente ha acquisito una o più offerte prima del mese selezionato.
- Un cliente sfornato ha annullato tutte le offerte acquistate in precedenza.

### <a name="customer-by-geography"></a>Cliente per area geografica

Il grafico **Cliente per area geografica** mostra i conteggi di tutti i clienti e i clienti acquisiti durante l'intervallo di date selezionato e viene mappato in base al Paese del cliente. Il colore da chiaro a scuro sulla mappa rappresenta il valore da basso a alto del numero di clienti. Fare clic su un record nella tabella per ingrandire un paese.

La mappa termica visualizza il numero di clienti e % per paese del cliente. È possibile spostare la mappa per visualizzare la posizione esatta e ingrandire una posizione specifica. Questa mappa ha una griglia supplementare che consente di visualizzare la percentuale di clienti in base alla posizione, nonché i clienti appena aggiunti a tale posizione.

### <a name="customer-trends"></a>Tendenze dei clienti

Il grafico **Tendenze cliente** visualizza un conteggio di tutti i clienti, inclusi nuovi, esistenti e sfornato, con un trend di crescita mese per mese.

- Il grafico a linee rappresenta le percentuali di crescita complessive dei clienti.
- La colonna month rappresenta il numero di clienti impilati da clienti nuovi, esistenti e sfornato.
- Il numero di clienti sfornato viene visualizzato nella direzione negativa dell'asse Y.
- È possibile selezionare elementi della legenda specifici per visualizzare visualizzazioni più dettagliate. Ad esempio, selezionare nuovi clienti dalla legenda per visualizzare solo i nuovi clienti.
- È possibile utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere verso destra e sinistra sull'asse x e concentrarsi su punti dati specifici per visualizzare in modo più dettagliato.
- Passando il mouse su una colonna del grafico verranno visualizzati i dettagli solo per quel mese.

### <a name="customers-by-orders-and-usage"></a>Clienti per ordini e utilizzo

Il grafico **Clienti per ordini/utilizzo** include tre schede, "ordini", "utilizzo normalizzato" e "utilizzo non elaborato". Il **percentile del cliente superiore** viene visualizzato lungo l'asse x, come determinato dal numero di ordini. L'asse y visualizza il conteggio degli ordini del cliente. L'asse y secondario (grafico a linee) visualizza la percentuale cumulativa del numero totale di ordini. È possibile visualizzare i dettagli passando il mouse sui punti lungo il grafico a linee.

Ad esempio, vedere il grafico seguente per l'utilizzo normalizzato: il primo trentesimo percentile dei clienti contribuisce all'87% dell'utilizzo normalizzato in modo cumulativo. Il 30o percentile dei clienti contribuisce solo a 1,57 milioni di ore di utilizzo.

### <a name="customers-by-skus"></a>Clienti per SKU

I **grafici Customers by SKUs/usage** sono descritti di seguito.

1. La classifica presenta i dettagli dei primi 50 clienti classificati in base al conteggio degli ordini. Dopo aver selezionato un cliente, i dettagli del cliente sono presentati nelle sezioni 2, 3 e 4 di questa classifica.
2. I dettagli del profilo cliente vengono visualizzati in questo spazio quando i publisher sono connessi con un ruolo di proprietario. Se i publisher sono connessi con un ruolo di collaboratore, i dettagli in questa sezione non saranno disponibili.
3. Il grafico Ordini per ciambella Ordini per SKU visualizza la suddivisione degli ordini acquistati per gli SKU. Vengono visualizzati i primi 5 SKU con il numero di ordini più alto, mentre il resto degli ordini è raggruppato in "resto tutti".
4. Il grafico Ciambella Posti per SKU mostra la ripartizione delle postazioni ordinate per gli SKU. Vengono visualizzati i primi 5 SKU con i posti più alti, mentre il resto degli ordini è raggruppato in riposo.

### <a name="orders-and-usage-by-customer-type"></a>Ordini e utilizzo per tipo di cliente

Il grafico **Ordini/utilizzo per tipo cliente** visualizza il numero di ordini, l'utilizzo normalizzato e le ore di utilizzo non elaborate suddivise tra i nuovi clienti e i clienti esistenti; in base alla selezione di ordini, normalizzati e di utilizzo non elaborato rispettivamente nel grafico.

- Un nuovo cliente ha acquisito una o più offerte o l'utilizzo segnalato per la prima volta all'interno dello stesso mese di calendario (asse y).
- Un cliente esistente ha precedentemente acquisito un'offerta dall'utente o l'utilizzo riportato prima del mese di calendario segnalato (sull'asse y).

### <a name="customer-details-table"></a>Tabella Dettagli cliente

Nella tabella **Dettagli cliente** viene visualizzato un elenco numerato dei primi 1000 clienti ordinati in base alla data in cui hanno acquisito una delle offerte per la prima volta.

- Le informazioni personali del cliente saranno disponibili solo se il cliente ha fornito il consenso. È possibile visualizzare queste informazioni solo se è stato effettuato l'accesso con un livello di autorizzazioni per il ruolo proprietario. Ulteriori informazioni sui ruoli utente e sulle autorizzazioni.
- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file TSV se il conteggio dei record è inferiore a 1000.
- Se il numero di record è superiore a 1000, i dati esportati verranno inseriti in modo asincrono in una pagina di download per i prossimi 30 giorni.
- I filtri possono essere applicati alla tabella per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati in base alla ragione della società, all'ID cliente, all'ID sottoscrizione Marketplace, al tipo di licenza di Azure, alla data di acquisizione, alla data di perdita, all'e-mail del cliente, al paese/stato del cliente/città/zip, alla lingua del cliente e così via.

### <a name="customer-page-filters"></a>Filtri di pagina dei clienti

I filtri **di pagina Clienti** vengono applicati a livello di pagina Clienti. È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si sceglie di visualizzare e i dati che si desidera visualizzare nella griglia /esportazione dei dati degli ordini dettagliati. I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato nell'angolo superiore destro della pagina degli ordini.

>[!NOTE]
> Definizioni dettagliate per ognuno dei campi nella griglia Cliente, i filtri di pagina e le relative selezioni sono disponibili in [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)di Commercial Marketplace.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale del Centro per i partner, vedere Analisi per il Marketplace commerciale nel Centro per i [partner.](./analytics.md)
- Per grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per la tua offerta, vedi [Dashboard di riepilogo nell'analisi](./summary-dashboard.md)di Commercial Marketplace.
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Orders Dashboard in Commercial Marketplace analytics](./orders-dashboard.md).
- Per la macchina virtuale (VM) offre l'utilizzo e le metriche di fatturazione a consumo, vedere [Dashboard di utilizzo nell'analisi](./usage-dashboard.md)di Commercial Marketplace.
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Download Dashboard in Commercial Marketplace analytics](./downloads-dashboard.md).
- Per visualizzare una visualizzazione consolidata dei commenti e suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Valutazioni e recensioni del dashboard nell'analisi](./ratings-reviews.md)di Commercial Marketplace.
- Per le domande frequenti sull'analisi di Commercial Marketplace e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)di Commercial Marketplace .
