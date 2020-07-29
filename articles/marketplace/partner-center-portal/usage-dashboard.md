---
title: Dashboard di utilizzo in Microsoft Commercial Marketplace Analytics, Azure Marketplace e Microsoft AppSource
description: Informazioni su come accedere alle metriche di fatturazione a consumo e di utilizzo di tutte le offerte di macchine virtuali. Passare al dashboard di utilizzo nel Centro per i partner nel marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: c6964a28956230dabad007d60d84dcdd1cffab23
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319916"
---
# <a name="usage-dashboard-in-microsoft-commercial-marketplace-analytics"></a>Dashboard di utilizzo nell'analisi per il marketplace commerciale Microsoft

Questo articolo fornisce informazioni sul dashboard di utilizzo nel Centro per i partner. Questo dashboard mostra le metriche di fatturazione a consumo e di utilizzo di tutte le offerte di macchine virtuali in due schede separate: Utilizzo macchina virtuale e Utilizzo di fatturazione a consumo.

Per accedere al dashboard di utilizzo, aprire il dashboard di **[analisi](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** nel **marketplace commerciale**.

>[!NOTE]
> Per definizioni dettagliate della terminologia di analisi, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).

## <a name="usage-dashboard"></a>Dashboard Utilizzo

Il dashboard di utilizzo rappresenta le metriche per l'utilizzo di tutte le offerte di macchine virtuali e l'utilizzo di fatturazione a consumo. Queste metriche si trovano in due schede separate: Utilizzo macchina virtuale e Utilizzo di fatturazione a consumo.

Nella scheda Utilizzo macchina virtuale sono presenti le rappresentazioni grafiche degli elementi seguenti:

- [Riepilogo dell'utilizzo](#usage-summary)
- [Utilizzo per area geografica](#usage-by-geography)
- [Utilizzo per offerte](#usage-by-offers)
- [Tendenza di utilizzo per offerte e piani](#usage-trend-by-offers-and-plans)
- [Utilizzo per tipo di offerta](#usage-by-offer-type)
- [Utilizzo per dimensioni di macchina virtuale](#usage-by-vm-size)
- [Utilizzo per canale di vendita](#usage-by-sales-channel)
- [Dati dettagliati di utilizzo](#detailed-usage-data)

La latenza massima tra la generazione di eventi di utilizzo e la creazione di report nel centro per i partner è 48 ore

### <a name="usage-summary"></a>Riepilogo utilizzo

La tabella Riepilogo utilizzo visualizza le ore di utilizzo dei clienti per tutte le offerte acquistate.

- Le ore di utilizzo normalizzate sono definite come ore di utilizzo normalizzate per contabilizzare il numero di core della macchina virtuale: [numero di core della macchina virtuale] x [ore di utilizzo di base]. Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore di [numero di core della macchina virtuale].
- Le ore di utilizzo di base sono definite come numero di volte in cui le macchine virtuali sono state in esecuzione in termini di ore.
- Il valore percentuale rappresenta una modifica nella crescita dell'utilizzo per l'intervallo di date selezionato: [utilizzo dell'ultimo mese - utilizzo del primo mese]/utilizzo del primo mese.
- I triangoli verdi rivolti verso l'alto indicano una modifica nella crescita.
- Il triangolo rosso rivolto verso il basso indica una modifica di crescita negativa rispetto al mese precedente.
- I micrografici a barre rappresentano i valori mensili. È possibile visualizzare il valore per ogni mese passando il puntatore sulle colonne all'interno del grafico.

### <a name="usage-by-geography"></a>Utilizzo per area geografica

La mappa termica **di utilizzo normalizzato per area geografica** consente di visualizzare le ore di utilizzo mappate in base al paese del cliente. La variazione del colore del paese o dell'area geografica rappresenta la concentrazione di utilizzo normalizzato. Per tornare alla visualizzazione originale, premere il pulsante **Home** nella mappa.

### <a name="usage-by-offers"></a>Utilizzo per offerte

- Il grafico a torta **Utilizzo normalizzato per offerte** visualizza una suddivisione delle ore di utilizzo normalizzato per offerte in base all'intervallo di date selezionato. Le prime cinque offerte vengono visualizzate nel grafico, le altre invece vengono raggruppate nella categoria **Sospendi tutto**.
- Il grafico a barre rappresenta una tendenza di crescita mensile per l'intervallo di date selezionato. Le colonne dei mesi rappresentano le ore di utilizzo dalle offerte con il numero più elevato di ore di utilizzo per il mese corrispondente. Il grafico a linee rappresenta la tendenza percentuale di crescita tracciata sull'asse Y secondario.
- Utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere da destra a sinistra lungo l'asse X e/o esaminare punti dati specifici.

### <a name="usage-trend-by-offers-and-plans"></a>Tendenza di utilizzo per offerte e piani

Questo grafico mostra la tendenza dell'utilizzo normalizzato per i piani selezionati, chiamati in precedenza SKU, di un'offerta. Il tabellone punteggi delle offerte visualizza le prime 50 offerte con l'utilizzo più elevato, ordinate in base alle ore di utilizzo. Nella classifica del piano vengono visualizzati i primi 50 piani con l'utilizzo più elevato per l'offerta selezionata.

### <a name="usage-by-offer-type"></a>Utilizzo per tipo di offerta

- Il grafico a torta dell'**utilizzo per tipo di offerta** organizza l'utilizzo in base al tipo di offerta.
- Le offerte principali vengono visualizzate nel grafico, mentre le altre vengono raggruppate come "Sospendi tutto".
- Il grafico **Tendenza** visualizza le tendenze di crescita mensili. La colonna Mese rappresenta l'utilizzo in base ai principali tipi di offerte del mese.

### <a name="usage-by-vm-size"></a>Utilizzo per dimensioni di macchina virtuale

Questo grafico rappresenta la tendenza di utilizzo per le dimensioni di VM selezionate (max cinque) di tutte le offerte/piani. L'istogramma è in pila con le ore di utilizzo delle dimensioni delle VM selezionate.

Il tabellone punteggi visualizza le prime 50 dimensioni di macchina virtuale con l'utilizzo più elevato e ordinate in base alle ore di utilizzo.

### <a name="usage-by-sales-channel"></a>Utilizzo per canale di vendita

- Il grafico a torta dei canali di vendita organizza l'utilizzo in base al canale di vendita
- I primi canali di vendita con l'utilizzo più elevato vengono visualizzati nel grafico, mentre gli altri vengono raggruppati come "Sospendi tutto".
- La colonna Mese rappresenta l'utilizzo in base al primo canale di vendita del mese.
- Le funzionalità di questo grafico corrispondono a quelle del grafico "Utilizzo per offerte".

### <a name="detailed-usage-data"></a>Dati dettagliati di utilizzo

La **tabella Dettagli di utilizzo** visualizza un elenco numerato dei primi 1000 record di utilizzo ordinati in base all'utilizzo.

- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file CSV se il numero dei record è inferiore a 1000.
- Se il numero dei record è superiore a 1000, i dati esportati verranno inseriti in modo asincrono in una pagina di download che sarà disponibile per i 30 giorni successivi.
- Applicare filtri ai **dati di utilizzo dettagliati** per visualizzare solo i dati a cui si è interessati. Filtrare i dati per paese/area geografica, canale di vendita, tipo di licenza Marketplace, tipo di utilizzo, nome dell'offerta, tipo di offerta, versioni di valutazione gratuite, ID sottoscrizione del Marketplace, ID cliente e nome della società.

> [!NOTE]
> Selezionare il **tipo di utilizzo** nel filtro della pagina per visualizzare i grafici nella pagina in "visualizzazione normalizzata" o in "visualizzazione non elaborata". La visualizzazione predefinita per questi grafici è quella normalizzata.

I **filtri della pagina di utilizzo** vengono applicati a livello di pagina. È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si sceglie di visualizzare e i dati da visualizzare nella griglia/esportazione dei dati di utilizzo dettagliati. I filtri vengono applicati ai dati estratti per l'intervallo di dati selezionato nell'angolo in alto destro della pagina degli ordini.

- I **tipi di offerte** e i **nomi delle offerte** vengono elencati solo per le offerte acquistate durante l'intervallo di date selezionato. I nomi delle offerte nell'elenco vengono visualizzati per i tipi di offerte selezionati nell'elenco.
- La selezione predefinita è "Tutti" per ogni opzione dei filtri, tranne che per **Tipo di utilizzo**. La selezione predefinita per **Tipo di utilizzo** è Utilizzo normalizzato. Per visualizzare l'utilizzo di base nei grafici, selezionare "Utilizzo base".
- I filtri applicati mostrano la selezione del conteggio per le selezioni di filtro effettuate. I filtri applicati non vengono visualizzati per le selezioni predefinite.

> [!NOTE]
> Una definizione dettagliata di ogni campo della griglia "detailed order data" (Dati ordine dettagliati), dei filtri di pagina e di tutte le possibili selezioni è disponibile nella sezione relativa al dizionario dei dati nell'articolo [Domande frequenti e terminologia](link needed).

La scheda **Utilizzo di fatturazione a consumo** contiene informazioni sull'utilizzo per i tipi di offerte quando l'utilizzo è misurato in base alla dimensione del contatore. Attualmente è visualizzata l'eccedenza del tipo di offerta SaaS. La scheda presenta le rappresentazioni grafiche delle tendenze di eccedenza per l'utilizzo di fatturazione a consumo SaaS:

- **Overage trend by meter dimension** (Tendenza eccedenza per dimensione del contatore): visualizza la tendenza dell'eccedenza mensile per la dimensione del contatore selezionata di un'offerta. L'asse X rappresenta il mese e l'asse Y rappresenta la quantità di utilizzo. Anche l'unità di misura del contatore personalizzato viene visualizzata sull'asse Y.
- **Tendenza in eccedenza per piano**: rappresenta la tendenza della quantità di utilizzo della dimensione del contatore selezionata per piani. I piani visualizzati rappresenteranno i primi cinque piani con la massima quantità di utilizzo per l'offerta selezionata.
- **Tendenza eccedenza per i primi 50 clienti**: le prime 50 offerte con il maggior numero di ore di utilizzo vengono visualizzate in una ***classifica*** e classificate in base al maggior utilizzo del contatore personalizzato. Selezionare un cliente nella classifica per visualizzare la tendenza di utilizzo di una dimensione del contatore selezionata.
- **Tendenza eccedenza per clienti principali**: presenta i percentili dei clienti principali che contribuiscono alla percentuale di utilizzo complessivo. Il percentile del cliente principale viene visualizzato lungo l'asse X ed è determinato dalla quantità di utilizzo del cliente. L'asse Y Visualizza la quantità di utilizzo. È possibile visualizzare i dettagli posizionando il puntatore del mouse sui punti lungo il grafico a linee.

> [!NOTE]
> I dettagli di utilizzo e tutti i grafici di questa pagina vengono visualizzati per qualsiasi dimensione del contatore selezionata per il filtro di pagina.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel marketplace commerciale del Centro per i partner, vedere [Analisi per il marketplace commerciale nel Centro per i partner](./analytics.md).
- Per i grafici, le tendenze e i valori dei dati aggregati che riassumono l'attività del marketplace per l'offerta, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard degli ordini nell'analisi per il marketplace commerciale](./orders-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi per il marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./downloads-dashboard.md).
- Per una visualizzazione consolidata del feedback dei clienti per le offerte in Microsoft AppSource e Azure Marketplace, vedere [Dashboard di valutazioni e recensioni nell'analisi per il marketplace commerciale](./ratings-reviews.md).
- Per le domande frequenti sull'analisi del marketplace commerciale e per un dizionario completo dei termini relativi ai dati, vedere [Domande frequenti e terminologia per l'analisi nel marketplace commerciale](./faq-terminology.md).
