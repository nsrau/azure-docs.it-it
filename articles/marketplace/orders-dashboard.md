---
title: Dashboard degli ordini del centro per i partner in analisi del Marketplace commerciale, Microsoft AppSource e Azure Marketplace
description: Informazioni su come accedere ai report analitici sugli ordini di offerte del Marketplace commerciale in un formato grafico e scaricabile.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: ceecaf88f87a94473011eb48e944db7a011b3acc
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563784"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Dashboard degli ordini nell'analisi per il marketplace commerciale

Questo articolo fornisce informazioni sul Dashboard ordini nel Centro per i partner. Questo dashboard Visualizza le informazioni sugli ordini, incluse le tendenze di crescita, presentate in un formato grafico e scaricabile.

Per accedere al dashboard Orders nel centro per i partner, in **Marketplace commerciale** selezionare **[analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **ordini**.

>[!NOTE]
> Per le definizioni dettagliate della terminologia di analisi, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./partner-center-portal/faq-terminology.md).

## <a name="orders-dashboard"></a>Dashboard Ordini

Il dashboard Orders Visualizza gli ordini correnti per tutte le offerte SaaS (Software as a Service). È possibile visualizzare rappresentazioni grafiche degli elementi seguenti:

- Tendenza ordini
- Ordini per postazione e tendenza del sito
- Ordini per offerte e SKU
- Ordini per area geografica
- Tabella Orders dettagliata
- Filtri pagina ordini

> [!NOTE]
> La latenza massima tra l'acquisizione dei clienti e la creazione di report nel centro per i partner è 48 ore.

## <a name="elements-of-the-orders-dashboard"></a>Elementi del dashboard Orders

Le sezioni seguenti descrivono come usare il dashboard Orders e come leggere i dati.

### <a name="month-range"></a>Intervallo di mesi

È possibile trovare la selezione di un intervallo di mesi nell'angolo superiore destro di ogni pagina. Personalizzare l'output dei grafici della pagina **Orders** selezionando un intervallo di mesi basato sugli ultimi 6 o 12 mesi oppure selezionando un intervallo di mesi personalizzato con una durata massima di 12 mesi. L'intervallo di mesi predefinito (periodo di calcolo) è di sei mesi.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Illustra i filtri per i mesi nel dashboard Orders.":::

> [!NOTE]
> Tutte le metriche nei widget di visualizzazione e nei report di esportazione rispettano il periodo di calcolo selezionato dall'utente.

### <a name="orders-trend"></a>Tendenza ordini

In questa sezione si troverà il grafico **ordini** che mostra la tendenza degli ordini attivi e annullati per il periodo di calcolo selezionato. Le metriche e le tendenze di crescita sono rappresentate da un grafico a linee e visualizzeranno il valore per ogni mese passando il puntatore del mouse sulla linea del grafico. Il valore percentuale sotto la metrica Orders nel widget rappresenta la quantità di crescita o di rifiuto durante il periodo di calcolo selezionato.

Sono presenti due contatori degli ordini: _attivo_ e _annullato_.

- **Attivo** è uguale al numero di ordini attualmente in uso da parte dei clienti durante l'intervallo di date selezionato.
- **Annullato** è uguale al numero di ordini acquistati in precedenza e quindi annullato durante l'intervallo di date selezionato.

[![Viene illustrato il widget Orders nel dashboard Orders che mostra la tendenza degli ordini attivi e annullati.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Ordini per per postazione e tendenza sito

Il grafico a linee **Order by per postazione e basato su sito** rappresenta la metrica e la tendenza degli ordini SaaS per sito SaaS e per postazione acquistati dai clienti (questo grafico include gli ordini annullati).

[![Viene illustrato il widget Orders nel dashboard Orders che Mostra gli ordini per postazione e la tendenza del sito.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

Le offerte SaaS possono usare uno dei due modelli di determinazione prezzi con ogni piano: tariffa fissa (basata sul sito) o per utente (basata su postazione).

- **Tariffa** fissa: consentire l'accesso all'offerta con un singolo prezzo tariffario mensile o annuale. Questo approccio è a volte noto come prezzo basato sul sito.
- **Per utente** : consentire l'accesso all'offerta con un prezzo basato sul numero di utenti che possono accedere all'offerta o occupare le postazioni. Con questo modello basato sull'utilizzo, è possibile impostare il numero minimo e massimo di utenti supportati dal piano. È possibile creare più piani per configurare punti di prezzo diversi in base al numero di utenti. Questi campi sono facoltativi. Se viene lasciato deselezionato, il numero di utenti verrà interpretato come non avente un limite (minimo 1 e massimo di quanti ne può supportare il servizio). Questi campi possono essere modificati come parte di un aggiornamento del piano.
- **Fatturazione a consumo** : oltre ai prezzi forfettari. Con questo modello di determinazione dei prezzi, è possibile definire facoltativamente piani a consumo che usano l'API del servizio di misurazione del Marketplace per addebitare ai clienti l'utilizzo non coperto dalla tariffa fissa.

Per altri dettagli sulla fatturazione basata su sede, sito e misurazione, vedere [How to plan an Saas offer for the Commercial Marketplace](plan-saas-offer.md).

### <a name="orders-by-offers-and-skus"></a>Ordini per offerte e SKU

Il grafico Orders by offers e SKU Mostra le misure e le tendenze di tutte le offerte:

- Le offerte principali vengono visualizzate nel grafico e il resto delle offerte viene raggruppato come **Rest all**.
- È possibile selezionare offerte specifiche nella legenda per visualizzare solo l'offerta e gli SKU associati nel grafico.
- Se si passa il puntatore del mouse su una sezione del grafico, viene visualizzato il numero di ordini e la percentuale di tale offerta rispetto al numero totale di ordini in tutte le offerte.
- La **tendenza degli ordini in base alle offerte** visualizza le tendenze di crescita mensili. La colonna del mese rappresenta il numero di ordini in base al nome dell'offerta. Il grafico a linee visualizza la tendenza percentuale di crescita tracciata sull'asse Z.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="Illustra il grafico Orders by offers nel dashboard Orders.":::

È possibile selezionare qualsiasi offerta e un massimo di tre SKU dell'offerta per visualizzare la tendenza di mese per mese per l'offerta, gli SKU e le postazioni.

### <a name="orders-by-geography"></a>Ordini per area geografica

Per il periodo di calcolo selezionato, il mappa termica Visualizza il numero totale di ordini e la percentuale di crescita degli ordini appena aggiunti rispetto a una geografia.  Il colore diventa da chiaro a scuro sulla mappa per rappresentare il passaggio del numero di clienti da un valore basso a uno alto. Selezionare un record nella tabella per eseguire lo zoom avanti su un paese o una regione specifica.

[![Illustra il grafico spread geografico nel dashboard Orders.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

Tenere presente quanto segue:

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile fare zoom avanti su una posizione specifica.
- Mappa termica dispone di una griglia supplementare per visualizzare i dettagli relativi al numero di clienti, al numero di ordini e alle ore di utilizzo normalizzato per la posizione specifica.
- È possibile cercare e selezionare un paese nella griglia per fare zoom avanti sulla posizione nella mappa. Ripristinare la visualizzazione originale selezionando il pulsante **Home** nella mappa.

### <a name="orders-details-table"></a>Tabella Dettagli ordini

Nella tabella Order Details viene visualizzato un elenco numerato dei 1.000 ordini principali ordinati in base alla data di acquisizione.

- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un oggetto. CSV o. File TSV se il numero di record è inferiore a 1.000.
- Se il numero di record è superiore a 1.000, i dati esportati verranno posizionati in modo asincrono in una pagina di download per i successivi 30 giorni.
- Applicare i filtri alla tabella **Order Details** per visualizzare solo i dati a cui si è interessati. Filtra per paese/area geografica, tipo di licenza di Azure, tipo di licenza per Marketplace commerciale, tipo di offerta, stato dell'ordine, tracce gratuite, ID sottoscrizione del Marketplace commerciale, ID cliente e nome della società.
- Poiché le offerte SaaS acquistate tramite Azure Marketplace o Microsoft AppSource, non richiedono una sottoscrizione di Azure, l'ID sottoscrizione del Marketplace verrà visualizzato nel formato 00000000-0000-0000-0000-000000000000 nella sezione relativa ai **dati degli ordini dettagliati** .
- Quando un ordine viene acquistato da un cliente protetto, le informazioni in **Orders detailed data** vengono mascherate (* * * * * * * * * * * *).

**_Tabella 1: dizionario dei termini di dati_* _

| Nome colonna | Nome attributo | Definizione |
| ------------ | ------------- | ------------- |
| ID sottoscrizione Marketplace | ID sottoscrizione Marketplace | Identificatore univoco associato alla sottoscrizione di Azure usata dal cliente per acquistare l'offerta del Marketplace commerciale. L'ID è stato in precedenza il GUID della sottoscrizione di Azure. |
| MonthStartDate | Data di inizio del mese | Data di inizio del mese rappresenta il mese di acquisto. |
| Tipo di offerta | Tipo di offerta | Tipo di offerta di Marketplace commerciale. |
| Azure License Type | Azure License Type | Tipo di contratto di licenza usato dai clienti per acquistare Azure. È detto anche canale. I valori possibili sono:<ul><li>Provider di soluzioni cloud</li><li>Enterprise</li><li>Enterprise tramite rivenditore</li><li>Con pagamento in base al consumo</li></ul> |
| Marketplace License Type | Marketplace License Type | Metodo di fatturazione dell'offerta del Marketplace commerciale. I diversi valori sono:<ul><li>Fatturazione tramite Azure</li><li>Bring your own License</li><li>Gratuito</li><li>Microsoft As Reseller</li></ul> |
| SKU | SKU | Piano associato all'offerta |
| Customer Country | Paese/Area geografica del cliente | Nome del paese o dell'area geografica specificato dal cliente. Il paese/area geografica può essere diverso da quello del paese nella sottoscrizione di Azure di un cliente. |
| Anteprima SKU | Anteprima SKU | Il valore indica se lo SKU è stato contrassegnato come "Anteprima". Il valore sarà "Sì" se lo SKU è stato contrassegnato di conseguenza e solo le sottoscrizioni di Azure autorizzate dal partner possono distribuire e usare questa immagine. Il valore sarà "No" se lo SKU non è stato identificato come "Anteprima". |
| ID ordine | ID dell'ordine | Identificatore univoco dell'ordine del cliente per il servizio del Marketplace commerciale. Le offerte basate sull'utilizzo di macchine virtuali non sono associate a un ordine. |
| Order Quantity | Order Quantity | Numero di asset associati all'ID ordine per gli ordini attivi |
| Nome dell'istanza cloud | Nome dell'istanza cloud | Microsoft Cloud in cui sono state distribuite le macchine virtuali. |
| Nuovo cliente | Nuovo cliente | Il valore indica se un nuovo cliente ha acquisito una o più offerte per la prima volta. Il valore sarà "Sì" se l'acquisto rientra nello stesso mese del calendario per "Data acquisizione". Il valore sarà "No" Se il cliente ha acquistato una delle offerte prima del mese del calendario indicato. |
| Order Status | Order Status | Stato di un ordine del Marketplace commerciale al momento dell'ultimo aggiornamento dei dati. |
| Order Cancel Date | Order Cancel Date | Data in cui l'ordine del Marketplace commerciale è stato annullato. |
| Customer Company Name | Customer Company Name | Nome dell'azienda fornito dal cliente. Il nome può essere diverso da quello della città nella sottoscrizione di Azure di un cliente. |
| Order Purchase Date | Order Purchase Date | Data di creazione dell'ordine del Marketplace commerciale. |
| Nome offerta | Nome offerta | Nome dell'offerta del Marketplace commerciale. |
| Trial End Date | Trial End Date | Data di scadenza del periodo di valutazione per questo ordine. |
| Customer Id | Customer ID | Identificatore univoco assegnato a un cliente. Un cliente può avere zero o più sottoscrizioni di Azure Marketplace. |
| ID account di fatturazione | ID account di fatturazione | Identificatore dell'account in cui viene generata la fatturazione. Mappa _ *ID account di fatturazione* * a **CustomerID** per connettere il report delle transazioni di pagamento con i report relativi a clienti, ordini e utilizzo. |
| AssetCount | Conteggio asset | Numero di asset associati all'ID dell'ordine. |
||||

### <a name="orders-page-filters"></a>Filtri pagina ordini

I filtri della pagina **Orders** vengono applicati a livello di pagina Orders. È possibile selezionare uno o più filtri per eseguire il rendering del grafico per i criteri scelti per la visualizzazione e i dati da visualizzare nella griglia o nell'esportazione dei dati degli ordini dettagliati. I filtri vengono applicati ai dati estratti per l'intervallo di mesi selezionato nell'angolo superiore destro della pagina Orders.

> [!TIP]
> È possibile usare l'icona di download nell'angolo in alto a destra di qualsiasi widget per scaricare i dati. È possibile inviare commenti e suggerimenti su ognuno dei widget facendo clic sull'icona "thumbs up" o "thumbs down".

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale, vedere [accedere ai report analitici per il Marketplace commerciale nel centro per i partner](./partner-center-portal/analytics.md).
- Per grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per le offerte, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard degli ordini nell'analisi per il marketplace commerciale](orders-dashboard.md).
- Per le metriche di fatturazione a consumo e di utilizzo delle offerte di macchine virtuali, vedere [Dashboard di utilizzo nell'analisi per il marketplace commerciale](./usage-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./partner-center-portal/downloads-dashboard.md).
- Per una visualizzazione consolidata dei suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere la pagina relativa alle [classificazioni & il dashboard di analisi nel centro per i partner](./partner-center-portal/ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./partner-center-portal/faq-terminology.md).
