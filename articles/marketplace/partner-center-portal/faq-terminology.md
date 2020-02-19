---
title: Domande frequenti e terminologia per l'analisi del Marketplace commerciale nel centro per i partner
description: Informazioni su come risolvere le domande frequenti sull'analisi del Marketplace commerciale. Include un dizionario dei dati per la terminologia di analisi.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 081109c2208e2006eb1628fbf0cfb99b1e6bd8f9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462140"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Domande frequenti e terminologia per l'analisi del Marketplace commerciale

Questo articolo illustra le domande frequenti sui messaggi di analisi nel centro per i partner e fornisce anche un dizionario di terminologia di analisi.

## <a name="frequently-asked-questions"></a>Domande frequenti

In questa sezione vengono fornite le risposte alle domande più frequenti su **Nessuna analisi disponibile** nel centro per i partner.

**Non è possibile visualizzare i dati analitici nel centro per i partner. Quando si accede a queste pagine, viene visualizzato il messaggio seguente. Perché?**

![Non sono ancora disponibili dati per le offerte](./media/analytics-faq-no-data.png)

Perché è possibile che venga ricevuto questo messaggio:

- Non sono attualmente disponibili acquisizioni per le offerte pubblicate nel Marketplace. Questo può significare che le offerte sono disponibili nel Marketplace e ottengono viste dai clienti nelle pagine di visualizzazione del prodotto, ma i clienti non hanno ancora eseguito l'acquisto e la distribuzione.
- La pubblicazione dell'offerta potrebbe essere in corso e non è ancora attiva. I clienti possono acquisire solo offerte Live. Per verificare lo stato delle offerte, vedere Panoramica nel dashboard di [analisi](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Per altre informazioni, vedere [dashboard di riepilogo in Commercial Marketplace Analytics](./summary-dashboard.md).
- Le offerte possono essere elencate come **contattatemi**, che sono offerte solo elenco e che non possono essere acquistate dai clienti nel Marketplace. Anche se queste offerte generano lead e vengono condivise con l'utente, gli ordini non vengono creati per queste offerte perché non possono essere acquistati. Per controllare il tipo di inserzione dell'offerta, passare alla pagina di installazione.

**So che ho dati analitici, ma il messaggio seguente è visualizzato:**

![Nessun dato per l'intervallo di date specificato](./media/analytics-faq-data-range.png)

Se si riceve questo messaggio, significa che si hanno dati di analisi ma non sono disponibili dati per l'intervallo di date selezionato. Selezionare un intervallo di date diverso o un intervallo di date personalizzato per visualizzare i dati a partire da 2010. Per altre informazioni, vedere la sezione relativa all'intervallo di date del [dashboard di riepilogo in Commercial Marketplace Analytics](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Dizionario dei termini dati

| Nome attributo | Report | Definizione|
|---|---|---|
| Azure License Type | Cliente, ordine | Tipo di contratto di licenza usato dai clienti per acquistare Azure. Noto anche come canale |
| Azure License Type: Cloud Solution Provider | Cliente, ordine | Il cliente finale ottiene Azure e l'offerta del Marketplace dell'utente tramite il Cloud Solution Provider, che agisce come rivenditore.|
| Azure License Type: Enterprise | Cliente, ordine | Il cliente finale ottiene Azure e l'offerta del Marketplace dell'utente tramite un contratto Enterprise Agreement firmato direttamente con Microsoft.|
| Azure License Type: Enterprise through Reseller  | Cliente, ordine | Il cliente finale approvvigiona Azure e l'offerta del Marketplace tramite un rivenditore che facilita la Enterprise Agreement con Microsoft.|  |
| Azure License Type: Pay as You Go| Cliente, ordine | Il cliente finale approvvigiona Azure e l'offerta del Marketplace tramite un contratto con pagamento in base al consumo, firmato direttamente con Microsoft.||
| Nome dell'istanza cloud| Order| Microsoft Cloud in cui sono state distribuite le macchine virtuali.||
| Nome istanza cloud: globale di Azure| Order| Microsoft cloud pubblico globale.|| |
| Nome istanza cloud: Azure per enti pubblici | Order| Cloud Microsoft specifici per enti pubblici per uno dei seguenti enti pubblici: Cina, Germania o il Stati Uniti di America.| |
| Città del cliente| Cliente| Nome della città fornito dal cliente. La città potrebbe essere diversa da quella della città nella sottoscrizione di Azure di un cliente.||
| Customer Communication Language  | Cliente| Lingua di comunicazione preferita dal cliente.||
| Customer Company Name | Cliente, ordine | Nome della società fornito dal cliente. Il nome può essere diverso da quello della città nella sottoscrizione di Azure di un cliente.|  |
| Customer Country | Cliente, ordine | Nome del paese fornito dal cliente. Il paese può essere diverso da quello del paese nella sottoscrizione di Azure di un cliente.|  |
| Customer Email| Cliente| Indirizzo di posta elettronica fornito dal cliente finale. Il messaggio di posta elettronica potrebbe essere diverso dall'indirizzo di posta elettronica nella sottoscrizione di Azure di un cliente.||
| Customer First Name| Cliente| Nome fornito dal cliente. Il nome potrebbe essere diverso dal nome fornito nella sottoscrizione di Azure di un cliente.| |
| Customer ID | Cliente, ordine | Identificatore univoco assegnato a un cliente. Un cliente può avere zero o più sottoscrizioni di Azure Marketplace.|  |
| Customer Postal Code  | Cliente| Il CAP fornito dal cliente. Il codice può essere diverso dal codice postale fornito nella sottoscrizione di Azure di un cliente.| |
| Customer State| Cliente| Stato (indirizzo) fornito dal cliente. Lo stato potrebbe essere diverso da quello fornito nella sottoscrizione di Azure di un cliente.| |
| Date Acquired| Cliente| La prima data in cui il cliente ha acquistato qualsiasi offerta pubblicata dall'utente.| |
| Date Lost| Cliente| Ultima data in cui il cliente ha annullato l'ultima delle offerte acquistate in precedenza.||
| È un nuovo cliente  | Order| Il valore identificherà un nuovo cliente che acquisisce una o più offerte per la prima volta (o meno). Il valore sarà "Sì" se rientra nello stesso mese di calendario per "data di acquisizione". Il valore sarà "No" Se il cliente ha acquistato una delle offerte prima del mese di calendario indicato. |
| SKU anteprima| Order| Il valore indica se lo SKU è stato contrassegnato come "anteprima". Il valore sarà "Yes" se lo SKU è stato contrassegnato di conseguenza e solo le sottoscrizioni di Azure autorizzate da possono distribuire e usare questa immagine. Il valore sarà "No" se lo SKU non è stato identificato come "anteprima".  |
| Consenso esplicito per il contatto promozionale| Cliente| Il valore indica se il cliente ha acconsentito in modo proattivo per il contatto promozionale da parte degli editori. Al momento l'opzione non è presentata ai clienti, pertanto è stato indicato "No" a livello generale. Dopo la distribuzione della funzionalità, si procederà all'aggiornamento dovuto.|
| Marketplace License Type| Order| Metodo di fatturazione dell'offerta del Marketplace dell'utente.||
| Marketplace License Type: Billed Through Azure| Order| Microsoft è l'agente per questa offerta del Marketplace dell'utente ed emette le fatture al cliente per conto dell'utente. Il pagamento avviene con l'uso di carta di credito per pagamento in base al consumo o fattura aziendale.||
| Marketplace License Type: Bring Your Own License | Order| La macchina virtuale richiede un codice di licenza fornito dal cliente per la distribuzione. Microsoft non fattura i clienti per elencare le loro offerte in questo modo tramite il Marketplace.||
| Marketplace License Type: Free| Order| L'offerta è configurata per essere gratuita a tutti gli utenti. Microsoft non addebita ai clienti l'utilizzo di questa offerta.||
| Marketplace License Type: Microsoft as Reseller  | Order| Microsoft è il rivenditore di questa offerta del Marketplace dell'utente.|  |
| ID sottoscrizione Marketplace | Cliente, ordine | Identificatore univoco associato alla sottoscrizione di Azure usata dal cliente per acquistare l'offerta per il Marketplace. ID è stato precedentemente il GUID della sottoscrizione di Azure.||
| Nome offerta  | Order| Nome dell'offerta del Marketplace.|| |
| Tipo di offerta  | Order| Tipo di offerta di Microsoft Marketplace.|||
| Offer Type: Managed Application  | Ordine | Usare l'app Azure: tipo di offerta app gestita quando sono necessarie le condizioni seguenti: si distribuisce una soluzione basata sulla sottoscrizione per il cliente usando una macchina virtuale o un'intera soluzione basata su IaaS. Per l'utente o il cliente è necessario che la soluzione sia gestita da un partner. |
| Tipo di offerta: applicazione Azure| Ordine | Usare il tipo di offerta del modello di soluzione applicazione Azure quando la soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre una semplice macchina virtuale.||
| Tipo di offerta: servizio di consulenza| Order| I Servizi di consulenza in Azure Marketplace consentono di connettere i clienti con servizi per supportare ed estendere l'uso di Azure.| |
| Tipo di offerta: contenitore | Order| Usare il tipo di offerta Contenitore quando la soluzione è un'immagine del contenitore Docker di cui è stato effettuato il provisioning come un servizio Azure Container basato su Kubernetes.||
| Tipo di offerta: Dynamics 365 business Central| Order| Usare questo tipo di offerta quando la soluzione è integrata con Dynamics 365 per Finanza e operazioni| |
| Tipo di offerta: Dynamics 365 per Customer Engagement | Order| Usare questo tipo di offerta quando la soluzione è integrata con Dynamics 365 per il coinvolgimento dei clienti.||
| Tipo di offerta: modulo IoT Edge | Order| Azure IoT Edge moduli sono le unità di calcolo più piccole gestite da IoT Edge e possono contenere servizi Microsoft, ad esempio analisi di flusso di Azure, servizi di terze parti o codice specifico della soluzione. |
| Tipo di offerta: applicazione Power BI | Order| Usare il tipo di offerta di applicazione Power BI quando si distribuisce un'applicazione integrata con Power BI.|  |
| Tipo di offerta: applicazione SaaS| Order| Usare il tipo di offerta di app SaaS per consentire ai clienti di acquistare la soluzione tecnica basata su SaaS come una sottoscrizione.||
| Tipo di offerta: macchina virtuale | Order| Usare il tipo di offerta per le macchine virtuali quando si distribuisce un'appliance virtuale all'abbonamento associato al cliente.||
| Tipo di offerta: estensione Visual Studio Marketplace  | Order| Tipo di offerta in precedenza disponibile per gli sviluppatori di estensioni DevOps di Azure. In futuro gli sviluppatori di estensioni DevOps di Azure possono vendere la propria estensione direttamente ai clienti. Le offerte di estensione possono essere configurate come a pagamento o inclusa una versione di valutazione. |
| Order Cancel Date| Order| Data in cui l'ordine del Marketplace è stato eliminato.||
| Order ID| Order| Identificatore univoco dell'ordine del cliente per il servizio Marketplace. Le offerte basate sull'utilizzo delle macchine virtuali non sono associate a un ordine.| |
| Order Purchase Date| Order| Data di creazione dell'ordine del Marketplace.|||
| Order Status| Order| Lo stato di un ordine del Marketplace al momento dell'ultimo aggiornamento dei dati.|     |
| Order Status: Active  | Order| Il cliente ha acquistato un ordine e non ha annullato l'ordine.|         |
| Stato dell'ordine: annullato | Order| Il cliente ha precedentemente acquistato un ordine e successivamente ha annullato l'ordine.||
| Posta elettronica provider| Cliente| Indirizzo di posta elettronica del provider che ha richiesto la relazione tra Microsoft e il cliente finale. Se il cliente è un'azienda tramite rivenditore, questo sarà il rivenditore. Se è necessario un provider di soluzioni cloud (CSP), questo sarà il CSP.|
| Nome provider| Cliente| Nome del provider associato alla relazione tra Microsoft e il cliente finale. Se il cliente è un'azienda tramite rivenditore, questo sarà il rivenditore. Se è necessario un provider di soluzioni cloud (CSP), questo sarà il CSP.|
| SKU| Order| Nome dello SKU come definito durante la pubblicazione. Un'offerta può avere molti SKU, ma uno SKU può essere associato solo a una singola offerta.||
| Trial End Date| Order| Data di scadenza del periodo di valutazione per questo ordine.||

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale del centro per i partner, vedere [analisi per il Marketplace commerciale nel centro per i partner](./analytics.md).
- Per i grafici, le tendenze e i valori dei dati aggregati che riepilogano l'attività del Marketplace per l'offerta, vedere [dashboard di riepilogo in analisi del Marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard Orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Per la macchina virtuale (VM) offre metriche di fatturazione di utilizzo e a consumo, vedere [dashboard di utilizzo in analisi del Marketplace commerciale](./usage-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [dashboard dei clienti in analisi del Marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere la pagina relativa ai [download del dashboard in Commercial Marketplace Analytics](./downloads-dashboard.md).
- Per una visualizzazione consolidata dei suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard ratings and Reviews in Commercial Marketplace Analytics](./ratings-reviews.md).
