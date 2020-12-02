---
title: Dashboard dei clienti in Microsoft Commercial Marketplace Analytics nel centro per i partner, Azure Marketplace e Microsoft AppSource
description: Informazioni su come accedere alle informazioni sui clienti, incluse le tendenze di crescita, usando il dashboard Customers in analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 878fa181ff09d547ef6594a238e9c1d883cc89b7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460490"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Dashboard dei clienti in analisi del Marketplace commerciale

Questo articolo fornisce informazioni sul dashboard Customers nel centro per i partner. Il dashboard visualizza le informazioni sui clienti, tra cui le tendenze di crescita, presentate in un formato grafico e scaricabile.

Per accedere al dashboard Customers in Partner Center, in **Marketplace commerciale** selezionare **[analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **i clienti**.

>[!NOTE]
> Per le definizioni dettagliate della terminologia di analisi, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./analytics-faq.md).

## <a name="customers-dashboard"></a>Dashboard Clienti

Il dashboard Customers Visualizza i dati per i clienti che hanno acquisito le offerte. Si possono visualizzare rappresentazioni grafiche degli elementi seguenti:

- Tendenza dei clienti attivi e agitati
- Tendenza alla crescita dei clienti, inclusi i clienti esistenti, nuovi e in varianza
- Clienti per ordini e utilizzo
- Clienti percentile 
- Tipo di cliente per ordini e utilizzo
- Clienti per area geografica
- Tabella dei dettagli dei clienti
- Filtri della pagina dei clienti

> [!NOTE]
> La latenza massima tra l'acquisizione dei clienti e la creazione di report nel centro per i partner è 48 ore.

## <a name="elements-of-the-customers-dashboard"></a>Elementi del dashboard Customers

Le sezioni seguenti descrivono come usare il dashboard Customers e come leggere i dati.

### <a name="month-range"></a>Intervallo di mesi

È possibile trovare la selezione di un intervallo di mesi nell'angolo superiore destro di ogni pagina. Personalizzare l'output dei grafici della pagina **Customers** selezionando un intervallo di mesi basato sugli ultimi 6 o 12 mesi oppure selezionando un intervallo di mesi personalizzato con una durata massima di 12 mesi. L'intervallo di mesi predefinito (periodo di calcolo) è di sei mesi.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Illustra i filtri per i mesi nella pagina Customers.":::

> [!NOTE]
> Tutte le metriche nei widget di visualizzazione e nei report di esportazione rispettano il periodo di calcolo selezionato dall'utente.

### <a name="active-and-churned-customers-trend"></a>Tendenza dei clienti attivi e agitati

In questa sezione si troverà la tendenza di crescita dei clienti per il periodo di calcolo selezionato. Le metriche e le tendenze di crescita sono rappresentate da un grafico a linee e visualizzano il valore per ogni mese passando il puntatore del mouse sulla linea del grafico. Il valore percentuale sotto i **clienti attivi** nel widget rappresenta la quantità di crescita durante il periodo di calcolo selezionato. Ad esempio, lo screenshot seguente mostra una crescita del 0,92% per il periodo di calcolo selezionato.

[![Viene illustrato il widget Customers nella pagina Customers.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Sono disponibili tre _tipi di clienti_: nuovo, esistente e inattivo.

- Un nuovo cliente ha acquistato una o più offerte per la prima volta durante il mese selezionato.
- Un cliente esistente ha acquistato una o più offerte prima del mese selezionato.
- Un cliente inattivo ha annullato tutte le offerte acquistate in precedenza. I clienti con varianza sono rappresentati nell'asse negativo nel widget tendenza.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Tendenza alla crescita dei clienti, inclusi i clienti esistenti, nuovi e in varianza

In questa sezione vengono riportate le tendenze e il conteggio di tutti i clienti, inclusi quelli nuovi, esistenti e con varianza, con una tendenza di crescita mensile.

- Il grafico a linee rappresenta le percentuali di crescita complessive dei clienti.
- La colonna del mese rappresenta il conteggio dei clienti in pila in base ai clienti nuovi, esistenti e inattivi.
- Il numero di clienti varianza viene visualizzato sulla direzione negativa dell'asse X.
- È possibile selezionare elementi di legenda specifici per visualizzare visualizzazioni più dettagliate. Ad esempio, selezionare nuovi clienti dalla legenda per visualizzare solo i nuovi clienti.
- Il passaggio del mouse su una colonna nel grafico Visualizza solo i dettagli relativi al mese.

[![Viene illustrato il widget tendenza clienti nella pagina clienti.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Clienti per ordine/utilizzo

Il grafico **Customers by Orders/Usage** include tre schede: ordini, utilizzo normalizzato e utilizzo non elaborato. Selezionare la scheda **Orders (ordini** ) per visualizzare i dettagli dell'ordine.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Viene illustrata la scheda Orders del widget Customers by Orders and Usage della pagina Customers.":::

Tenere presente quanto segue:

- La lavagna leader presenta i dettagli dei clienti classificati in base al numero di ordini. Dopo aver selezionato un cliente, le informazioni relative ai clienti vengono presentate nelle sezioni "dettagli", "Orders by SKU" e "SKU by seats" adiacenti.
- I dettagli del profilo cliente vengono visualizzati in questo spazio quando i publisher hanno eseguito l'accesso con un ruolo proprietario. Se i publisher hanno eseguito l'accesso con un ruolo Collaboratore, i dettagli in questa sezione non saranno disponibili.
- Il grafico ad anello **Orders by SKU** Visualizza la suddivisione degli ordini acquistati per i piani. Vengono visualizzati i primi cinque piani con il numero massimo di ordini, mentre quelli rimanenti vengono raggruppati in **Rest all**.
- Il grafico ad anello **SKU per postazioni** Visualizza la suddivisione dei seggi ordinati per i piani. Vengono visualizzati i primi cinque piani con le postazioni più elevate, mentre gli altri ordini sono raggruppati in **Rest all**.

È anche possibile selezionare la scheda utilizzo **normalizzato** o **utilizzo non elaborato** per visualizzare i dettagli di utilizzo.

- La lavagna leader presenta i dettagli dei clienti classificati in base alle ore di utilizzo. Dopo aver selezionato un cliente, i dettagli del cliente vengono presentati nella sezione "dettagli", "utilizzo normalizzato per offerte" e "utilizzo normalizzato dalle dimensioni della macchina virtuale (VM)".
- I dettagli del profilo cliente vengono visualizzati in questo spazio quando gli autori sono connessi con ruolo di proprietario. Se gli autori sono connessi con un ruolo di collaboratore, i dettagli in questa sezione non saranno disponibili.
- Il grafico ad anello di **utilizzo normalizzato per offerte** Mostra la suddivisione dell'utilizzo utilizzato dalle offerte. Vengono visualizzati i primi cinque piani con il numero di utilizzo più elevato, mentre il resto delle offerte viene raggruppato in **Rest all**.
- Il grafico ad anello di **utilizzo normalizzato per dimensione VM** Visualizza la suddivisione dell'utilizzo utilizzato dalle diverse dimensioni della macchina virtuale. Vengono visualizzate le prime cinque dimensioni di VM con l'utilizzo normalizzato più alto, mentre il resto dell'utilizzo viene raggruppato in **Rest all**.

### <a name="top-customers-percentile"></a>Primi clienti percentile

Il grafico **superiore dei clienti percentile** include tre schede, "Orders", "Usage normalizzato" e "Raw Usage". Il _primo percentile del cliente_ viene visualizzato lungo l'asse x, come determinato dal numero di ordini. L'asse Y visualizza il numero di ordini del cliente. L'asse Y secondario (grafico a linee) visualizza la percentuale cumulativa del numero totale di ordini. È possibile visualizzare i dettagli posizionando il puntatore del mouse sui punti lungo il grafico a linee.

[![Viene illustrata la scheda Orders del widget Top percentile Customer della pagina Customers.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Tipo di cliente per ordini e utilizzo

Nel grafico relativo agli **ordini e all'utilizzo suddivisi per tipo di cliente** sono visualizzati il numero di ordini, l'utilizzo normalizzato e le ore di utilizzo base divisi tra nuovi clienti e clienti esistenti, in base alla selezione rispettivamente di ordini, utilizzo normalizzato e utilizzo base nel grafico.

Questo grafico mostra quanto segue:

- Un nuovo cliente ha acquistato una o più offerte o ha segnalato l'utilizzo per la prima volta durante lo stesso mese di calendario (asse Y).
- Un cliente esistente ha precedentemente acquistato un'offerta o ha segnalato l'utilizzo prima del mese di calendario indicato (sull'asse Y).

[![Viene illustrata la scheda Orders del widget Orders by Customer Type della pagina Customers.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Clienti per area geografica

Per il periodo di calcolo selezionato, il mappa termica Visualizza il numero totale di clienti e la percentuale di nuovi clienti aggiunti rispetto alla dimensione Geography. Il colore diventa da chiaro a scuro sulla mappa per rappresentare il passaggio del numero di clienti da un valore basso a uno alto. Selezionare un record nella tabella per eseguire lo zoom avanti su un paese o un'area geografica.

[![Viene illustrata la scheda Orders del widget Orders by Geography della pagina Customers.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

Tenere presente quanto segue:

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile fare zoom avanti su una posizione specifica.
- La mappa termica dispone di una griglia supplementare per visualizzare i dettagli relativi al numero di clienti, al numero di ordini e alle ore di utilizzo normalizzato nella posizione specifica.
- È possibile cercare e selezionare un paese nella griglia per fare zoom avanti sulla posizione nella mappa. Ripristinare la visualizzazione originale selezionando il pulsante **Home** nella mappa.

### <a name="customer-details-table"></a>Tabella Dettagli cliente

La tabella **Customer Details** Visualizza un elenco numerato dei primi 1.000 clienti ordinati in base alla data in cui è stata acquisita per la prima volta una delle offerte. È possibile espandere una sezione selezionando l'icona di espansione nella barra multifunzione dettagli.

Tenere presente quanto segue:

- Le informazioni personali dei clienti sono disponibili solo se il cliente ha dato il suo consenso. È possibile visualizzare queste informazioni solo se è stato eseguito l'accesso con un livello di autorizzazione del ruolo proprietario.
- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un oggetto. CSV o. File TSV se il numero di record è inferiore a 1.000.
- Se il numero di record è maggiore di 1.000, i dati esportati verranno posizionati in modo asincrono in una pagina di download per i 30 giorni successivi.
- Applicare filtri alla tabella per visualizzare solo i dati a cui si è interessati. Filtrare i dati in base a nome società, ID cliente, ID sottoscrizione Marketplace, tipo di licenza di Azure, data di acquisizione, data di perdita, posta elettronica del cliente, paese del cliente/Regione/stato/città/CAP, lingua del cliente e così via.
- Quando un'offerta viene acquistata da un cliente protetto, le informazioni presenti nei **dati dettagliati del cliente** vengono mascherate (************).
- I dettagli relativi alle dimensioni del cliente, ad esempio il nome della società, il nome del cliente e la posta elettronica del cliente, sono a livello di ID organizzazione, non in Azure Marketplace o Microsoft AppSource livello di transazione.

_**Tabella 1: dizionario dei termini dati**_

| Nome colonna | Nome attributo | Definizione |
| ------------ | ------------- | ------------- |
| ID sottoscrizione Marketplace | ID sottoscrizione Marketplace | Identificatore univoco associato alla sottoscrizione di Azure usata dal cliente per acquistare l'offerta del Marketplace commerciale. L'ID è stato in precedenza il GUID della sottoscrizione di Azure. |
| DateAcquired | Date Acquired | La prima data in cui il cliente ha acquistato eventuali offerte pubblicate. |
| DateLost | Date Lost | Ultima data in cui il cliente ha annullato l'ultima delle offerte acquistate in precedenza. |
| Provider Name | Provider Name | Nome del provider associato alla relazione tra Microsoft e il cliente. Se il cliente è di tipo Enterprise tramite rivenditore, sarà il rivenditore. Se è coinvolto un provider di soluzioni cloud (CSP), sarà il CSP. |
| Posta elettronica del provider | Posta elettronica del provider | Indirizzo di posta elettronica del provider associato alla relazione tra Microsoft e il cliente. Se il cliente è di tipo Enterprise tramite rivenditore, sarà il rivenditore. Se è coinvolto un provider di soluzioni cloud (CSP), sarà il CSP. |
| FirstName | Customer First Name | Il primo nome fornito dal cliente. Il nome potrebbe essere diverso dal nome fornito nella sottoscrizione di Azure di un cliente. |
| LastName | Customer Last Name | Cognome fornito dal cliente. Il nome potrebbe essere diverso dal nome fornito nella sottoscrizione di Azure di un cliente. |
| E-mail | Customer Email | Indirizzo di posta elettronica fornito dal cliente finale. Il messaggio di posta elettronica potrebbe essere diverso dall'indirizzo di posta elettronica nella sottoscrizione di Azure di un cliente. |
| Customer Company Name | Customer Company Name | Nome dell'azienda fornito dal cliente. Il nome può essere diverso da quello della città nella sottoscrizione di Azure di un cliente. |
| CustomerCity | Città del cliente | Nome della città fornito dal cliente. La città potrebbe essere diversa da quella della città nella sottoscrizione di Azure di un cliente. |
| Customer Postal Code | Customer Postal Code | Codice postale fornito dal cliente. Il codice può essere diverso dal codice postale fornito nella sottoscrizione di Azure di un cliente. |
| CustomerCommunicationCulture | Customer Communication Language | Lingua di comunicazione preferita dal cliente. |
| CustomerCountryRegion | Paese/Area geografica del cliente | Nome del paese o dell'area geografica specificato dal cliente. Il paese/area geografica può essere diverso da quello del paese nella sottoscrizione di Azure di un cliente. |
| AzureLicenseType | Azure License Type | Tipo di contratto di licenza usato dai clienti per acquistare Azure. Noto anche come _canale_. I valori possibili sono:<ul><li>Provider di soluzioni cloud</li><li>Enterprise</li><li>Enterprise tramite rivenditore</li><li>Con pagamento in base al consumo</li></ul> |
| PromotionalCustomers | Is Promotional Contact Opt In (Consenso esplicito contatti promozionali) | Indica se il cliente ha acconsentito esplicitamente ai contatti promozionali da parte degli editori. Al momento l'opzione non è presentata ai clienti, pertanto è stato indicato "No" a livello generale. Una volta distribuita questa funzionalità, l'aggiornamento verrà avviato di conseguenza. |
| CustomerState | Customer State | Stato di residenza fornito dal cliente. Lo stato potrebbe essere diverso da quello fornito nella sottoscrizione di Azure di un cliente. |
| CommerceRootCustomer | Cliente radice commerciale | Un ID account di fatturazione può essere associato a più ID cliente.<br>Una combinazione di ID account di fatturazione e ID cliente può essere associata a più sottoscrizioni di Marketplace commerciali.<br>Il cliente radice commerciale indica il nome del cliente della sottoscrizione. |
| Customer Id | Customer ID | Identificatore univoco assegnato a un cliente. Un cliente può avere zero o più sottoscrizioni di Azure Marketplace. |
| ID account di fatturazione | ID account di fatturazione | Identificatore dell'account in cui viene generata la fatturazione. Mappare l' **ID dell'account di fatturazione** a **CustomerID** per connettere il report delle transazioni di pagamento ai report relativi a clienti, ordini e utilizzo. |
||||

### <a name="customers-page-filters"></a>Filtri della pagina dei clienti

I filtri della pagina Clienti vengono applicati a livello di pagina Clienti. È possibile selezionare più filtri per eseguire il rendering del grafico per i criteri che si desidera visualizzare e i dati da visualizzare nella griglia "dati dettagliati sugli ordini"/esportazione. I filtri vengono applicati ai dati estratti per l'intervallo di mesi selezionato nell'angolo superiore destro della pagina Customers (clienti).

> [!TIP]
> È possibile usare l'icona di download nell'angolo in alto a destra di qualsiasi widget per scaricare i dati. È possibile inviare commenti e suggerimenti su ognuno dei widget facendo clic sull'icona "thumbs up" o "thumbs down".

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale, vedere [accedere ai report analitici per il Marketplace commerciale nel centro per i partner](./partner-center-portal/analytics.md).
- Per grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per le offerte, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard Orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Per la macchina virtuale (VM) offre metriche di fatturazione di utilizzo e a consumo, vedere [dashboard di utilizzo in analisi del Marketplace commerciale](./usage-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./partner-center-portal/downloads-dashboard.md).
- Per una visualizzazione consolidata dei commenti e suggerimenti dei clienti per le offerte in Azure Marketplace e Microsoft AppSource, vedere la pagina relativa al [dashboard di analisi & revisioni in Partner Center](./partner-center-portal/ratings-reviews.md).
- Per le domande frequenti sull'analisi del Marketplace commerciale e per un dizionario completo dei termini dati, vedere [terminologia di analisi del Marketplace commerciale e domande frequenti](./analytics-faq.md).