---
title: Dashboard di utilizzo in analisi del Marketplace commerciale nel centro per i partner
description: Informazioni su come accedere a tutte le macchine virtuali offre l'utilizzo e le metriche di fatturazione a consumo.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 33762540d14ea51e8325abe9a466007cd7cca748
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262180"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Dashboard di utilizzo in analisi del Marketplace commerciale

Questo articolo fornisce informazioni sul dashboard Usage (utilizzo) nel centro per i partner. Questo dashboard mostra che tutte le macchine virtuali offrono metriche di fatturazione di utilizzo e a consumo in due schede separate: utilizzo della macchina virtuale e fatturazione a consumo.

Per accedere al dashboard Usage, aprire il dashboard di **[analisi](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Marketplace commerciale.

>[!NOTE]
> Per le definizioni dettagliate della terminologia di analisi, vedere [le domande frequenti e la terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).

## <a name="usage-dashboard"></a>Dashboard Utilizzo

Il dashboard Usage (utilizzo) rappresenta la metrica per tutte le macchine virtuali (VM) che offre l'utilizzo e la fatturazione a consumo. Si trovano in due schede separate: utilizzo della macchina virtuale e utilizzo della fatturazione a consumo.

Nella scheda utilizzo VM sono presenti rappresentazioni grafiche degli elementi seguenti:

- [Riepilogo utilizzo](#usage-summary)
- [Utilizzo per geografia](#usage-by-geography)
- [Utilizzo per offerte](#usage-by-offers)
- [Tendenza di utilizzo per offerte e SKU](#usage-trend-by-offers-and-skus)
- [Utilizzo per tipo di offerta](#usage-by-offer-type)
- [Utilizzo in base alle dimensioni della macchina virtuale](#usage-by-vm-size)
- [Utilizzo per canale di vendita](#usage-by-sales-channel)
- [Dati di utilizzo dettagliati](#detailed-usage-data)

> [!NOTE]
> I report di analisi vengono visualizzati in modo diverso in portale Cloud Partner (CPP) e centro per i partner. **Seller Insights** in cpp include una scheda Orders and Usage che Visualizza i dati per le offerte basate sull'utilizzo e le offerte non basate sull'utilizzo. Nel centro per i partner, le metriche di utilizzo vengono visualizzate in una pagina separata.

### <a name="usage-summary"></a>Riepilogo utilizzo

Nella tabella Riepilogo utilizzo vengono visualizzate le ore di utilizzo del cliente per tutte le offerte acquistate.

- Le ore di utilizzo normalizzate sono definite come ore di utilizzo normalizzate per tenere conto del numero di core VM ([numero di core VM] x [ore di utilizzo non elaborato]). Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore [Number of VM Cores].
- Le ore di utilizzo non elaborato sono definite come quantità di tempo di esecuzione delle macchine virtuali in termini di ore.
- Il valore percentuale rappresenta la modifica della crescita dell'utilizzo per l'intervallo di date selezionato ([utilizzo del mese precedente, utilizzo del primo mese])/utilizzo del primo mese).
- I triangoli verdi che puntano verso l'alto indicano una variazione di crescita.
- Il triangolo rosso che punta verso il basso indica una modifica di crescita negativa rispetto al mese precedente.
- I grafici a barre micro rappresentano i valori mensili. È possibile visualizzare il valore per ogni mese passando il mouse sulle colonne all'interno del grafico.

### <a name="usage-by-geography"></a>Utilizzo per geografia

L' **utilizzo normalizzato da geography** mappa termica Visualizza le ore di utilizzo mappate in base al paese del cliente. La variazione del colore del paese rappresenta una concentrazione di utilizzo normalizzata. Ripristinare la visualizzazione originale premendo il pulsante **Home** sulla mappa.

### <a name="usage-by-offers"></a>Utilizzo per offerte

- Il grafico a torta **utilizzo normalizzato per offerte** Visualizza una suddivisione delle ore di utilizzo normalizzate in base alle offerte in base all'intervallo di date selezionato. Le prime 5 offerte vengono visualizzate in Graph, mentre le altre sono raggruppate nella categoria "Rest all".
- Il grafico a barre illustra una tendenza di crescita mensile per mese per l'intervallo di date selezionato. Le colonne month rappresentano le ore di utilizzo dalle offerte con le ore di utilizzo massime per il mese corrispondente. Il grafico a linee rappresenta la tendenza percentuale di crescita tracciata sull'asse Y secondario.
- Utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra verso sinistra lungo l'asse x e/o concentrarsi su punti dati specifici.

### <a name="usage-trend-by-offers-and-skus"></a>Tendenza di utilizzo per offerte e SKU

Questo grafico mostra la tendenza dell'utilizzo normalizzato per gli SKU selezionati di un'offerta. La classifica dell'offerta Visualizza le prime 50 offerte con l'utilizzo più elevato e sono ordinate in base alle ore di utilizzo. La classifica SKU Visualizza i primi 50 SKU con l'utilizzo più elevato per l'offerta selezionata.

### <a name="usage-by-offer-type"></a>Utilizzo per tipo di offerta

- Il grafico **a torta utilizzo per tipo di offerta** organizza l'utilizzo in base al tipo di offerta.
- Le offerte principali vengono visualizzate nel grafico e il resto delle offerte viene raggruppato come "Rest all".
- Il grafico **tendenza** Visualizza le tendenze di crescita mensili per mese. La colonna Month rappresenta l'utilizzo da parte dei principali tipi di offerta in quel mese.

### <a name="usage-by-vm-size"></a>Utilizzo in base alle dimensioni della macchina virtuale

Questo grafico rappresenta la tendenza di utilizzo per le dimensioni di VM selezionate (max 5) di tutte le offerte/SKU. L'istogramma è in pila con le ore di utilizzo delle dimensioni delle VM selezionate.

Nella classifica vengono visualizzate le prime 50 dimensioni delle VM con l'utilizzo più elevato e ordinate in base alle ore di utilizzo.

### <a name="usage-by-sales-channel"></a>Utilizzo per canale di vendita

- Il grafico a torta utilizzo per canale di vendita organizza l'utilizzo in base al canale di vendita
- Il canale di vendita principale con l'utilizzo più elevato viene visualizzato nel grafico e il resto del canale di vendita viene raggruppato come "Rest all".
- La colonna Month rappresenta l'utilizzo in base al canale di vendita principale del mese.
- Le funzionalità di questo grafico corrispondono al grafico "utilizzo per offerte"

### <a name="detailed-usage-data"></a>Dati di utilizzo dettagliati

Nella **tabella dettagli utilizzo** viene visualizzato un elenco numerato dei primi 1000 record di utilizzo ordinati in base all'utilizzo.

- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file CSV se il numero di record è inferiore a 1000.
- Se il numero di record è superiore a 1000, i dati di esportazione verranno posizionati in modo asincrono in una pagina di download che sarà disponibile per i 30 giorni successivi.
- I filtri possono essere applicati ai **dati di utilizzo dettagliati** per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati in base a paese, canale di vendita, tipo di licenza Marketplace, tipo di utilizzo, nome dell'offerta, tipo di offerta, versioni di valutazione gratuite, ID sottoscrizione del Marketplace, ID cliente e nome della società.

> [!NOTE]
> Selezionare il **tipo di utilizzo** nel filtro della pagina per visualizzare i grafici nella pagina in "visualizzazione normalizzata" o "visualizzazione non elaborata". La visualizzazione predefinita per questi grafici è "visualizzazione normalizzata".

I **filtri della pagina utilizzo** vengono applicati a livello di pagina. È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si desidera visualizzare e i dati da visualizzare nella griglia "dati di utilizzo dettagliati"/esportazione. I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato nell'angolo superiore destro della pagina Orders.

- I **tipi di offerta** e **i nomi delle offerte** sono elencati solo per le offerte acquisite durante l'intervallo di date selezionato. I nomi delle offerte nell'elenco vengono visualizzati per i tipi di offerta selezionati nell'elenco.
- La selezione predefinita è "All" per ogni opzione di filtro, ad eccezione del **tipo di utilizzo**. La selezione predefinita per il **tipo di utilizzo** è l'utilizzo normalizzato. Per visualizzare l'utilizzo non elaborato nei grafici, selezionare "utilizzo non elaborato".
- I filtri applicati mostrano le selezioni di conteggio per le selezioni di filtro effettuate. I filtri applicati non vengono visualizzati per le selezioni predefinite.

> [!NOTE]
> Una definizione dettagliata di ognuno dei campi nella griglia "dati dettagliati dell'ordine", dei filtri di pagina e di tutte le selezioni possibili è definita nella sezione Dizionario dei dati dell'articolo [domande frequenti e terminologia](link needed) .

La scheda **utilizzo fatturazione a consumo** Visualizza le informazioni sull'utilizzo per i tipi di offerta in cui l'utilizzo viene misurato in base alla dimensione del contatore. L'eccedenza del tipo di offerta SaaS è attualmente visualizzata. La scheda presenta le rappresentazioni grafiche delle tendenze di eccedenza per l'utilizzo della fatturazione a consumo SaaS:

- **Tendenza in eccedenza per dimensione contatore**: Visualizza la tendenza in eccedenza mensile per la dimensione del contatore selezionata di un'offerta. L'asse X rappresenta il mese e l'asse Y rappresenta la quantità di utilizzo. Anche l'unità di misura del contatore personalizzato viene visualizzata sull'asse Y.
- **Tendenza in eccedenza per SKU**: rappresenta la tendenza della quantità di utilizzo della dimensione del contatore selezionata per SKU. Gli SKU visualizzati rappresenteranno i primi 5 SKU con la quantità massima di utilizzo per l'offerta selezionata.
- **Tendenza in eccedenza per i primi 50 clienti**: le prime 50 offerte con le ore di utilizzo più elevate vengono visualizzate in una ***bacheca*** e vengono classificate in base all'utilizzo più elevato del contatore personalizzato. Selezionare un cliente nella classifica per visualizzare la tendenza di utilizzo di una dimensione del contatore selezionata.
- **Tendenza in eccedenza per i clienti principali**: presenta i primi percentile dei clienti che contribuiscono alla percentuale di utilizzo complessivo. Il primo percentile del cliente viene visualizzato lungo l'asse X ed è determinato dalla quantità di utilizzo del cliente. L'asse Y Visualizza la quantità di utilizzo. È possibile visualizzare i dettagli posizionando il puntatore del mouse sui punti lungo il grafico a linee.

> [!NOTE]
> I dettagli di utilizzo e tutti i grafici in questa pagina vengono visualizzati per qualsiasi dimensione del contatore selezionata per il filtro di pagina.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale del centro per i partner, vedere [analisi per il Marketplace commerciale nel centro per i partner](./analytics.md).
- Per i grafici, le tendenze e i valori dei dati aggregati che riepilogano l'attività del Marketplace per l'offerta, vedere [dashboard di riepilogo in analisi del Marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard Orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [dashboard dei clienti in analisi del Marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere la pagina relativa ai [download del dashboard in Commercial Marketplace Analytics](./downloads-dashboard.md).
- Per una visualizzazione consolidata dei suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard ratings and Reviews in Commercial Marketplace Analytics](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [le domande frequenti e la terminologia per l'analisi del Marketplace commerciale](./faq-terminology.md).
