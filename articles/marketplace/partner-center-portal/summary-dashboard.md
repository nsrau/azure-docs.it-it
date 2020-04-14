---
title: Dashboard di riepilogo per l'analisi del Centro per i partner in Commercial Marketplace
description: Informazioni su come accedere a grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace dal dashboard Riepilogo nel Centro per i partner.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 86bce85c6ec273b4ab5f9f00cbae68fc054f53f5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262401"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard di riepilogo nell'analisi del mercato commerciale

In questo articolo vengono fornite informazioni sul dashboard Riepilogo nel Centro per i partner. Questo dashboard visualizza grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per le tue offerte.

Per accedere al dashboard Riepilogo, aprire il **[dashboard Analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Mercato commerciale.

>[!NOTE]
> Per le definizioni dettagliate della terminologia dell'analisi, vedere [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)del mercato commerciale.

## <a name="summary-dashboard"></a>Dashboard Riepilogo

Il dashboard **Riepilogo** presenta una panoramica basata su ogni tipo di offerta. **Le informazioni dettagliate** mostrano informazioni critiche a colpo d'occhio e forniscono un'ampia vista dell'attività di vendita delle offerte. È possibile visualizzare questi report utilizzando il dashboard **Riepilogo.** Questo articolo illustra ulteriori dettagli su ciascuno dei seguenti elementi:

- [Intervallo di date](#date-range)
- [Sezione Riepilogo](#summary-section)
- [Clienti per area geografica](#customers-by-geography)
- [Grafici delle tendenze di crescita](#growth-trend-charts)
- [Classifica clienti](#customer-leaderboard)
- [Tendenza del conteggio dei posti](#seat-count-trend)
- [Prove gratuite SaaS ordini di tendenza](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementi del dashboard Riepilogo

Nelle sezioni seguenti viene descritto come utilizzare il dashboard di riepilogo e come leggere i dati.

### <a name="date-range"></a>Intervallo di date

Puoi trovare una selezione di intervallo di date nell'angolo in alto a destra di ogni pagina. Personalizzare l'output dei grafici della pagina **Riepilogo** selezionando un intervallo di date basato sugli ultimi 3, 6 o 12 mesi oppure selezionando un intervallo di date personalizzato con una durata massima di 12 mesi. L'intervallo di date predefinito è di sei mesi.

![Dashboard di analisi del Centro per i partner](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Sezione Riepilogo

Nella sezione Riepilogo viene visualizzato il conteggio di tutti gli ordini creati, i clienti acquisiti e l'utilizzo riportato durante l'intervallo di date selezionato. Il mese corrente parziale verrà escluso dal calcolo di queste metriche. Ad esempio: se è stato selezionato l'intervallo di tempo di 6M, le ore di utilizzo vengono calcolate per i sei mesi precedenti al mese corrente. Se viene selezionato un intervallo di date personalizzato, un importo parziale del mese corrente verrà escluso dal calcolo.

![Tendenze di crescita nel dashboard Riepilogo](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Lettura della sezione di riepilogo

- **Ordini**: Conteggio di tutti gli ordini acquistati (esclusi gli ordini annullati) per le offerte che hai pubblicato finora.
- **Clienti**: Contare tutti i clienti che hanno acquistato le vostre offerte e hanno almeno un ordine non annullato.
- **Ore di utilizzo normalizzate**: definite come le ore di utilizzo normalizzate per tenere conto del numero di core di macchine virtuali ([numero di core di macchine virtuali] x [ore di utilizzo non elaborato]). Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore [numero di core di VM].
- **Ore**di utilizzo non elaborate: quantità di tempo per cui le macchine virtuali sono state eseguite in termini di ore. Il valore percentuale accanto agli **ordini totali**, **ai clienti totali,** alle ore di **utilizzo normalizzate,** alle ore di **utilizzo non elaborate,** alle visite alle **pagine**e alla chiamata **alle azioni** rappresenta la quantità di crescita dell'utilizzo per l'intervallo di date selezionato ([utilizzo ultimo mese - utilizzo del primo mese])/utilizzo del primo mese). Come descritto in precedenza, un importo parziale del mese corrente verrà escluso da questa metrica.
- **Tendenze**di crescita : Se si passa il mouse sulle colonne del grafico, i grafici a barre visualizzano il valore per ogni mese.
- **Triangolo verde che punta verso l'alto**: Indica un trend di crescita positivo.
- **Triangolo rosso rivolto verso il basso**: Indica una tendenza di crescita negativa rispetto al mese precedente.

### <a name="customers-by-geography"></a>Clienti per area geografica

La mappa **termica Clienti per area geografica** visualizza il numero di clienti su una mappa del mondo.

![Clienti per area geografica nel dashboard Riepilogo](./media/summary-customers-by-geography.png)

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile ingrandire una posizione specifica.
- La mappa termica dispone di una griglia supplementare per visualizzare i dettagli del conteggio dei clienti, del conteggio degli ordini, delle ore di utilizzo normalizzate nell'ubicazione specifica.
- È possibile cercare e selezionare un paese nella griglia per eseguire lo zoom sulla posizione nella mappa. Ripristinare la visualizzazione originale premendo il pulsante **Home** nella mappa.
- Un **nuovo** cliente ha acquistato una delle tue offerte per la prima volta durante il mese compreso nell'intervallo di date selezionato.

### <a name="growth-trend-charts"></a>Grafici delle tendenze di crescita

È possibile visualizzare le tendenze in base alla crescita degli **ordini acquistati** (inclusi gli ordini annullati), **ai clienti acquisiti** (inclusi i clienti persi) e **all'utilizzo** riportato, che vengono visualizzati mese per mese in base all'intervallo di date selezionato. È possibile analizzare ulteriormente queste tendenze selezionando i collegamenti sotto il grafico, che consentono di passare alle rispettive pagine **order**, **usage**, **customer**o **Marketplace Insights.**

I grafici di tendenza delle pagine dell'offerta Marketplace **e dell'invito all'azione** vengono visualizzati per il marketplace di Azure e AppSource in due schede.

![Visite alle pagine e call ai grafici di tendenza delle azioni nel dashboard Riepilogo](./media/summary-page-visits-and-cta.png)

Il grafico **Ordini per offerte** organizza gli ordini in base al nome dell'offerta.

Il grafico a torta degli **ordini per canale di vendita** organizza gli ordini (inclusi gli ordini che i clienti hanno annullato) durante l'intervallo di date selezionato, in base al canale Vendite. Il canale di vendita è il tipo di contratto di licenza usato dai clienti per acquistare Azure, che sono Cloud Solution Provider (CSP), Enterprise, Enterprise through Reseller, GTM e Pay As You Go.

**L'utilizzo da parte delle offerte** e **l'utilizzo da parte** dei grafici a torta dei canali di vendita presentano la suddivisione dell'utilizzo da parte delle migliori offerte e dei canali di vendita, rispettivamente. Il grafico a torta interno rappresenta l'utilizzo non elaborato e il grafico a torta esterno rappresenta l'utilizzo normalizzato.

Gli **ordini per tipo di licenza marketplace** e i grafici a torta relativi al tipo di licenza **marketplace** visualizzano una suddivisione degli ordini e dell'utilizzo in base al rispettivo tipo di licenza. I tipi di licenza includono:

- **Fatturazione tramite Azure:** Microsoft fattura ai clienti per conto dell'utente quando si sceglie di vendere l'offerta tramite Microsoft con questo tipo di licenza. I tipi di pagamento includono la fatturazione con pagamento in base al costo tramite carta di credito o fatturazione aziendale.
- **Porta la tua licenza**: Microsoft non fattura ai clienti per il loro utilizzo con questo tipo di offerta di mercato. Questo utilizzo è elencato come **Scarica ora (Gratuito)** nel marketplace.
- **Gratis**: Microsoft non fattura ai clienti per il loro utilizzo con questo tipo di offerta di mercato. Questo utilizzo è elencato come **versione di valutazione gratuita** nel marketplace.
- **Microsoft as reseller**: Rappresenta le offerte vendute dai rivenditori Microsoft come parte del **programma Cloud Solution Provider (CSP).**

### <a name="customer-leaderboard"></a>Classifica clienti

I primi 50 clienti con il maggior numero di ordini vengono visualizzati in una *classifica,* ordinati in base al numero di ordini e alla percentuale di ordine più alti.

- Selezionare un cliente per visualizzare i dettagli del profilo, gli ordini organizzati per offerta o gli ordini organizzati per tipo di licenza di Azure e canale dei prezzi.
- Il **grafico Offerte per ordini** ciambella presenta le prime quattro offerte (per numero di ordini) e le offerte rimanenti raggruppate come 'Rest All'.
- **L'utilizzo normalizzato per offerta** grafico ad anello presenta le prime cinque offerte per utilizzo.

> [!NOTE]
> Le informazioni personali del cliente saranno presentate solo se il cliente ha fornito il consenso. È possibile visualizzare queste informazioni se è stato effettuato l'accesso con un livello di autorizzazioni del ruolo **Proprietario.You** can view this information if you have logged in with an Owner role permissions-level. Gli utenti con il ruolo **Collaboratore** non saranno in grado di visualizzare queste informazioni. [Ulteriori informazioni sui ruoli utente e sulle autorizzazioni](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Tendenza del conteggio dei posti

Gli **ordini per posto/ per grafico del sito** presenta la ripartizione di tutti gli ordini acquistati in base al modello di prezzo. Il grafico **di tendenza** del numero di posti presenta i posti rispetto agli ordini acquistati per tutte le offerte offerte di Software as a Service (SaaS) per posto.

### <a name="free-trials-saas-orders-trend"></a>Prove gratuite SaaS ordini di tendenza

Il grafico di **tendenza degli ordini SaaS di prova gratuita** presenta la tendenza degli ordini per le prove gratuite offerte da SaaS con un periodo di prova di 30 giorni.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel marketplace commerciale del Centro per i partner, vedere Analisi per il marketplace commerciale nel Centro per i [partner.](./analytics.md)
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Orders Dashboard in commercial marketplace analytics](./orders-dashboard.md).
- Per la macchina virtuale (VM) offre l'utilizzo e le metriche di fatturazione a consumo, vedere [Dashboard di utilizzo nell'analisi del marketplace commerciale.](./usage-dashboard.md)
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Customer Dashboard in commercial marketplace analytics](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Download Dashboard in commercial marketplace analytics](./downloads-dashboard.md).
- Per visualizzare una visualizzazione consolidata dei commenti e suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Valutazioni e recensioni del dashboard nell'analisi](./ratings-reviews.md)del marketplace commerciale.
- Per le domande frequenti sull'analisi del mercato commerciale e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi](./faq-terminology.md)del mercato commerciale.
