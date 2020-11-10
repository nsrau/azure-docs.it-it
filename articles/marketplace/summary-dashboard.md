---
title: Dashboard di riepilogo per l'analisi del Centro per i partner nel marketplace commerciale
description: Informazioni su come accedere a grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace dal dashboard di riepilogo nel Centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 957c1d8a87e334af421c53b1425d3aec0766f31a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414973"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard di riepilogo nell'analisi per il marketplace commerciale

Questo articolo fornisce informazioni sul dashboard di riepilogo nel Centro per i partner. Questo dashboard mostra grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per le offerte.

Per accedere al dashboard di riepilogo in centro per i partner, in **Marketplace commerciale** selezionare **[analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Riepilogo**.

>[!NOTE]
> Per le definizioni dettagliate della terminologia di analisi, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./partner-center-portal/faq-terminology.md).

## <a name="summary-dashboard"></a>Dashboard Riepilogo

Il dashboard di riepilogo presenta una panoramica di Azure Marketplace e Microsoft AppSource offre prestazioni aziendali. Il dashboard offre una panoramica generale dei seguenti elementi:

- Ordini dei clienti
- Clienti
- Utilizzo delle offerte da parte dei clienti
- Visite alle pagine dei clienti in Azure Marketplace e AppSource

## <a name="elements-of-the-summary-dashboard"></a>Elementi del dashboard di riepilogo

Le sezioni seguenti descrivono come usare il dashboard di riepilogo e come leggere i dati.

### <a name="month-range"></a>Intervallo di mesi

È possibile trovare la selezione di un intervallo di mesi nell'angolo superiore destro di ogni pagina. Personalizzare l'output dei grafici della pagina di **Riepilogo** selezionando un intervallo di mesi basato sugli ultimi 3, 6 o 12 mesi oppure selezionando un intervallo di mesi personalizzato con una durata massima di 12 mesi. L'intervallo di mesi predefinito (periodo di calcolo) è di sei mesi.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Illustra le opzioni di intervallo mensile nel dashboard di riepilogo.":::

> [!NOTE]
> Tutte le metriche nei widget di visualizzazione e nei report di esportazione rispettano il periodo di calcolo selezionato dall'utente.

### <a name="orders-widget"></a>Widget Orders

Il widget Orders del dashboard Summary * * Visualizza gli ordini correnti per tutte le offerte basate su transazioni. Il widget Orders Visualizza un conteggio e una tendenza di tutti gli ordini acquistati (esclusi gli ordini annullati) per il periodo di calcolo selezionato. Gli **ordini** con valore percentuale rappresentano la quantità di crescita durante il periodo di calcolo selezionato.

[![Viene illustrato il widget Orders nel dashboard di riepilogo.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


È anche possibile passare al report Orders selezionando il collegamento **Orders dashboard** nell'angolo inferiore sinistro del widget.

### <a name="customers-widget"></a>Widget Customers

Il widget **Customers** del dashboard di **Riepilogo** Visualizza il numero totale di clienti che hanno acquisito le offerte per il periodo di calcolo selezionato. Il widget Customers Visualizza il conteggio e la tendenza del numero totale di clienti attivi (compresi quelli nuovi ed esistenti), esclusi i clienti con varianza, per il periodo di calcolo selezionato. Il valore percentuale in **clienti** rappresenta la quantità di crescita durante il periodo di calcolo selezionato.

[![Viene illustrato il widget Customers nel dashboard di riepilogo.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

È anche possibile passare al report dettagliato dei clienti selezionando il collegamento **Dashboard Customers** nell'angolo inferiore sinistro del widget.

### <a name="usage-widget"></a>Widget utilizzo

Il widget **Usage** del dashboard di **Riepilogo** rappresenta le ore totali normalizzate e di utilizzo non elaborato per tutte le offerte di macchine virtuali (VM) di Azure. Il widget Usage (utilizzo) Visualizza un conteggio e una tendenza delle ore di utilizzo totali per il periodo di calcolo selezionato.

La tabella Riepilogo utilizzo visualizza le ore di utilizzo dei clienti per tutte le offerte acquistate.

- Le ore di utilizzo normalizzate sono definite come ore di utilizzo normalizzate per contabilizzare il numero di core della macchina virtuale: [numero di core della macchina virtuale] x [ore di utilizzo di base]. Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore di [numero di core della macchina virtuale].
- Le ore di utilizzo non elaborato sono definite come quantità di tempo di esecuzione delle macchine virtuali in termini di ore.

Il valore percentuale al di sotto del totale delle ore di utilizzo rappresenta la quantità di crescita nelle ore di utilizzo durante il periodo di calcolo selezionato.

[![Illustra il widget Usage (utilizzo) nel dashboard di riepilogo.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

È anche possibile passare al report utilizzo selezionando il collegamento **Dashboard utilizzo** nell'angolo inferiore sinistro del widget.

### <a name="marketplace-insights"></a>Informazioni dettagliate sul Marketplace

Marketplace Insights Mostra il numero di utenti che hanno visitato le pagine offerte in Azure Marketplace e AppSource. Il **conteggio delle visite** alle pagine Mostra un riepilogo di analisi Web del Marketplace commerciale che consente agli editori di misurare il coinvolgimento dei clienti nelle rispettive pagine dei dettagli del prodotto elencate nei negozi online del Marketplace commerciale: Microsoft AppSource e Azure Marketplace. Questo widget Visualizza il numero e la tendenza delle visite totali alle pagine durante il periodo di calcolo selezionato.

[![Viene illustrata la pagina visitare il widget count nel dashboard di riepilogo.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

È anche possibile accedere al report di Marketplace Insights selezionando il collegamento **dashboard di Marketplace Insights** nell'angolo inferiore sinistro del widget.

### <a name="geographical-spread"></a>Distribuzione geografica

Per il periodo di calcolo selezionato, il mappa termica Visualizza il numero totale di clienti, ordini e ore di utilizzo normalizzato rispetto alla dimensione Geography.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Illustra il widget paesi distribuiti nel dashboard di riepilogo.":::

Tenere presente quanto segue:

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile fare zoom avanti su una posizione specifica.
- Mappa termica dispone di una griglia supplementare per visualizzare i dettagli relativi al numero di clienti, al numero di ordini e alle ore di utilizzo normalizzato per la posizione specifica.
- È possibile cercare e selezionare un paese nella griglia per fare zoom avanti sulla posizione nella mappa. Ripristinare la visualizzazione originale selezionando il pulsante **Home** nella mappa.

> [!TIP]
> È possibile usare l'icona di download nell'angolo in alto a destra di qualsiasi widget per scaricare i dati. È possibile fornire commenti e suggerimenti su ognuno dei widget selezionando l'icona "pollice su" o "pollice giù".

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale, vedere [accedere ai report analitici per il Marketplace commerciale nel centro per i partner](./partner-center-portal/analytics.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard degli ordini nell'analisi per il marketplace commerciale](orders-dashboard.md).
- Per informazioni sull'utilizzo di offerte con macchine virtuali (VM) e sull'analisi della fatturazione a consumo, vedere [Dashboard di utilizzo nell'analisi per il marketplace commerciale](usage-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi per il marketplace commerciale](customer-dashboard.md).
- Per un elenco delle richieste di download degli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./partner-center-portal/downloads-dashboard.md).
- Per una visualizzazione consolidata dei suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere la pagina relativa alle [classificazioni & il dashboard di analisi nel centro per i partner](./partner-center-portal/ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./partner-center-portal/faq-terminology.md).
