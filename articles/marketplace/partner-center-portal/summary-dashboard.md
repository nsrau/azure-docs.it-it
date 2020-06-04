---
title: Dashboard di riepilogo per l'analisi del Centro per i partner nel marketplace commerciale
description: Informazioni su come accedere a grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace dal dashboard di riepilogo nel Centro per i partner.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: c7f1700cb386a197b33427e2ea615de019284d96
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738320"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard di riepilogo nell'analisi per il marketplace commerciale

Questo articolo fornisce informazioni sul dashboard di riepilogo nel Centro per i partner. Questo dashboard mostra grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per le offerte.

Per accedere al dashboard di riepilogo, aprire il **[dashboard di analisi](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** nel marketplace commerciale.

>[!NOTE]
> Per definizioni dettagliate della terminologia di analisi, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).

## <a name="summary-dashboard"></a>Dashboard Riepilogo

Il dashboard **Riepilogo** presenta una panoramica in base a ogni tipo di offerta. La sezione **Informazioni dettagliate** mostra a colpo d'occhio le informazioni critiche e offre una panoramica generale dell'attività di vendita delle offerte. È possibile visualizzare questi report usando il dashboard **Riepilogo**. Questo articolo esamina in modo più dettagliato ognuno degli elementi seguenti:

- [Intervallo di date](#date-range)
- [Sezione Riepilogo](#summary-section)
- [Clienti per area geografica](#customers-by-geography)
- [Grafici delle tendenze di crescita](#growth-trend-charts)
- [Classifica clienti](#customer-leaderboard)
- [Tendenza conteggio postazioni](#seat-count-trend)
- [Free trials SaaS orders trend](#free-trials-saas-orders-trend) (Tendenza ordini SaaS versioni di valutazione gratuite)

## <a name="elements-of-the-summary-dashboard"></a>Elementi del dashboard di riepilogo

Le sezioni seguenti descrivono come usare il dashboard di riepilogo e come leggere i dati.

### <a name="date-range"></a>Intervallo di date

È possibile selezionare un intervallo di date nell'angolo superiore destro di ogni pagina. Personalizzare l'output dei grafici della pagina **Riepilogo** selezionando un intervallo di date basato sugli ultimi 3, 6 o 12 mesi oppure selezionando un intervallo di date personalizzato con una durata massima di 12 mesi. L'intervallo di date predefinito è di sei mesi.

![Dashboard di analisi del Centro per i partner](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Sezione Riepilogo

La sezione Riepilogo mostra il conteggio di tutti gli ordini creati, i clienti acquisiti e l'utilizzo durante l'intervallo di date selezionato. Il mese corrente parziale viene escluso dal calcolo di queste metriche. Ad esempio: se è stato selezionato un intervallo di tempo di 6 mesi, le ore di utilizzo vengono calcolate per i sei mesi precedenti al mese corrente. Se è selezionato un intervallo di date personalizzato, il valore parziale del mese corrente viene escluso dal calcolo.

![Tendenze di crescita nel dashboard di riepilogo](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Lettura della sezione di riepilogo

- **Ordini**: numero di tutti gli ordini acquistati (esclusi gli ordini annullati) per le offerte pubblicate fino al momento corrente.
- **Clienti**: numero di tutti i clienti che hanno acquistato le offerte e hanno almeno un ordine non annullato.
- **Ore di utilizzo normalizzato**: valore normalizzato delle ore di utilizzo per rendere conto del numero di core di macchine virtuali ([numero di core macchine virtuali] x [ore di utilizzo di base]). Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore di [numero di core macchine virtuali].
- **Ore di utilizzo base**: tempo di esecuzione delle macchine virtuali espresso in ore. Il valore percentuale accanto a **Totale ordini**, **Totale clienti**, **Ore di utilizzo normalizzato**, **Ore di utilizzo base**, **Visite pagina** e **Inviti all'azione** rappresenta la crescita dell'utilizzo per l'intervallo di date selezionato ([utilizzo ultimo mese - utilizzo primo mese]) / utilizzo primo mese). Come descritto in precedenza, una quantità parziale del mese corrente viene esclusa da questa metrica.
- **Tendenze di crescita**: se si passa il puntatore sulle colonne del grafico, i grafici a barre visualizzano il valore per ogni mese.
- **Triangolo verde che punta verso l'alto**: indica una tendenza di crescita positiva.
- **Triangolo rosso che punta verso il basso**: indica una tendenza di crescita negativa rispetto al mese precedente.

### <a name="customers-by-geography"></a>Clienti per area geografica

La mappa termica **Clienti per area geografica** mostra il numero di clienti su una mappa mondiale.

![Clienti per area geografica nel dashboard di riepilogo](./media/summary-customers-by-geography.png)

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile fare zoom avanti su una posizione specifica.
- La mappa termica dispone di una griglia supplementare per visualizzare i dettagli relativi al numero di clienti, al numero di ordini e alle ore di utilizzo normalizzato nella posizione specifica.
- È possibile cercare e selezionare un paese nella griglia per fare zoom avanti sulla posizione nella mappa. Per tornare alla visualizzazione originale, premere il pulsante **Home** nella mappa.
- Un **nuovo** cliente ha acquistato una delle offerte per la prima volta durante il mese nell'intervallo di date selezionato.

### <a name="growth-trend-charts"></a>Grafici delle tendenze di crescita

È possibile visualizzare le tendenze in base alla crescita degli **ordini acquistati** (inclusi gli ordini annullati), dei **clienti acquisiti** (inclusi i clienti persi) e dell'**utilizzo** segnalato, visualizzati mese per mese in base all'intervallo di date selezionato. È possibile analizzare ulteriormente queste tendenze selezionando i collegamenti al di sotto del grafico, che consentono di passare alle rispettive pagine per **Ordine**, **Utilizzo**, **Cliente** o **Informazioni dettagliate sul marketplace**.

I grafici delle tendenze **Visite pagina e inviti all'azione** per l'offerta del marketplace sono visualizzati per Azure Marketplace e AppSource in due schede.

![Grafici delle tendenze Visite pagina e inviti all'azione nel dashboard di riepilogo](./media/summary-page-visits-and-cta.png)

Il grafico **Ordini per offerte** organizza gli ordini in base al nome dell'offerta.

Il grafico a torta **Ordini per canale di vendita** organizza gli ordini (compresi quelli annullati) durante l'intervallo di date selezionato in base al canale di vendita. Il canale di vendita è il tipo di contratto di licenza usato dai clienti per acquistare Azure, ovvero Cloud Solution Provider (CSP), Enterprise, Enterprise tramite rivenditore, GMT e Pagamento a consumo.

I grafici a torta **Utilizzo per offerte** e **Utilizzo per canali di vendita** presentano la suddivisione dell'utilizzo rispettivamente in base alle offerte e ai canali di vendita principali. Il grafico a torta interno rappresenta l'utilizzo di base e quello esterno rappresenta l'utilizzo normalizzato.

I grafici a torta **Ordini per tipo di licenza del marketplace** e **Utilizzo per tipo di licenza del marketplace** mostrano una suddivisione degli ordini e dell'utilizzo in base al tipo di licenza. I tipi di licenza includono:

- **Fatturazione con Azure**: Microsoft si occupa della fatturazione ai clienti per conto dell'utente quando questo sceglie di vendere l'offerta tramite Microsoft con questo tipo di licenza. I tipi di pagamento includono il pagamento a consumo tramite carta di credito o fatturazione aziendale.
- **Bring Your Own License**: Microsoft non addebita ai clienti l'utilizzo con questo tipo di offerta del marketplace. Questo utilizzo è indicato come **Scarica ora (gratuito)** nel marketplace.
- **Gratuito**: Microsoft non addebita ai clienti l'utilizzo con questo tipo di offerta del marketplace. Questo utilizzo è indicato come **Versione di valutazione gratuita** nel marketplace.
- **Microsoft come rivenditore**: rappresenta le offerte vendute da rivenditori Microsoft come parte del **programma Cloud Solution Provider (CSP)** .

### <a name="customer-leaderboard"></a>Classifica clienti

I primi 50 clienti con il numero più elevato di ordini vengono visualizzati in una *classifica*, con un ordinamento in base al numero di ordini e alla percentuale di ordini più elevati.

- Selezionare un cliente per visualizzare i dettagli del profilo, gli ordini organizzati per offerta o gli ordini organizzati in base al canale di prezzi e al tipo di licenza di Azure.
- Il grafico ad anello **Offers by orders** (Offerte per ordini) presenta le prime quattro offerte (per numero di ordini) e le offerte rimanenti raggruppate come "Sospendi tutto".
- Il grafico ad anello **Utilizzo normalizzato per offerte** presenta le prime cinque offerte in base all'utilizzo.

> [!NOTE]
> Le informazioni personali dei clienti vengono visualizzate solo se il cliente ha fornito il consenso. È possibile visualizzare queste informazioni se è stato effettuato l'accesso con un livello di autorizzazioni del ruolo di **Proprietario**. Gli utenti con il ruolo **Collaboratore** non saranno in grado di visualizzare queste informazioni. [Altre informazioni su ruoli utente e autorizzazioni](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Tendenza conteggio postazioni

Il grafico **Ordini per postazione/per sito** presenta la suddivisione di tutti gli ordini acquistati in base al modello di determinazione prezzi. Il grafico **Tendenza conteggio postazioni** presenta il confronto tra postazioni e ordini acquistati per tutte le offerte SaaS (Software as a Service, software come un servizio) per postazione.

### <a name="free-trials-saas-orders-trend"></a>Free trials SaaS orders trend (Tendenza ordini SaaS versioni di valutazione gratuite)

Il grafico **Free trial SaaS orders trend** (Tendenza ordini SaaS versioni di valutazione gratuite) presenta la tendenza degli ordini di offerte SaaS di versioni di valutazione gratuite con un periodo di valutazione di 30 giorni.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel marketplace commerciale del Centro per i partner, vedere [Analisi per il marketplace commerciale nel Centro per i partner](./analytics.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard degli ordini nell'analisi per il marketplace commerciale](./orders-dashboard.md).
- Per le metriche di fatturazione a consumo e di utilizzo delle offerte di macchine virtuali (VM), vedere [Dashboard di utilizzo nell'analisi per il marketplace commerciale](./usage-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi per il marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./downloads-dashboard.md).
- Per una visualizzazione consolidata del feedback dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard di valutazioni e recensioni nell'analisi per il marketplace commerciale](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del marketplace commerciale e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).
