---
title: Dashboard di utilizzo in analisi del Marketplace commerciale | Azure Marketplace
description: Informazioni su come accedere a tutte le metriche di fatturazione per l'utilizzo e a consumo per le offerte pubblicate in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 5e2bdec8b3c628f1d470acb3e27bb18eabbc71ac
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565539"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Dashboard di utilizzo nell'analisi per il marketplace commerciale

Questo articolo fornisce informazioni sul dashboard Usage (utilizzo) nel centro per i partner. Questo dashboard mostra che tutte le macchine virtuali (VM) offrono utilizzo normalizzato, utilizzo non elaborato e metriche di fatturazione a consumo in tre schede separate: utilizzo normalizzato della VM, utilizzo non elaborato della macchina virtuale e utilizzo della fatturazione a consumo.

Per accedere al dashboard Usage in centro per i partner, in **Marketplace commerciale** selezionare **[analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **utilizzo**.

>[!NOTE]
> Per le definizioni dettagliate della terminologia di analisi, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./partner-center-portal/faq-terminology.md).

## <a name="usage-dashboard"></a>Dashboard Utilizzo

Il dashboard **Usage** nel menu **analizza** Visualizza gli ordini correnti per tutte le offerte SaaS (software as a Service). È possibile visualizzare rappresentazioni grafiche degli elementi seguenti:

- Tendenza di utilizzo
- Utilizzo normalizzato per offerte
- Utilizzo normalizzato da altre dimensioni: dimensioni della macchina virtuale, canali di vendita e tipi di offerta
- Utilizzo per area geografica
- Tabella di utilizzo dettagliata
- Filtri pagina ordini

> [!NOTE]
> La latenza massima tra la generazione di eventi di utilizzo e la creazione di report nel centro per i partner è 48 ore

## <a name="elements-of-the-usage-dashboard"></a>Elementi del dashboard di utilizzo

Le sezioni seguenti descrivono come usare il dashboard Usage e come leggere i dati.

### <a name="month-range"></a>Intervallo di mesi

È possibile trovare la selezione di un intervallo di mesi nell'angolo superiore destro di ogni pagina. Personalizzare l'output dei grafici della pagina **Usage** selezionando un intervallo di mesi basato sugli ultimi 6 o 12 mesi oppure selezionando un intervallo di mesi personalizzato con una durata massima di 12 mesi. L'intervallo di mesi predefinito (periodo di calcolo) è di sei mesi.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Illustra i filtri per i mesi sul dashboard Usage.":::

### <a name="usage-trend"></a>Tendenza di utilizzo

In questa sezione sono disponibili le ore e le tendenze di utilizzo totali per tutte le offerte utilizzate dai clienti durante il periodo di calcolo selezionato. Le metriche e le tendenze di crescita sono rappresentate da un grafico a linee. Mostra il valore di ogni mese passando il puntatore del mouse sulla linea del grafico. Il valore percentuale sotto le metriche di utilizzo nel widget rappresenta la quantità di crescita o di calo durante il periodo di calcolo selezionato.

Sono disponibili due rappresentazioni delle ore di utilizzo: utilizzo normalizzato della macchina virtuale e utilizzo non elaborato delle macchine virtuali.

- Le ore di utilizzo normalizzate sono definite come ore di utilizzo normalizzate per contabilizzare il numero di core della macchina virtuale: [numero di core della macchina virtuale] x [ore di utilizzo di base]. Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore di [numero di core della macchina virtuale].
- Le ore di utilizzo non elaborato sono definite come quantità di tempo di esecuzione delle macchine virtuali in termini di ore.

[![Viene illustrato l'utilizzo normalizzato e i dati di utilizzo non elaborati nel dashboard Usage.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Utilizzo normalizzato per offerte

Questa sezione fornisce le ore e le tendenze di utilizzo totali per le offerte basate sull'utilizzo in Azure Marketplace. Il grafico di utilizzo normalizzato per le offerte è descritto di seguito.

- Nell'istogramma a colonne in pila delle **offerte utilizzo normalizzato** viene visualizzata una suddivisione delle ore di utilizzo normalizzate per le prime 5 offerte in base al periodo di calcolo selezionato. Le prime cinque offerte vengono visualizzate in un grafico, mentre le altre sono raggruppate nella categoria **Rest all** .
- Nell'istogramma a colonne in pila viene illustrata una tendenza di crescita di mese per mese per l'intervallo di date selezionato. Le colonne dei mesi rappresentano le ore di utilizzo dalle offerte con il numero più elevato di ore di utilizzo per il mese corrispondente. Il grafico a linee rappresenta la tendenza percentuale di crescita tracciata sull'asse Y secondario.
- È possibile selezionare offerte specifiche nella legenda per visualizzare solo queste nel grafico.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Viene illustrato l'utilizzo normalizzato che offre dati sul dashboard Usage.":::

È possibile selezionare qualsiasi offerta e un massimo di tre SKU dell'offerta per visualizzare la tendenza di utilizzo mese per mese per l'offerta e gli SKU selezionati.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Illustra le offerte di utilizzo normalizzate e i dati SKU nel dashboard Usage.":::

### <a name="orders-by-offers-and-skus"></a>Ordini per offerte e SKU

Il grafico **Orders by offers e SKU** Mostra le misure e le tendenze di tutte le offerte. Tenere presente quanto segue:

- Le offerte principali vengono visualizzate nel grafico e il resto delle offerte viene raggruppato come **Rest all**.
- È possibile selezionare offerte specifiche nella legenda per visualizzare solo queste nel grafico.
- Se si passa il puntatore del mouse su una sezione del grafico, viene visualizzato il numero di ordini e la percentuale di tale offerta rispetto al numero totale di ordini in tutte le offerte.
- La **tendenza degli ordini in base alle offerte** visualizza le tendenze di crescita mensili. La colonna del mese rappresenta il numero di ordini in base al nome dell'offerta. Il grafico a linee Visualizza la tendenza percentuale di crescita tracciata sull'asse z.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Illustra il grafico Orders by offers and SKUS nel dashboard Usage.":::

È possibile selezionare qualsiasi offerta e un massimo di tre SKU dell'offerta per visualizzare la tendenza di mese per mese per l'offerta, gli SKU e le postazioni.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Illustra il grafico Orders by offers and SKUS nel dashboard Usage. Vengono visualizzate le tendenze relative all'offerta, alla tendenza degli SKU e alle postazioni.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Utilizzo normalizzato da altre dimensioni: dimensioni della macchina virtuale, canali di vendita e tipo di offerta

Sono disponibili tre schede per le dimensioni: dimensioni della macchina virtuale, canali di vendita e tipo di offerta. È possibile visualizzare le metriche di utilizzo e la tendenza mensile rispetto a ognuna di queste dimensioni.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Illustra il grafico di altre dimensioni di utilizzo normalizzato nel dashboard Usage.":::

### <a name="usage-by-geography"></a>Utilizzo per area geografica

Per il periodo di calcolo selezionato, mappa termica Visualizza l'utilizzo totale rispetto alla dimensione Geography. Il colore diventa da chiaro a scuro sulla mappa per rappresentare il passaggio del numero di clienti da un valore basso a uno alto. Selezionare un record nella tabella per eseguire lo zoom avanti su un paese/area geografica.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Illustra il grafico dei paesi di utilizzo normalizzato nel dashboard Usage.":::

Tenere presente quanto segue:

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile fare zoom avanti su una posizione specifica.
- Mappa termica dispone di una griglia supplementare per visualizzare i dettagli relativi al numero di clienti, al numero di ordini e alle ore di utilizzo normalizzato nella posizione specifica.
- È possibile cercare e selezionare un paese nella griglia per fare zoom avanti sulla posizione nella mappa. Ripristinare la visualizzazione originale selezionando il pulsante **Home** nella mappa.

### <a name="usage-details-table"></a>Tabella dettagli utilizzo

Nella tabella **Dettagli utilizzo** viene visualizzato un elenco numerato dei primi 1.000 record di utilizzo ordinati in base all'utilizzo. Tenere presente quanto segue:

- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un oggetto. TSV o. File CSV se il numero di record è inferiore a 1.000.
- Se il numero di record è superiore a 1.000, i dati di esportazione verranno posizionati in modo asincrono in una pagina di download che sarà disponibile per i 30 giorni successivi.
- Applicare filtri ai **dati di utilizzo dettagliati** per visualizzare solo i dati a cui si è interessati. Filtrare i dati per paese/area geografica, canale di vendita, tipo di licenza Marketplace, tipo di utilizzo, nome dell'offerta, tipo di offerta, versioni di valutazione gratuite, ID sottoscrizione del Marketplace, ID cliente e nome della società.

_**Tabella 1: dizionario dei termini dati**_

| Nome colonna | Nome attributo | Definizione |
| ------------ | ------------- | ------------- |
| ID sottoscrizione Marketplace | ID sottoscrizione Marketplace | Identificatore univoco associato alla sottoscrizione di Azure usata dal cliente per acquistare l'offerta del Marketplace commerciale. L'ID è stato in precedenza il GUID della sottoscrizione di Azure. |
| MonthStartDate | Data di inizio del mese | Data di inizio del mese rappresenta il mese di acquisto. |
| Tipo di offerta | Tipo di offerta | Tipo di offerta di Marketplace commerciale. |
| Azure License Type | Azure License Type | Tipo di contratto di licenza usato dai clienti per acquistare Azure. Noto anche come canale. I valori possibili sono:<ui><li>Provider di soluzioni cloud</li><li>Enterprise</li><li>Enterprise tramite rivenditore</li><li>Con pagamento in base al consumo</li></ul> |
| Marketplace License Type | Marketplace License Type | Metodo di fatturazione dell'offerta del Marketplace commerciale. I valori possibili sono:<ul><li>Fatturazione tramite Azure</li><li>Bring your own License</li><li>Gratuito</li><li>Microsoft As Reseller</li></ul> |
| SKU | SKU | Piano associato all'offerta. |
| Customer Country | Paese/Area geografica del cliente | Nome del paese o dell'area geografica specificato dal cliente. Il paese/area geografica può essere diverso da quello del paese nella sottoscrizione di Azure di un cliente. |
| Anteprima SKU | Anteprima SKU | Il valore indica se lo SKU è stato contrassegnato come "anteprima". Il valore sarà "Sì" se lo SKU è stato contrassegnato di conseguenza e solo le sottoscrizioni di Azure autorizzate dal partner possono distribuire e usare questa immagine. Il valore sarà "No" se lo SKU non è stato identificato come "Anteprima". |
| SKU Billing Type | SKU Billing Type | Tipo di fatturazione associato a ogni SKU nell'offerta. I valori possibili sono:<ul><li>Gratuito</li><li>Paid</li></ul> |
| IsInternal | Deprecato | Deprecato |
| Dimensioni macchina virtuale | Dimensioni della macchina virtuale | Per i tipi di offerta basati su VM, questa entità indica le dimensioni della macchina virtuale associata allo SKU dell'offerta. |
| Nome dell'istanza cloud | Nome dell'istanza cloud | Microsoft Cloud in cui sono state distribuite le macchine virtuali. |
| ServicePlanName | Deprecato | Deprecato (stessa definizione dello SKU) |
| Nome offerta | Nome offerta | Nome dell'offerta del Marketplace commerciale. |
| DeploymentMethod | Deprecato | Deprecato (stessa definizione del tipo di offerta)
 |
| Customer Company Name | Customer Company Name | Nome dell'azienda fornito dal cliente. Il nome può essere diverso da quello della città nella sottoscrizione di Azure di un cliente. |
| Data utilizzo | Data utilizzo | Data di generazione dell'evento Usage per gli asset basati sull'utilizzo. |
| IsMultisolution | È multisoluzione | Indica se l'offerta è un tipo di offerta multisoluzione. |
| Nuovo cliente | Deprecato | Deprecato |
| Dimensioni core | Dimensioni core | Numero di core associati all'offerta basata su VM. |
| Usage Type | Usage Type | Indica se l'evento di utilizzo associato all'offerta è uno dei seguenti:<ul><li>Utilizzo normalizzato</li><li>Utilizzo non elaborato</li><li>Misurazione dell'utilizzo</li></ul> |
| Trial End Date | Trial End Date | Data di scadenza del periodo di valutazione per questo ordine. |
| Customer Currency (CC) | Valuta cliente | Valuta utilizzata dal cliente per la transazione del Marketplace commerciale. |
| Price (CC) | Prezzo | Prezzo unitario dello SKU visualizzato nella valuta del cliente. |
| Payout Currency (PC) | Valuta pagamento | Il server di pubblicazione viene pagato per gli eventi di utilizzo associati all'asset nella valuta configurata dall'editore. |
| Estimated Price (PC) | Prezzo stimato | Prezzo unitario dello SKU nella valuta configurata dall'editore. |
| Usage Reference | Usage Reference | GUID concatenato utilizzato per connettere il report sull'utilizzo (in analisi del Marketplace commerciale) al report delle transazioni di pagamento. Il riferimento all'utilizzo è connesso a OrderId e LineItemId nel report delle transazioni di pagamento. |
| Unità di utilizzo | Unità di utilizzo | Unità di consumo associata allo SKU. |
| Customer Id | Customer ID | Identificatore univoco assegnato a un cliente. Un cliente può avere zero o più sottoscrizioni di Azure Marketplace. |
| ID account di fatturazione | ID account di fatturazione | Identificatore dell'account in cui viene generata la fatturazione. Mappare l' **ID dell'account di fatturazione** a **CustomerID** per connettere il report delle transazioni di pagamento ai report relativi a clienti, ordini e utilizzo. |
| Quantità di utilizzo | Quantità di utilizzo | Unità di utilizzo totali utilizzate dall'asset distribuito dal cliente.<br>Si basa sull'elemento del tipo di utilizzo. Se, ad esempio, il tipo di utilizzo corrisponde all'utilizzo normalizzato, la quantità di utilizzo è per l'utilizzo normalizzato. |
| NormalizedUsage | Normalized Usage | Unità di utilizzo normalizzate totali utilizzate dall'asset distribuito dal cliente.<br>Le ore di utilizzo normalizzate sono definite come ore di utilizzo normalizzate per contabilizzare il numero di core della macchina virtuale: [numero di core della macchina virtuale] x [ore di utilizzo di base]. Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore di [numero di core della macchina virtuale]. |
| MeteredUsage | Utilizzo a consumo | Unità di utilizzo totali utilizzate dai contatori configurati con l'offerta distribuita dal cliente. |
| RawUsage | Raw Usage | Unità di utilizzo RAW totali utilizzate dall'asset distribuito dal cliente.<br>Le ore di utilizzo non elaborato sono definite come quantità di tempo in cui le macchine virtuali sono in esecuzione in termini di unità di utilizzo. |
| Estimated Extended Charge (CC) | Costo esteso stimato nella valuta del cliente | Indica gli addebiti associati all'utilizzo. La colonna è il prodotto di Price (CC) e Quantity Usage. |
| Estimated Extended Charge (PC) | Addebito esteso stimato nella valuta del pagamento | Indica gli addebiti associati all'utilizzo. La colonna è il prodotto del prezzo stimato (PC) e della quantità di utilizzo. |
||||

### <a name="usage-page-filters"></a>Filtri pagina utilizzo

I filtri della pagina **Usage** vengono applicati a livello di pagina Orders. È possibile selezionare uno o più filtri per eseguire il rendering del grafico per i criteri scelti per visualizzare e i dati che si desidera visualizzare nella griglia/esportazione dati degli ordini di utilizzo. I filtri vengono applicati ai dati estratti per l'intervallo di mesi selezionato nell'angolo superiore destro della pagina Usage.

I widget e il report di esportazione per l'utilizzo non elaborato della macchina virtuale sono simili all'utilizzo normalizzato della VM con le seguenti differenze:

- Le ore di utilizzo normalizzate sono definite come ore di utilizzo normalizzate per contabilizzare il numero di core della macchina virtuale: [numero di core della macchina virtuale] x [ore di utilizzo di base]. Le macchine virtuali designate come "SHAREDCORE" usano 1/6 (o 0,1666) come moltiplicatore di [numero di core della macchina virtuale].
- Le ore di utilizzo non elaborato sono definite come quantità di tempo in cui le macchine virtuali sono in esecuzione in termini di unità di utilizzo.

### <a name="metered-billing-usage"></a>Utilizzo fatturazione a consumo

La scheda **utilizzo** a consumo Visualizza le informazioni sull'utilizzo per i tipi di offerta in cui l'utilizzo viene misurato in base alla dimensione del contatore. Attualmente è visualizzata l'eccedenza del tipo di offerta SaaS. La scheda presenta le rappresentazioni grafiche delle tendenze di eccedenza per l'utilizzo di fatturazione a consumo SaaS:

- **Overage trend by meter dimension** (Tendenza eccedenza per dimensione del contatore): visualizza la tendenza dell'eccedenza mensile per la dimensione del contatore selezionata di un'offerta. L'asse X rappresenta il mese e l'asse Y rappresenta la quantità di utilizzo in eccedenza. Anche l'unità di misura del contatore personalizzato viene visualizzata sull'asse Y.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Illustra il grafico di utilizzo normalizzato della macchina virtuale nel dashboard Usage.":::

- **Tendenza in eccedenza per SKU** : rappresenta la tendenza della quantità di utilizzo della dimensione del contatore selezionata per SKU/piani. Vengono visualizzati i primi cinque piani con la quantità di utilizzo più elevata per l'offerta selezionata.

- **Tendenza in eccedenza da parte dei clienti** : la lavagna del cliente rappresenta un elenco in pila dei clienti con le ore di utilizzo più elevate e vengono visualizzate in una _bacheca_ , classificate in base all'utilizzo più elevato del contatore personalizzato. Selezionare un cliente nella lavagna principale per visualizzare la tendenza di utilizzo in eccedenza di una dimensione del contatore selezionata.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Illustra il grafico sull'utilizzo a consumo da parte dei clienti nel dashboard Usage.":::

Se sono disponibili più offerte che usano contatori personalizzati, il report sull'utilizzo della fatturazione a consumo Mostra le informazioni di utilizzo per tutte le offerte, in base alle dimensioni del contatore personalizzate.

> [!TIP]
> È possibile usare l'icona di download nell'angolo in alto a destra di qualsiasi widget per scaricare i dati. È possibile inviare commenti e suggerimenti su ognuno dei widget facendo clic sull'icona "thumbs up" o "thumbs down".

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale, vedere [accedere ai report analitici per il Marketplace commerciale nel centro per i partner](./partner-center-portal/analytics.md).
- Per i grafici, le tendenze e i valori dei dati aggregati che riassumono l'attività del marketplace per l'offerta, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [dashboard degli ordini in analisi del Marketplace commerciale](./orders-dashboard.md)
- Per la macchina virtuale (VM) offre metriche di fatturazione di utilizzo e a consumo, vedere [dashboard di utilizzo in analisi del Marketplace commerciale](usage-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./partner-center-portal/downloads-dashboard.md).
- Per una visualizzazione consolidata dei commenti e suggerimenti dei clienti per le offerte in Azure Marketplace e Microsoft AppSource, vedere la pagina relativa al [dashboard di analisi & revisioni in Partner Center](./partner-center-portal/ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./partner-center-portal/faq-terminology.md).
