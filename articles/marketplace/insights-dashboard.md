---
title: Dashboard di informazioni dettagliate sul marketplace nell'analisi per il marketplace commerciale
description: Accedi a un riepilogo di analisi Web del Marketplace nel centro per i partner, che ti permette di misurare il coinvolgimento dei clienti in Microsoft AppSource e in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 9089fee7565633b198043efeed929fc7446437d5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458296"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Dashboard di informazioni dettagliate sul marketplace nell'analisi per il marketplace commerciale

Questo articolo illustra il dashboard Informazioni dettagliate sul marketplace disponibile nel Centro per i partner. Questo dashboard Visualizza un riepilogo di analisi Web del Marketplace commerciale che consente agli editori di misurare il coinvolgimento dei clienti per le rispettive pagine dei dettagli del prodotto elencate nei negozi online del Marketplace commerciale: Microsoft AppSource e Azure Marketplace.

Per accedere al dashboard di **Marketplace Insights** in centro per i partner, in Marketplace commerciale selezionare **[analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **informazioni dettagliate sul Marketplace**.

Per le definizioni dettagliate della terminologia di analisi, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./analytics-faq.md).

## <a name="marketplace-insights-dashboard"></a>Dashboard Informazioni dettagliate sul marketplace

Il dashboard di Marketplace Insights offre una panoramica delle prestazioni aziendali di Azure Marketplace e AppSource. Questo dashboard offre una panoramica generale dei seguenti elementi:

- Tendenza delle visite alle pagine
- Tendenza chiamata a azioni
- Visite di pagina e chiamate a azioni sulle offerte, i domini di riferimento e gli ID della campagna
- Informazioni dettagliate sul Marketplace per geografia
- Tabella dei dettagli di Informazioni dettagliate sul marketplace

Il dashboard di Marketplace Insights fornisce i dati di clickstream, che non devono essere correlati con i lead generati nell'endpoint di destinazione principale.

> [!NOTE]
> La latenza massima tra gli utenti che visitano le offerte in Azure Marketplace o AppSource e la creazione di report nel centro per i partner è 48 ore.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Elementi del dashboard di Marketplace Insights

Il dashboard di Marketplace Insights Visualizza i dettagli di telemetria Web per Azure Marketplace e AppSource in due schede separate. Le sezioni seguenti descrivono come usare il dashboard di Marketplace Insights e come leggere i dati.

### <a name="month-range"></a>Intervallo di mesi

È possibile trovare la selezione di un intervallo di mesi nell'angolo superiore destro di ogni pagina. Personalizzare l'output dei grafici della pagina di **Marketplace Insights** selezionando un intervallo di mesi basato sugli ultimi 6 o 12 mesi oppure selezionando un intervallo di mesi personalizzato con una durata massima di 12 mesi. L'intervallo di mesi predefinito (periodo di calcolo) è di sei mesi.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Illustra i filtri per i mesi nel dashboard di Marketplace Insights.":::

> [!NOTE]
> Tutte le metriche nei widget di visualizzazione e nei report di esportazione rispettano il periodo di calcolo selezionato dall'utente.

### <a name="page-visits-trends"></a>Tendenze delle visite alle pagine

Il grafico dei **visitatori** di Marketplace Insights Visualizza un conteggio delle _visite alle pagine_ e dei _visitatori univoci_ per il periodo di calcolo selezionato.

**Visite di pagina**: questo numero rappresenta il numero di sessioni utente distinte nella pagina di presentazione dell'offerta (pagina dei dettagli del prodotto) per un periodo di calcolo selezionato. Gli indicatori di percentuale rosso e verde rappresentano la percentuale di aumento delle visite alle pagine. Il grafico di tendenza rappresenta il numero di visite alle pagine mese per mese.

**Visitatori univoci**: questo numero rappresenta il conteggio dei visitatori distinti durante il periodo di calcolo selezionato per le offerte in Azure Marketplace e AppSource. Un visitatore che ha visitato una o più pagine di dettagli del prodotto verrà conteggiato come un solo visitatore univoco.

[![Illustra il grafico dei visitatori nel dashboard di Marketplace Insights.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Tendenza chiamata a azioni

Questo numero rappresenta il conteggio dei clic del pulsante **di chiamata a azione** completato nella pagina di presentazione dell'offerta (pagina dei dettagli del prodotto). Le _chiamate all'azione_ vengono conteggiate quando gli utenti selezionano i pulsanti **Ottieni ora**, **versione di valutazione gratuita**, **Contatta me** o **test drive** .

[![Viene illustrato il grafico della chiamata a Action nel dashboard di Marketplace Insights.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Visite di pagina e chiamate a azioni sulle offerte, i domini di riferimento e gli ID della campagna

**Domini di riferimento**: la selezione di un dominio di riferimento specifico Mostra la tendenza mensile delle visite alle pagine e le chiamate all'azione sul grafico a destra.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Illustra il grafico del dominio di riferimento nel dashboard di Marketplace Insights.":::

**Offerte**: selezionare un'offerta specifica per visualizzare la tendenza mensile delle visite alle pagine e le chiamate all'azione sul grafico a destra.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Illustra il grafico degli alias dell'offerta nel dashboard di Marketplace Insights.":::

**ID campagna**: selezionando un ID campagna specifico, si dovrebbe essere in grado di comprendere il successo della campagna. Per ogni campagna, dovrebbe essere possibile visualizzare la tendenza mensile delle visite alle pagine e le chiamate all'azione sul grafico a destra.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Illustra il grafico della campagna nel dashboard di Marketplace Insights.":::

### <a name="marketplace-insights-by-geography"></a>Informazioni dettagliate sul Marketplace per geografia

Per il periodo di calcolo selezionato, il mappa termica Visualizza il numero di visite di pagina, i visitatori univoci e le chiamate a Action (CTA). Il colore da chiaro a scuro sulla mappa rappresenta il valore minimo e massimo dei visitatori univoci. Selezionare un record nella tabella per eseguire lo zoom avanti su un paese/area geografica.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Illustra il grafico spread geografico nel dashboard di Marketplace Insights.":::

Tenere presente quanto segue:

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile fare zoom avanti su una posizione specifica.
- Mappa termica dispone di una griglia supplementare per visualizzare i dettagli relativi al numero di clienti, al numero di ordini e alle ore di utilizzo normalizzato nella posizione specifica.
- È possibile cercare e selezionare un paese nella griglia per fare zoom avanti sulla posizione nella mappa. Ripristinare la visualizzazione originale selezionando il pulsante **Home** nella mappa.

### <a name="marketplace-insights-details-table"></a>Tabella dei dettagli di Informazioni dettagliate sul marketplace

Questa tabella fornisce una visualizzazione elenco delle visite alle pagine e delle chiamate all'azione delle pagine delle offerte selezionate ordinate in base alla data.

- I dati possono essere estratti in un oggetto. TSV o. File CSV se il numero di record è inferiore a 1.000.
- Se il numero di record è superiore a 1.000, i dati esportati verranno posizionati in modo asincrono in una pagina di download per i successivi 30 giorni.
- Filtrare i dati in base ai nomi delle offerte e ai nomi delle campagne per visualizzare i dati a cui si è interessati.

> [!TIP]
> È possibile usare l'icona di download nell'angolo in alto a destra di qualsiasi widget per scaricare i dati. È possibile inviare commenti e suggerimenti su ognuno dei widget facendo clic sull'icona "thumbs up" o "thumbs down".

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale, vedere [accedere ai report analitici per il Marketplace commerciale nel centro per i partner](./partner-center-portal/analytics.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard Orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Per la macchina virtuale (VM) offre metriche di fatturazione di utilizzo e a consumo, vedere [dashboard di utilizzo in analisi del Marketplace commerciale](./usage-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi per il marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./partner-center-portal/downloads-dashboard.md).
- Per una visualizzazione consolidata dei suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere la pagina relativa alle [classificazioni & il dashboard di analisi nel centro per i partner](./partner-center-portal/ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./analytics-faq.md).