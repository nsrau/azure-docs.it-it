---
title: Analisi per il Marketplace commerciale nel centro per i partner
description: Informazioni su come accedere ai report analitici per monitorare le vendite, valutare le prestazioni e ottimizzare le offerte del Marketplace.
author: chjenk
manager: evansma
ms.author: shthota
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 4357a91a87648885def69d8e75e19e38a5a3f751
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993713"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Analisi per il Marketplace commerciale nel centro per i partner

Informazioni su come accedere ai report analitici nel centro per i partner Microsoft per monitorare le vendite, valutare le prestazioni e ottimizzare le offerte nel Marketplace. In qualità di partner, è possibile monitorare gli elenchi di offerte usando i grafici di visualizzazione e analisi dei dati supportati dal centro per i partner e trovare i modi per ottimizzare le vendite. Gli strumenti di analisi migliorati consentono di agire sui risultati delle prestazioni e di mantenere relazioni migliori con i clienti e i rivenditori. 

Per accedere agli strumenti di analisi del centro per i partner, aprire il dashboard **[analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** in Marketplace commerciale.

|**Dashboard**|**Contenuto visualizzato**|
|:---|:---|
|[Riepilogo](#summary-dashboard)|Grafici, tendenze e valori di dati aggregati che riepilogano le attività del Marketplace per le offerte|
|[Ordini](#orders-dashboard)|Informazioni sugli ordini in formato grafico e scaricabile|
|[Clienti](#customer-dashboard)|Informazioni sui clienti, incluse le tendenze di crescita, presentate in formato grafico e scaricabile|
|[Download](#downloads-dashboard)|Elenco delle richieste di download negli ultimi 30 giorni|

## <a name="summary-dashboard"></a>Dashboard Riepilogo

Il dashboard di **Riepilogo** presenta una panoramica basata su ogni tipo di offerta. **Insights** Mostra informazioni critiche a colpo d'occhio e fornisce un'ampia panoramica dell'attività di vendita delle offerte. È possibile visualizzare questi report usando il dashboard di **Riepilogo** :

- [Totale ordini](#totals)
- [Clienti totali](#totals)
- [Posizione geografica dei clienti](#customers-by-geography)
- [Tendenze emergenti in base alle informazioni sui clienti e sugli ordini](#growth-trend-chart)
- [Tabellone clienti con ordini più elevati](#customer-leaderboard)
- [Numero di ordini organizzati in base al nome dell'offerta](#offers-by-orders)

### <a name="date-range"></a>Intervallo di date

È possibile trovare la selezione di un intervallo di date nell'angolo superiore destro di ogni pagina. L'output dei grafici della pagina **Riepilogo** può essere personalizzato selezionando un intervallo di date basato sugli ultimi 3, 6 o 12 mesi oppure selezionando un intervallo di date personalizzato con una durata massima di 12 mesi. L'intervallo di date predefinito è di sei mesi.

![Dashboard di analisi del centro per i partner](./media/analyze-dashboard.png)

### <a name="totals"></a>Totali

Nella sezione **totali** viene visualizzato il conteggio di tutti gli ordini creati o dei clienti acquisiti durante l'intervallo di date selezionato. 

- Il valore percentuale accanto a **Total Orders** e **Total Customers** rappresenta la quantità di crescita rispetto al mese precedente. 
- Un triangolo verde che punta verso l'alto indica una tendenza di crescita positiva. Un triangolo rosso che punta verso il basso indica una tendenza di crescita negativa rispetto al mese precedente. 
- Le tendenze di crescita degli ordini e dei clienti sono rappresentate da grafici a barre e visualizzeranno il valore per ogni mese passando il mouse sulle colonne del grafico.

![Analisi totali del centro per i partner](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Clienti per geografia

I **clienti di geography** mappa termica visualizzano un numero di clienti in una mappa mondiale.

- È possibile spostare la mappa per visualizzare la posizione esatta.
- È possibile ingrandire una posizione specifica.
- Mappa termica dispone di una griglia supplementare per visualizzare i dettagli relativi al numero di clienti (o al [numero di ordini](#orders-by-geography)) nella posizione specifica.
- È possibile cercare e selezionare un paese nella griglia per ingrandire la posizione della mappa. Ripristinare la visualizzazione originale premendo il pulsante **Home** nella mappa.
- Un **nuovo** cliente ha acquistato una delle offerte per la prima volta durante il mese entro l'intervallo di date selezionato.

![Centro per i partner analizzare la geografia del cliente](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Grafico tendenza crescita

È possibile visualizzare le tendenze in base alla crescita degli **ordini creati** o **ai clienti acquisiti**, visualizzando mese per mese in base all'intervallo di date selezionato. È possibile analizzare ulteriormente queste tendenze selezionando i collegamenti al di sotto del grafico, che consentono di passare alle rispettive pagine dell' **ordine** o **del cliente** .

![Il centro per i partner analizza le tendenze di crescita](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Tabellone clienti

I primi 50 clienti con il maggior numero di ordini vengono visualizzati in una *bacheca*, ordinati in base al numero massimo di ordini e alla percentuale di ordine. 

- Consente di selezionare un cliente per visualizzare i dettagli del profilo, gli ordini organizzati per offerta o gli ordini organizzati in base al tipo di licenza e al canale dei prezzi di Azure. 
- Il grafico ad anello **Offerte per ordini** presenta le prime quattro offerte (per numero di ordini) e le offerte rimanenti raggruppate come ' Rest all'.

> [!NOTE]
> Le informazioni personali del cliente verranno presentate solo se il cliente ha fornito il consenso. È possibile visualizzare queste informazioni se è stato effettuato l'accesso con un ruolo **proprietario** a livello di autorizzazioni. Gli utenti con il ruolo **collaboratore** non saranno in grado di visualizzare queste informazioni. [Altre informazioni sui ruoli utente e le autorizzazioni](./manage-account.md#define-user-roles-and-permissions).

![Il centro per i partner analizza le tendenze di crescita](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Offerte per ordini

Il grafico **Offerte per ordini** consente di organizzare gli ordini attivi in base al nome dell'offerta. 

- È possibile trascinare le sezioni dal grafico a sinistra al grafico ad anello a destra per visualizzare altri dettagli sull'offerta trascinata. Questi due grafici consentono di confrontare un'offerta specifica con le prestazioni di tutte le altre offerte ("Rest all"). 

![Il centro per i partner analizza le offerte per ordini](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Dashboard ordini

Il dashboard **Orders** del menu **Analyze** Visualizza gli ordini correnti per tutte le offerte SaaS. È possibile visualizzare le rappresentazioni grafiche di:

- [Totale ordini](#order-totals)
- [Ordini per geografia](#orders-by-geography)
- [Tendenze per gli ordini attivi e annullati](#trends-for-active-and-canceled-orders)
- [Ordini organizzati per tipo di licenza Marketplace](#orders-by-marketplace-license-type)
- [Ordini organizzati da clienti nuovi ed esistenti](#orders-by-customer-type)
- [Tabella Dettagli ordine](#order-details-table)

> [!NOTE]
> Esistono differenze tra il modo in cui i report di analisi vengono visualizzati nel portale Cloud Partner (CPP) e il nuovo programma commerciale Marketplace nel centro per i partner. Un modo specifico è che il **venditore Insights** in CPP dispone di una scheda **Orders & Usage** , che Visualizza i dati per le offerte basate sull'utilizzo e le offerte non basate sull'utilizzo. Nel centro per i partner la pagina **Orders** include una scheda separata per le offerte SaaS.

### <a name="order-totals"></a>Totale ordini

Nella sezione **Order totali** viene visualizzato il conteggio di tutti gli ordini creati, inclusi sia gli ordini **attivi** che quelli **annullati** , durante l' [intervallo di date](#date-range)selezionato. 

- Il valore percentuale accanto a **ordini totali** rappresenta la quantità di crescita rispetto al mese precedente. 
- Un triangolo verde che punta verso l'alto indica una tendenza di crescita positiva. Un triangolo rosso che punta verso il basso indica una tendenza di crescita negativa rispetto al mese precedente. 
- Le tendenze di crescita sono rappresentate da grafici a barre e visualizzeranno il valore per ogni mese passando il mouse sulle colonne del grafico.

### <a name="orders-by-geography"></a>Ordini per geografia

Il mappa termica **Orders by geography** Visualizza un conteggio degli ordini in una mappa mondiale e funziona allo stesso modo dei **[clienti in base all'area geografica mappa termica](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Tendenze per gli ordini attivi e annullati

Il grafico **ad anello attivo Orders by consente** di organizzare tutti gli ordini attivi in base ai nomi delle offerte.

- Le prime quattro offerte vengono visualizzate nel grafico e il resto delle offerte viene raggruppato come "Rest all".
- È possibile selezionare offerte specifiche nella legenda per visualizzare solo le offerte nel grafico. 
- Se si passa il puntatore del mouse su una sezione del grafico, viene visualizzato il numero di ordini e la percentuale di tale offerta rispetto al numero totale di ordini in tutte le offerte.
- La **tendenza Orders by offers** Visualizza tendenze di crescita mensili per mese. La colonna Month rappresenta il numero di ordini in base al nome dell'offerta. Il grafico a linee Visualizza la tendenza percentuale di crescita tracciata su un asse z.
- È possibile utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra e a sinistra lungo l'asse x e concentrarsi su punti dati specifici.
- È possibile visualizzare il grafico di tendenza selezionando un elemento specifico nella legenda.
- È anche possibile scegliere di visualizzare le tendenze e i dati per **gli ordini annullati**. Il grafico funzionerà nello stesso modo degli ordini attivi.

![Analizza gli ordini attivi nel centro per i partner](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Tipo di licenza Orders by Marketplace
<!-- Section needs review and clarification!  -->
Il grafico **Orders by Marketplace License Type** Visualizza un numero di ordini mensili in base al tipo di licenza e al metodo di fatturazione dell'offerta Marketplace. I tipi di licenza includono:

- **Fatturazione tramite Azure**: Microsoft fattura i clienti per conto dell'utente quando sceglie di [vendere l'offerta tramite Microsoft](./create-new-saas-offer.md#sell-through-microsoft) con questo tipo di licenza. I tipi di pagamento includono il pagamento in base al consumo tramite carta di credito o fatturazione aziendale.
- **Bring your own License**: Microsoft non addebita ai clienti l'utilizzo di questo tipo di offerta Marketplace. Elencato come **[Get it Now (gratuito)](./create-new-saas-offer.md#get-it-now-free)** nel Marketplace.
- **Gratuito**: Microsoft non addebita ai clienti l'utilizzo di questo tipo di offerta Marketplace. Elencato come **[versione di valutazione gratuita](./create-new-saas-offer.md##free-trial-listing)** nel Marketplace.
- **Microsoft As Reseller**: Rappresenta le offerte vendute da Microsoft Resellers come parte del **[programma Cloud Solution Provider (CSP)](./create-new-saas-offer.md#csp-program-opt-in)** .

![Il centro per i partner analizza gli ordini per tipo di licenza](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Ordini per tipo di cliente

Il grafico a barre **Orders by Customer Type** Visualizza il numero di ordini divisi tra **i nuovi clienti** e i **clienti esistenti**. 

- Un **nuovo cliente** ha acquisito una o più offerte per la prima volta nello stesso mese di calendario (asse y). Un **cliente esistente** ha precedentemente acquisito un'offerta dall'utente prima del mese di calendario indicato (sull'asse y). 
- Un grafico a torta aggiuntivo rappresenta tutti gli ordini creati dal cliente nuovo o esistente per l'intervallo di date selezionato.
- In entrambi i grafici è possibile scegliere di visualizzare solo i clienti nuovi o solo quelli esistenti selezionando la legenda corrispondente.

![Il centro per i partner analizza gli ordini per tipo di cliente](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Tabella Dettagli ordine

Nella **tabella Order Details** viene visualizzato un elenco numerato dei 1000 ordini principali ordinati in base alla data di acquisizione.

- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file TSV se il numero di record è inferiore a 1000.
- Se il numero di record è superiore a 1000, i dati esportati verranno posizionati in modo asincrono in una pagina di download per i successivi 30 giorni.
- I filtri possono essere applicati alla **tabella Order Details** per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati in base a paese, tipo di licenza di Azure, tipo di licenza Marketplace, tipo di offerta, stato dell'ordine, tracce gratuite, ID sottoscrizione del Marketplace, ID cliente e nome della società.

![Dettagli dell'ordine di analisi del centro per i partner](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Dashboard del cliente

Il dashboard del **cliente** del menu **analizza** Visualizza i dati per i clienti che hanno acquisito le offerte. È possibile visualizzare le rappresentazioni grafiche di:

- [Totale clienti](#customer-totals)
- [Clienti per geografia](#customers-by-geography)
- [Tendenze dei clienti](#customer-trends)
- [Clienti per ordini](#customers-by-orders)
- [Tabella Dettagli cliente](#customer-details-table)

### <a name="customer-totals"></a>Totale clienti

La sezione relativa ai **totali** dei clienti Visualizza il conteggio di tutti i clienti, inclusi quelli nuovi, esistenti e in varianza, durante l' [intervallo di date](#date-range)selezionato.

- La percentuale di crescita dei clienti rispetto al mese precedente è indicata dal numero e dall'indicatore ascendente in verde o verso il basso in rosso.
- Le tendenze di crescita sono rappresentate da grafici a barre e visualizzeranno il valore per ogni mese passando il mouse sulle colonne del grafico.

#### <a name="customer-types"></a>Tipi di cliente

Sono disponibili tre tipi di clienti: nuovo, esistente e varianza. 

- Un nuovo cliente ha acquisito una o più offerte per la prima volta entro il mese selezionato.
- Un cliente esistente ha acquisito una o più offerte prima del mese selezionato.
- Un cliente con varianza ha annullato tutte le offerte acquistate in precedenza.

### <a name="customer-trends"></a>Tendenze dei clienti

Il grafico **tendenze dei clienti** Visualizza un conteggio di tutti i clienti, inclusi quelli nuovi, esistenti e in varianza, con una tendenza di crescita mensile per mese.

- Il grafico a linee rappresenta le percentuali di crescita complessive del cliente. 
- La colonna Month rappresenta il conteggio dei clienti in pila per i clienti nuovi, esistenti e rilevati.
- Il numero di clienti varianza viene visualizzato sulla direzione negativa dell'asse Y.
- È possibile selezionare elementi di legenda specifici per visualizzare visualizzazioni più dettagliate. Ad esempio, selezionare nuovi clienti dalla legenda per visualizzare solo i nuovi clienti.
- È possibile utilizzare il dispositivo di scorrimento nella parte superiore del grafico per scorrere a destra e a sinistra sull'asse x e concentrarsi su punti dati specifici per visualizzarli in modo più dettagliato.
- Se si passa il puntatore del mouse su una colonna del grafico, vengono visualizzati i dettagli solo per quel mese.

![Centro per i partner analizzare le tendenze dei clienti](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Clienti per ordini

Il grafico **Customers by Orders** Visualizza il **primo percentile del cliente** lungo l'asse x, come determinato dal numero di ordini. L'asse y Visualizza il numero di ordini del cliente. L'asse z (grafico a linee) Visualizza la percentuale cumulativa del numero totale di ordini. È possibile visualizzare i dettagli posizionando il puntatore del mouse sui punti lungo il grafico a linee.

Ad esempio, è possibile apprendere dai dati degli ordini dei clienti che i primi 30% dei clienti contribuiscono al 83% degli ordini, equivalenti a 2.130 ordini.

![Centro per i partner analizzare gli ordini dei clienti](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Tabella Dettagli cliente

La **tabella Customer Details** Visualizza un elenco numerato dei primi 1000 clienti ordinati in base alla data in cui è stata acquisita per la prima volta una delle offerte.

- Le informazioni personali del cliente saranno disponibili solo se il cliente ha fornito il consenso. È possibile visualizzare queste informazioni solo se è stato effettuato l'accesso con un livello di autorizzazione del ruolo **proprietario** . [Altre informazioni sui ruoli utente e le autorizzazioni](./manage-account.md#define-user-roles-and-permissions).
- Ogni colonna della griglia è ordinabile.
- I dati possono essere estratti in un file TSV se il numero di record è inferiore a 1000.
- Se il numero di record è superiore a 1000, i dati esportati verranno posizionati in modo asincrono in una pagina di download per i successivi 30 giorni.
- I filtri possono essere applicati alla tabella per visualizzare solo i dati a cui si è interessati. I dati possono essere filtrati in base al nome della società, all'ID cliente, all'ID sottoscrizione del Marketplace, al tipo di licenza di Azure, alla data di acquisizione, alla data di smarrimento, alla posta elettronica del cliente, alla lingua del cliente, alla lingua e così via.

![Centro per i partner analizzare i dettagli dei clienti](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Download del dashboard

Il dashboard **download** del menu **analizza** consente di visualizzare le richieste di download che contengono oltre 1000 righe di dati del cliente o dell'ordine.

Si riceverà una notifica popup e un messaggio di posta elettronica contenente un collegamento al dashboard **Downloads** ogni volta che si richiede un download con oltre 1000 righe di dati. Questi download dei dati saranno disponibili per un periodo di 30 giorni e quindi rimossi.

![Analizza i download del centro per i partner](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Dizionario dei termini dati

| Nome attributo | Report | Definizione|
|---|---|---|
| Azure License Type | Cliente, ordine | Tipo di contratto di licenza usato dai clienti per acquistare Azure. Noto anche come canale |
| Tipo di licenza di Azure: Provider di soluzioni cloud | Cliente, ordine | Il cliente finale ottiene Azure e l'offerta del Marketplace dell'utente tramite il Cloud Solution Provider, che agisce come rivenditore.|
| Tipo di licenza di Azure: Enterprise | Cliente, ordine | Il cliente finale ottiene Azure e l'offerta del Marketplace dell'utente tramite un contratto Enterprise Agreement firmato direttamente con Microsoft.|
| Tipo di licenza di Azure: Enterprise tramite rivenditore  | Cliente, ordine | Il cliente finale approvvigiona Azure e l'offerta del Marketplace tramite un rivenditore che facilita la Enterprise Agreement con Microsoft.|  |
| Tipo di licenza di Azure: Con pagamento in base al consumo| Cliente, ordine | Il cliente finale approvvigiona Azure e l'offerta del Marketplace tramite un contratto con pagamento in base al consumo, firmato direttamente con Microsoft.||
| Nome dell'istanza cloud| Ordine| Microsoft Cloud in cui sono state distribuite le macchine virtuali.||
| Nome istanza cloud: Azure Global| Ordine| Microsoft cloud pubblico globale.|| |
| Nome istanza cloud: Azure Government | Ordine| Cloud Microsoft specifici per enti pubblici per uno dei seguenti enti pubblici: Cina, Germania o il Stati Uniti di America.| |
| Città del cliente| Cliente| Nome della città fornito dal cliente. La città potrebbe essere diversa da quella della città nella sottoscrizione di Azure di un cliente.||
| Customer Communication Language  | Cliente| Lingua di comunicazione preferita dal cliente.||
| Customer Company Name | Cliente, ordine | Nome della società fornito dal cliente. Il nome potrebbe essere diverso dal nome nella sottoscrizione di Azure di un cliente.|  |
| Customer Country | Cliente, ordine | Nome del paese fornito dal cliente. Il paese può essere diverso da quello del paese nella sottoscrizione di Azure di un cliente.|  |
| Customer Email| Cliente| Indirizzo di posta elettronica fornito dal cliente finale. Il messaggio di posta elettronica potrebbe essere diverso dall'indirizzo di posta elettronica nella sottoscrizione di Azure di un cliente.||
| Customer First Name| Cliente| Nome fornito dal cliente. Il nome potrebbe essere diverso dal nome fornito nella sottoscrizione di Azure di un cliente.| |
| ID cliente | Cliente, ordine | Identificatore univoco assegnato a un cliente. Un cliente può avere zero o più sottoscrizioni di Azure Marketplace.|  |
| Customer Postal Code  | Cliente| Il CAP fornito dal cliente. Il codice può essere diverso dal codice postale fornito nella sottoscrizione di Azure di un cliente.| |
| Customer State| Cliente| Stato (indirizzo) fornito dal cliente. Lo stato potrebbe essere diverso dal cognome fornito nella sottoscrizione di Azure di un cliente.| |
| Date Acquired| Cliente| La prima data in cui il cliente ha acquistato qualsiasi offerta pubblicata dall'utente.| |
| Date Lost| Cliente| Ultima data in cui il cliente ha annullato l'ultima delle offerte acquistate in precedenza.||
| È un nuovo cliente  | Ordine| Il valore identificherà un nuovo cliente che acquisisce una o più offerte per la prima volta (o meno). Il valore sarà "Sì" se rientra nello stesso mese di calendario per "data di acquisizione". Il valore sarà "No" Se il cliente ha acquistato una delle offerte prima del mese di calendario indicato. |
| SKU anteprima| Ordine| Il valore indica se lo SKU è stato contrassegnato come "anteprima". Il valore sarà "Yes" se lo SKU è stato contrassegnato di conseguenza e solo le sottoscrizioni di Azure autorizzate da possono distribuire e usare questa immagine. Il valore sarà "No" se lo SKU non è stato identificato come "anteprima".  |
| Consenso esplicito per il contatto promozionale| Cliente| Il valore indica se il cliente ha acconsentito in modo proattivo per il contatto promozionale da parte degli editori. Al momento l'opzione non è presentata ai clienti, pertanto è stato indicato "No" a livello generale. Dopo la distribuzione della funzionalità, si procederà all'aggiornamento dovuto.|
| Marketplace License Type| Ordine| Metodo di fatturazione dell'offerta del Marketplace dell'utente.||
| Tipo di licenza Marketplace: Fatturazione tramite Azure| Ordine| Microsoft è l'agente per questa offerta del Marketplace dell'utente ed emette le fatture al cliente per conto dell'utente. Il pagamento avviene con l'uso di carta di credito per pagamento in base al consumo o fattura aziendale.||
| Tipo di licenza Marketplace: Bring your own License | Ordine| La macchina virtuale richiede un codice di licenza fornito dal cliente per la distribuzione. Microsoft non fattura i clienti per elencare le loro offerte in questo modo tramite il Marketplace.||
| Tipo di licenza Marketplace: Gratuito| Ordine| L'offerta è configurata per essere gratuita a tutti gli utenti. Microsoft non addebita ai clienti l'utilizzo di questa offerta.||
| Tipo di licenza Marketplace: Microsoft As Reseller  | Ordine| Microsoft è il rivenditore di questa offerta del Marketplace dell'utente.|  |
| ID sottoscrizione Marketplace | Cliente, ordine | Identificatore univoco associato alla sottoscrizione di Azure usata dal cliente per acquistare l'offerta per il Marketplace. ID è stato precedentemente il GUID della sottoscrizione di Azure.||
| Nome offerta  | Ordine| Nome dell'offerta del Marketplace.|| |
| Tipo di offerta  | Ordine| Tipo di offerta di Microsoft Marketplace.|||
| Tipo di offerta: Applicazione gestita  | Ordine | Usare l'app di Azure: tipo di offerta di app gestita quando sono necessarie le condizioni seguenti: Si distribuisce una soluzione basata su sottoscrizione per i clienti che usano una macchina virtuale o un'intera soluzione basata su IaaS. Per l'utente o il cliente è necessario che la soluzione sia gestita da un partner. |
| Tipo di offerta: Applicazione di Azure| Ordine | Usare il tipo di offerta del modello di soluzione applicazione Azure quando la soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre una semplice macchina virtuale.||
| Tipo di offerta: Servizio di consulenza| Ordine| I Servizi di consulenza in Azure Marketplace consentono di connettere i clienti con servizi per supportare ed estendere l'uso di Azure.| |
| Tipo di offerta: Contenitore | Ordine| Usare il tipo di offerta Contenitore quando la soluzione è un'immagine del contenitore Docker di cui è stato effettuato il provisioning come un servizio Azure Container basato su Kubernetes.||
| Tipo di offerta: Dynamics 365 Business Central| Ordine| Usare questo tipo di offerta quando la soluzione è integrata con Dynamics 365 per Finanza e operazioni| |
| Tipo di offerta: Dynamics 365 for Customer Engagement | Ordine| Usare questo tipo di offerta quando la soluzione è integrata con Dynamics 365 per il coinvolgimento dei clienti.||
| Tipo di offerta: Modulo IoT Edge | Ordine| Azure IoT Edge moduli sono le unità di calcolo più piccole gestite da IoT Edge e possono contenere servizi Microsoft, ad esempio analisi di flusso di Azure, servizi di terze parti o codice specifico della soluzione. |
| Tipo di offerta: Applicazione Power BI | Ordine| Usare il tipo di offerta di applicazione Power BI quando si distribuisce un'applicazione integrata con Power BI.|  |
| Tipo di offerta: Applicazione SaaS| Ordine| Usare il tipo di offerta di app SaaS per consentire ai clienti di acquistare la soluzione tecnica basata su SaaS come una sottoscrizione.||
| Tipo di offerta: Macchina virtuale | Ordine| Usare il tipo di offerta per le macchine virtuali quando si distribuisce un'appliance virtuale all'abbonamento associato al cliente.||
| Tipo di offerta: Estensione Visual Studio Marketplace  | Ordine| Tipo di offerta in precedenza disponibile per gli sviluppatori di estensioni DevOps di Azure. In futuro gli sviluppatori di estensioni DevOps di Azure possono vendere la propria estensione direttamente ai clienti. Le offerte di estensione possono essere configurate come a pagamento o inclusa una versione di valutazione. |
| Order Cancel Date| Ordine| Data in cui l'ordine del Marketplace è stato eliminato.||
| ID ordine| Ordine| Identificatore univoco dell'ordine del cliente per il servizio Marketplace. Le offerte basate sull'utilizzo delle macchine virtuali non sono associate a un ordine.| |
| Order Purchase Date| Ordine| Data di creazione dell'ordine del Marketplace.|||
| Order Status| Ordine| Lo stato di un ordine del Marketplace al momento dell'ultimo aggiornamento dei dati.|     |
| Stato dell'ordine: Attivo  | Ordine| Il cliente ha acquistato un ordine e non ha annullato l'ordine.|         |
| Stato dell'ordine: annullato | Ordine| Il cliente ha precedentemente acquistato un ordine e successivamente ha annullato l'ordine.||
| Posta elettronica provider| Cliente| Indirizzo di posta elettronica del provider che ha richiesto la relazione tra Microsoft e il cliente finale. Se il cliente è un'azienda tramite rivenditore, questo sarà il rivenditore. Se è necessario un provider di soluzioni cloud (CSP), questo sarà il CSP.|
| Nome provider| Cliente| Nome del provider associato alla relazione tra Microsoft e il cliente finale. Se il cliente è un'azienda tramite rivenditore, questo sarà il rivenditore. Se è necessario un provider di soluzioni cloud (CSP), questo sarà il CSP.|
| SKU| Ordine| Nome dello SKU come definito durante la pubblicazione. Un'offerta può avere molti SKU, ma uno SKU può essere associato solo a una singola offerta.||
| Trial End Date| Ordine| Data di scadenza del periodo di valutazione per questo ordine.||
