---
title: Dashboard dei clienti nell'analisi per il marketplace commerciale Microsoft nel Centro per i partner
description: Informazioni su come accedere ai dettagli relativi ai clienti, incluse le tendenze di crescita, usando il dashboard dei clienti nell'analisi per il marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 9f71a668b3473521e9d8d7c9318ab2e557380845
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121759"
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
- [Clienti per SKU](#customers-by-skus)
- [Ordini e utilizzo per tipo di cliente](#orders-and-usage-by-customer-type)
- [Tabella Dettagli cliente](#customer-details-table)
- [Filtri della pagina Clienti](#customer-page-filters)

### <a name="customer-summary"></a>Riepilogo clienti

Nella sezione Riepilogo clienti è visualizzato il conteggio di tutti i clienti, inclusi quelli nuovi, esistenti e inattivi, durante l'intervallo di date selezionato.

- Il totale dei clienti viene definito come il numero di tutti i clienti che hanno acquistato l'offerta e hanno almeno un ordine che non è stato annullato.
- La percentuale di crescita dei clienti rispetto al mese precedente è indicata dal numero e dall'indicatore verso l'alto in verde o verso il basso in rosso.
- Le tendenze di crescita sono rappresentate da grafici a barre e visualizzano il valore per ogni mese se si passa il mouse sulle colonne del grafico.

Sono disponibili tre **tipi di clienti**: nuovo, esistente e inattivo.

- Un nuovo cliente ha acquistato una o più offerte per la prima volta durante il mese selezionato.
- Un cliente esistente ha acquistato una o più offerte prima del mese selezionato.
- Un cliente inattivo ha annullato tutte le offerte acquistate in precedenza.

### <a name="customer-by-geography"></a>Clienti per area geografica

Nel grafico dei **clienti per area geografica** sono riportati i conteggi di tutti i clienti e dei clienti acquisiti durante l'intervallo di date selezionato, mappati in base al paese o all'area del cliente. Il colore diventa da chiaro a scuro sulla mappa per rappresentare il passaggio del numero di clienti da un valore basso a uno alto. Fare clic su un record nella tabella per fare zoom avanti su un paese o un'area geografica.

La mappa termica visualizza il numero di clienti e la percentuale per paese o area del cliente. È possibile spostare la mappa per visualizzare la posizione esatta e fare zoom avanti su una posizione specifica. Questa mappa ha una griglia supplementare che consente di visualizzare la percentuale di clienti in base alla posizione, nonché i clienti appena aggiunti a tale posizione.

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

### <a name="customers-by-skus"></a>Clienti per SKU

I grafici che rappresentano i **clienti in base agli SKU o all'utilizzo** sono descritti di seguito.

1. La classifica presenta i dettagli dei primi 50 clienti classificati in base al numero di ordini. Dopo aver selezionato un cliente, i dettagli del cliente vengono presentati nelle sezioni 2, 3 e 4 della classifica.
2. I dettagli del profilo cliente vengono visualizzati in questo spazio quando gli autori sono connessi con ruolo di proprietario. Se gli autori sono connessi con un ruolo di collaboratore, i dettagli in questa sezione non saranno disponibili.
3. Il grafico ad anello Ordini per SKU visualizza la suddivisione degli ordini acquistati per gli SKU. Vengono visualizzati i primi 5 SKU con il numero massimo di ordini, mentre gli altri ordini sono raggruppati in "Sospendi tutto".
4. Il grafico ad anello Postazioni per SKU visualizza la suddivisione delle postazioni ordinate per gli SKU. Vengono visualizzati i primi 5 SKU con il numero massimo di postazioni, mentre gli altri ordini sono raggruppati in "Sospendi tutto".

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
- Si possono applicare filtri alla tabella per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati per nome della società, ID cliente, ID sottoscrizione del Marketplace, tipo di licenza di Azure, data di acquisizione, data di perdita, indirizzo di posta elettronica del cliente, paese/area/stato/città/CAP del cliente, lingua del cliente e così via.
- Quando un'offerta viene acquistata da un cliente protetto, le informazioni presenti nei **dati dettagliati del cliente** vengono mascherate (************).
- I dettagli della dimensione del cliente, ad esempio il nome della società, il nome del cliente e l'indirizzo di posta elettronica del cliente, sono a livello di ID organizzazione, non a livello di transazione di Azure Marketplace o AppSource.

### <a name="customer-page-filters"></a>Filtri della pagina Clienti

I filtri della **pagina Clienti** vengono applicati a livello di pagina Clienti. È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si sceglie di visualizzare e i dati da visualizzare nella griglia/esportazione dei dati dettagliati degli ordini. I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato in alto a destra nella pagina degli ordini.

>[!NOTE]
> Le definizioni dettagliate di tutti i campi della griglia Clienti, dei filtri di pagina e delle possibili selezioni sono contenute in [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel marketplace commerciale del Centro per i partner, vedere [Analisi per il marketplace commerciale nel Centro per i partner](./analytics.md).
- Per grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per le offerte, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard degli ordini nell'analisi per il marketplace commerciale](./orders-dashboard.md).
- Per le metriche di fatturazione a consumo e di utilizzo delle offerte di macchine virtuali (VM), vedere [Dashboard di utilizzo nell'analisi per il marketplace commerciale](./usage-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./downloads-dashboard.md).
- Per una visualizzazione consolidata del feedback dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard di valutazioni e recensioni nell'analisi per il marketplace commerciale](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del marketplace commerciale e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).
