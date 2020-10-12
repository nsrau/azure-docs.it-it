---
title: Dashboard del cliente in Microsoft Commercial Marketplace Analytics nel centro per i partner, Azure Marketplace e Microsoft AppSource
description: Informazioni su come accedere ai dettagli relativi ai clienti, incluse le tendenze di crescita, usando il dashboard dei clienti nell'analisi per il marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: 44e992bb1445e1e58f42ec150a8e2c0682eea98b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87317587"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Dashboard dei clienti nell'analisi per il marketplace commerciale

Questo articolo contiene informazioni sul **dashboard dei clienti** nel Centro per i partner. Il dashboard visualizza le informazioni sui clienti, tra cui le tendenze di crescita, presentate in un formato grafico e scaricabile.

Per accedere al **dashboard dei clienti**, aprire il dashboard di **[analisi](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** nel marketplace commerciale.

>[!NOTE]
> Per definizioni dettagliate della terminologia di analisi, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).

## <a name="customer-dashboard"></a>Dashboard Cliente

Il **dashboard dei clienti** del menu **Analisi** visualizza i dati relativi ai clienti che hanno acquistato le offerte. Si possono visualizzare rappresentazioni grafiche degli elementi seguenti:

- [Riepilogo clienti](#customer-summary)
- [Clienti per area geografica](#customer-by-geography)
- [Tendenze per clienti](#customer-trends)
- [Clienti per ordini e utilizzo](#customers-by-orders-and-usage)
- [Clienti per piani](#customers-by-plans)
- [Ordini e utilizzo per tipo di cliente](#orders-and-usage-by-customer-type)
- [Tabella Dettagli cliente](#customer-details-table)
- [Filtri della pagina Clienti](#customer-page-filters)

La latenza massima tra l'acquisizione dei clienti e la creazione di report nel centro per i partner è 48 ore.

### <a name="customer-summary"></a>Riepilogo clienti

Nella sezione Riepilogo clienti è visualizzato il conteggio di tutti i clienti, inclusi quelli nuovi, esistenti e inattivi, durante l'intervallo di date selezionato.

- Il totale dei clienti viene definito come il numero di tutti i clienti che hanno acquistato l'offerta e hanno almeno un ordine che non è stato annullato.
- La percentuale di crescita del cliente rispetto al mese precedente è indicata dal numero e dall'indicatore verso l'alto in verde o verso il basso in rosso.
- Le tendenze di crescita sono rappresentate da grafici a barre e visualizzano il valore per ogni mese se si passa il mouse sulle colonne del grafico.

Sono disponibili tre **tipi di clienti**: nuovo, esistente e inattivo.

- Un nuovo cliente ha acquistato una o più offerte per la prima volta durante il mese selezionato.
- Un cliente esistente ha acquistato una o più offerte prima del mese selezionato.
- Un cliente inattivo ha annullato tutte le offerte acquistate in precedenza.

### <a name="customer-by-geography"></a>Clienti per area geografica

Il grafico **Customer by geography** Mostra i conteggi di tutti i clienti e i clienti acquisiti durante l'intervallo di date selezionato, mappato in base al paese del cliente/area geografica. Il colore diventa da chiaro a scuro sulla mappa per rappresentare il passaggio del numero di clienti da un valore basso a uno alto. Selezionare un record nella tabella per eseguire lo zoom avanti su un paese/area geografica.

La mappa termica Visualizza il numero di clienti e la percentuale in base al paese del cliente. È possibile spostare la mappa per visualizzare la posizione esatta e fare zoom avanti su una posizione specifica. Questa mappa ha una griglia supplementare che consente di visualizzare la percentuale di clienti in base alla posizione, nonché i clienti appena aggiunti a tale posizione.

### <a name="customer-trends"></a>Tendenze per clienti

Il grafico **Tendenze per clienti** visualizza il conteggio di tutti i clienti, inclusi quelli nuovi, esistenti e inattivi, con la tendenza di crescita mese per mese.

- Il grafico a linee rappresenta le percentuali di crescita complessive dei clienti.
- La colonna del mese rappresenta il conteggio dei clienti in pila in base ai clienti nuovi, esistenti e inattivi.
- Il numero di clienti inattivi viene visualizzato sulla direzione negativa dell'asse Y.
- È possibile selezionare elementi specifici della legenda per ottenere viste più dettagliate. Ad esempio, selezionare nuovi clienti dalla legenda per visualizzare solo i nuovi clienti.
- È possibile usare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra e a sinistra sull'asse X e concentrarsi su punti dati specifici da visualizzare più in dettaglio.
- Se si passa il mouse su una colonna del grafico, vengono visualizzati i dettagli solo per quel mese.

### <a name="customers-by-orders-and-usage"></a>Clienti per ordini e utilizzo

Il grafico dei **clienti raggruppati in base agli ordini e all'utilizzo** include tre schede, una per gli ordini, una per l'utilizzo normalizzato e una per l'utilizzo base. Il **percentile dei primi clienti** viene visualizzato lungo l'asse X ed è determinato dal numero di ordini dei clienti. L'asse Y visualizza il numero di ordini del cliente. L'asse Y secondario (grafico a linee) visualizza la percentuale cumulativa del numero totale di ordini. È possibile visualizzare i dettagli posizionando il puntatore del mouse sui punti lungo il grafico a linee.

Per un esempio, vedere il grafico seguente per l'utilizzo normalizzato: Il 30° percentile superiore di clienti contribuisce all'87% dell'utilizzo normalizzato in modo cumulativo. Il 30° percentile di clienti contribuisce solo a 1,57 milioni di ore di utilizzo.

### <a name="customers-by-plans"></a>Clienti per piani

I grafici **Customers by plans/Usage** sono descritti di seguito.

1. La classifica presenta i dettagli dei primi 50 clienti classificati in base al numero di ordini. Dopo aver selezionato un cliente, i dettagli del cliente vengono presentati nelle sezioni 2, 3 e 4 della classifica.
2. I dettagli del profilo cliente vengono visualizzati in questo spazio quando gli autori sono connessi con ruolo di proprietario. Se gli autori sono connessi con un ruolo di collaboratore, i dettagli in questa sezione non saranno disponibili.
3. Il grafico ad anello **Orders by plans** Visualizza la suddivisione degli ordini acquistati per i piani. Vengono visualizzati i primi cinque piani con il numero massimo di ordini, mentre gli altri ordini sono raggruppati in "Rest all".
4. Il grafico ad anello **seats by plans** Visualizza la suddivisione dei seggi ordinati per i piani. Vengono visualizzati i primi cinque piani con le postazioni più elevate, mentre gli altri ordini sono raggruppati in rest all.

### <a name="orders-and-usage-by-customer-type"></a>Ordini e utilizzo per tipo di cliente

Nel grafico relativo agli **ordini e all'utilizzo suddivisi per tipo di cliente** sono visualizzati il numero di ordini, l'utilizzo normalizzato e le ore di utilizzo base divisi tra nuovi clienti e clienti esistenti, in base alla selezione rispettivamente di ordini, utilizzo normalizzato e utilizzo base nel grafico.

- Un nuovo cliente ha acquistato una o più offerte o ha segnalato l'utilizzo per la prima volta durante lo stesso mese di calendario (asse Y).
- Un cliente esistente ha precedentemente acquistato un'offerta o ha segnalato l'utilizzo prima del mese di calendario indicato (sull'asse Y).

### <a name="customer-details-table"></a>Tabella Dettagli cliente

La tabella **Dettagli cliente** contiene un elenco numerato dei primi 1000 clienti ordinati in base alla data in cui hanno acquistato per la prima volta una delle offerte.

- Le informazioni personali dei clienti sono disponibili solo se il cliente ha dato il suo consenso. È possibile visualizzare queste informazioni solo se è stato effettuato l'accesso con un livello di autorizzazioni del ruolo di proprietario. Altre informazioni su ruoli utente e autorizzazioni.
- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file TSV se il numero dei record è inferiore a 1000.
- Se il numero dei record è superiore a 1000, i dati esportati verranno inseriti in modo asincrono in una pagina di download nei 30 giorni successivi.
- Applicare filtri alla tabella per visualizzare solo i dati a cui si è interessati. Filtrare i dati in base a nome società, ID cliente, ID sottoscrizione Marketplace, tipo di licenza di Azure, data di acquisizione, data di perdita, posta elettronica del cliente, paese del cliente/Regione/stato/città/CAP, lingua del cliente e così via.
- Quando un'offerta viene acquistata da un cliente protetto, le informazioni presenti nei **dati dettagliati del cliente** vengono mascherate (************).
- I dettagli della dimensione del cliente, ad esempio il nome della società, il nome del cliente e l'indirizzo di posta elettronica del cliente, sono a livello di ID organizzazione, non a livello di transazione di Azure Marketplace o AppSource.

### <a name="customer-page-filters"></a>Filtri della pagina Clienti

I filtri della **pagina Clienti** vengono applicati a livello di pagina Clienti. È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si sceglie di visualizzare e i dati da visualizzare nella griglia/esportazione dei dati dettagliati degli ordini. I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato in alto a destra nella pagina degli ordini.

>[!NOTE]
> Le definizioni dettagliate per ognuno dei campi nella griglia Customer, i filtri di pagina e le selezioni possibili sono disponibili nell'articolo di domande frequenti riportato di seguito.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel marketplace commerciale del Centro per i partner, vedere [Analisi per il marketplace commerciale nel Centro per i partner](./analytics.md).
- Per i grafici, le tendenze e i valori dei dati aggregati che riassumono l'attività del marketplace per l'offerta, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard degli ordini nell'analisi per il marketplace commerciale](./orders-dashboard.md).
- Per le metriche di fatturazione a consumo e di utilizzo delle offerte di macchine virtuali (VM), vedere [Dashboard di utilizzo nell'analisi per il marketplace commerciale](./usage-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./downloads-dashboard.md).
- Per una visualizzazione consolidata del feedback dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard di valutazioni e recensioni nell'analisi per il marketplace commerciale](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del marketplace commerciale e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).
