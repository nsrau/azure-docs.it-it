---
title: Dashboard di utilizzo nell'analisi di Commercial Marketplace nel Centro per i partner
description: Informazioni su come accedere a tutte le macchine virtuali offerte e metriche di fatturazione a consumo.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0cd6614c1ccc51a2b25f115dccf0ee389075aa70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285130"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Dashboard di utilizzo nell'analisi di Commercial Marketplace

In questo articolo vengono fornite informazioni sul dashboard Utilizzo nel Centro per i partner. Questo dashboard visualizza tutte le macchine virtuali offre l'utilizzo e le metriche di fatturazione a consumo in due schede separate: utilizzo della macchina virtuale e utilizzo della fatturazione a consumo.

Per accedere al dashboard Utilizzo, aprire il dashboard **[Analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Marketplace commerciale.

>[!NOTE]
> Per le definizioni dettagliate della terminologia dell'analisi, vedere [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)di Commercial Marketplace.

## <a name="usage-dashboard"></a>Dashboard Utilizzo

Il dashboard di utilizzo rappresenta le metriche per tutte le macchine virtuali (VM) che offrono l'utilizzo e l'utilizzo della fatturazione a consumo. Questi si trovano in due schede separate: utilizzo della macchina virtuale e utilizzo della fatturazione a consumo.

Nella scheda Utilizzo macchina virtuale sono disponibili rappresentazioni grafiche degli elementi seguenti:In the VM usage tab, there are graphical representations of the following items:

- [Riepilogo utilizzo](#usage-summary)
- [Utilizzo per area geografica](#usage-by-geography)
- [Utilizzo da parte delle offerte](#usage-by-offers)
- [Tendenza all'utilizzo da parte di offerte e SKU](#usage-trend-by-offers-and-skus)
- [Utilizzo per tipo di offerta](#usage-by-offer-type)
- [Utilizzo in base alle dimensioni della macchina virtualeUsage by VM size](#usage-by-vm-size)
- [Utilizzo per canale di vendita](#usage-by-sales-channel)
- [Dati di utilizzo dettagliati](#detailed-usage-data)

> [!NOTE]
> I report di Analytics vengono visualizzati in modo diverso nel portale per i partner cloud (CPP) e nel Centro per i partner. **Le informazioni dettagliate** sui venditori in CPP hanno una scheda ordini e utilizzo, che visualizza i dati sia per le offerte basate sull'utilizzo che per le offerte non basate sull'utilizzo. Nel Centro per i partner, le metriche di utilizzo vengono visualizzate in una pagina separata.

### <a name="usage-summary"></a>Riepilogo utilizzo

Nella tabella di riepilogo dell'utilizzo vengono visualizzate le ore di utilizzo del cliente per tutte le offerte acquistate.

- Le ore di utilizzo normalizzate sono definite come le ore di utilizzo normalizzate per tenere conto del numero di core della macchina virtuale ([numero di core di macchine virtuali] x [ore di utilizzo non elaborato]). Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore [numero di core di VM].
- Le ore di utilizzo non elaborate sono definite come la quantità di tempo per cui le macchine virtuali sono state eseguite in termini di ore.
- Il valore percentuale rappresenta la variazione di crescita dell'utilizzo per l'intervallo di date selezionato ([ultimo mese di utilizzo – utilizzo del primo mese])/utilizzo del primo mese).
- I triangoli verdi rietti verso l'alto indicano un cambiamento di crescita.
- Triangolo rosso rivolto verso il basso indica una variazione di crescita negativa rispetto al mese precedente.
- I grafici a barre micro rappresentano valori mensili. È possibile visualizzare il valore di ogni mese passando il mouse sulle colonne all'interno del grafico.

### <a name="usage-by-geography"></a>Utilizzo per area geografica

**L'utilizzo normalizzato per** mappa termica geografica visualizza le ore di utilizzo mappate in base al paese del cliente. Variazione di colore paese rappresenta la concentrazione di utilizzo normalizzato. Ripristinare la visualizzazione originale premendo il pulsante **Home** sulla mappa.

### <a name="usage-by-offers"></a>Utilizzo da parte delle offerte

- **L'utilizzo normalizzato per grafico** a torta delle offerte visualizza una suddivisione delle ore di utilizzo normalizzate per le offerte in base all'intervallo di date selezionato. Le prime 5 offerte sono visualizzate nel grafico, mentre le altre sono raggruppate nella categoria "resto tutto".
- Il grafico a barre illustra una tendenza di crescita mese per mese per l'intervallo di date selezionato. Le colonne del mese rappresentano le ore di utilizzo delle offerte con le ore di utilizzo più elevate per il rispettivo mese. Il grafico a linee mostra la tendenza percentuale di crescita tracciata sull'asse Y secondario.
- Utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere da destra a sinistra lungo l'asse x e/o concentrarsi su punti dati specifici.

### <a name="usage-trend-by-offers-and-skus"></a>Tendenza all'utilizzo da parte di offerte e SKU

Questo grafico mostra la tendenza dell'utilizzo normalizzato per gli SKU selezionati di un'offerta. La classifica dell'offerta visualizza le prime 50 offerte con il massimo utilizzo e vengono ordinate in base alle ore di utilizzo. Nella classifica SKU vengono visualizzati i primi 50 SKU con il massimo utilizzo per l'offerta selezionata.

### <a name="usage-by-offer-type"></a>Utilizzo per tipo di offerta

- **L'utilizzo per tipo di offerta** grafico a torta organizza l'utilizzo in base al tipo di offerta.
- Le offerte principali sono visualizzate nel grafico e le altre offerte sono raggruppate come 'Rest All'.
- Il grafico **delle tendenze** mostra le tendenze di crescita mese per mese. La colonna del mese rappresenta l'utilizzo in base ai principali tipi di offerta in quel mese.

### <a name="usage-by-vm-size"></a>Utilizzo in base alle dimensioni della macchina virtualeUsage by VM size

Questo grafico rappresenta la tendenza di utilizzo per le dimensioni di VM selezionate (massimo 5) di tutte le offerte/SKU. L'istogramma è in pila con le ore di utilizzo delle dimensioni della macchina virtuale selezionata.

La classifica visualizza le prime 50 dimensioni delle macchine virtuali con il massimo utilizzo e ordinate in base alle ore di utilizzo.

### <a name="usage-by-sales-channel"></a>Utilizzo per canale di vendita

- L'utilizzo per grafico a torta del canale di vendita organizza l'utilizzo in base al canale di vendita
- Il canale di vendita superiore con il più alto utilizzo viene visualizzato nel grafico e il resto del canale di vendita viene raggruppato come 'Rest All'.
- La colonna del mese rappresenta l'utilizzo per il canale di vendita principale in quel mese.
- Le caratteristiche di questo grafico sono le stesse del grafico "Utilizzo per offerte"

### <a name="detailed-usage-data"></a>Dati di utilizzo dettagliati

Nella tabella dei dettagli di **utilizzo** viene visualizzato un elenco numerato dei primi 1000 record di utilizzo ordinati in base all'utilizzo.

- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file CSV se il conteggio dei record è inferiore a 1000.
- Se il numero di record è superiore a 1000, i dati di esportazione verranno inseriti in modo asincrono in una pagina di download che sarà disponibile per i prossimi 30 giorni.
- I filtri possono essere applicati **ai dati di utilizzo dettagliati** per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati per paese, canale di vendita, tipo di licenza Marketplace, tipo di utilizzo, nome dell'offerta, tipo di offerta, versioni di valutazione gratuite, ID sottoscrizione Marketplace, ID cliente e nome della società.

> [!NOTE]
> Selezionare il **tipo di utilizzo** nel filtro di pagina per visualizzare i grafici nella pagina in "Visualizzazione normalizzata" o "Visualizzazione non elaborata". La visualizzazione predefinita per questi grafici è "Visualizzazione normalizzata".

I **filtri di pagina Utilizzo** vengono applicati a livello di pagina. È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si sceglie di visualizzare e i dati che si desidera visualizzare nella griglia/esportazione dei dati di utilizzo dettagliati. I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato nell'angolo in alto a destra della pagina degli ordini.

- **I tipi di offerta** e **i nomi delle offerte** sono elencati solo per le offerte acquisite durante l'intervallo di date selezionato. I nomi delle offerte nell'elenco vengono visualizzati per i tipi di offerta selezionati dall'elenco.
- La selezione predefinita è "Tutti" per ciascuna delle opzioni di filtro, ad eccezione di **Tipo di utilizzo**. La selezione predefinita per **Tipo di utilizzo** è l'utilizzo normalizzato. Per visualizzare l'utilizzo non elaborato nei grafici, selezionare "utilizzo non elaborato".
- I filtri applicati mostrano le selezioni di conteggio per le selezioni di filtro effettuate. I filtri applicati non vengono visualizzati per le selezioni predefinite.

> [!NOTE]
> Una definizione dettagliata di ciascuno dei campi nella griglia "dati dettagliati dell'ordine", i filtri di pagina e tutte le possibili selezioni sono definiti nella sezione dizionario dei dati dell'articolo [FAQ e terminologia.](link needed)

La scheda **Utilizzo fatturazione a consumo** presenta informazioni sull'utilizzo per i tipi di offerta in cui l'utilizzo viene misurato in base alla dimensione del contatore. Attualmente viene presentata l'overage del tipo di offerta SaaS. La scheda presenta le rappresentazioni grafiche delle tendenze di esatura per l'utilizzo della fatturazione a consumo SaaS:

- **Tendenza overage per dimensione contatore**: Visualizza la tendenza mensile di superamento per la dimensione del contatore selezionato di un'offerta. L'asse X rappresenta il mese e l'asse Y rappresenta la quantità di utilizzo. L'unità di misura del misuratore personalizzato viene visualizzata anche sull'asse Y.
- **Tendenza dell'overage per SKU**: rappresenta la tendenza della quantità di utilizzo della dimensione del contatore selezionata in base alle SKU. Gli SKU visualizzati rappresenteranno i primi 5 SKU con la massima quantità di utilizzo per l'offerta selezionata.
- **Tendenza di overage da Top 50 Clienti**: Le prime 50 offerte con le ore di utilizzo più alte vengono visualizzate in una ***classifica*** e sono classificate in base all'utilizzo più elevato del contatore personalizzato. Selezionare un cliente nella classifica per visualizzare la tendenza all'utilizzo di una dimensione contatore selezionata.
- **Tendenza dell'overage da parte dei principali clienti**: Presenta i percentili dei migliori clienti che contribuiscono alla percentuale di utilizzo complessivo. Il percentile principale del cliente viene visualizzato lungo l'asse X ed è determinato dalla quantità di utilizzo del cliente. Sull'asse Y viene visualizzata la quantità di utilizzo. È possibile visualizzare i dettagli passando il mouse sui punti lungo il grafico a linee.

> [!NOTE]
> I dettagli di utilizzo e tutti i grafici in questa pagina vengono visualizzati per qualsiasi dimensione del contatore selezionata per il filtro di pagina.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale del Centro per i partner, vedere Analisi per il Marketplace commerciale nel Centro per i [partner.](./analytics.md)
- Per grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per la tua offerta, vedi [Dashboard di riepilogo nell'analisi](./summary-dashboard.md)di Commercial Marketplace.
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Orders Dashboard in Commercial Marketplace analytics](./orders-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi](./customer-dashboard.md)di Commercial Marketplace .
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Download Dashboard in Commercial Marketplace analytics](./downloads-dashboard.md).
- Per visualizzare una visualizzazione consolidata dei commenti e suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Valutazioni e recensioni del dashboard nell'analisi](./ratings-reviews.md)di Commercial Marketplace.
- Per le domande frequenti sull'analisi di Commercial Marketplace e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)di Commercial Marketplace .
