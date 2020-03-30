---
title: Domande frequenti e terminologia per l'analisi di Commercial Marketplace nel Centro per i partner
description: Scopri come rispondere alle domande frequenti sull'analisi di Commercial Marketplace. Include un dizionario dei dati per la terminologia di analisi.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d53ae01fb302382ca5bc2d31e729e153aea6c838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286082"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Domande frequenti e terminologia per l'analisi di Commercial Marketplace

Questo articolo risponde alle domande frequenti sui messaggi di analisi nel Centro per i partner e fornisce anche un dizionario della terminologia di analisi.

## <a name="frequently-asked-questions"></a>Domande frequenti

In questa sezione vengono fornite le risposte alle domande frequenti sui messaggi **Nessun aumento disponibile ancora** nel Centro per i partner.

**Non riesco a visualizzare i miei dati di analisi nel Centro per i partner. Quando accedo a queste pagine, vedo il messaggio qui sotto. Perché?**

![Nessun dato per le tue offerte ancora](./media/analytics-faq-no-data.png)

Perché è possibile che venga visualizzato questo messaggio:

- Al momento non esistono acquisizioni per le offerte pubblicate sul mercato. Ciò può significare che le tue offerte sono in diretta sul mercato e acquisiscono visualizzazioni dai clienti nelle pagine di visualizzazione dei prodotti, ma i clienti non hanno ancora preso provvedimenti per acquistarle e distribuirle.
- La pubblicazione dell'offerta potrebbe essere in corso e non è ancora in diretta. Solo le offerte live possono essere acquisite dai clienti. Per verificare lo stato delle offerte, vedere Panoramica nel [dashboard Analizza](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Per ulteriori informazioni, vedere [Dashboard di riepilogo nell'analisi](./summary-dashboard.md)di Commercial Marketplace.
- Le tue offerte possono essere elencate come **Contattami**, che sono offerte di solo elenco e non possono essere acquistate dai clienti sul mercato. Anche se queste offerte generano lead e vengono condivise con te, gli ordini non vengono creati per queste offerte in quanto non possono essere acquistati. Per verificare il tipo di offerta, vai alla pagina di configurazione.

**So di avere dati di analisi, ma viene visualizzato il messaggio seguente:**

![Nessun dato per un determinato intervallo di date](./media/analytics-faq-data-range.png)

Se ricevi questo messaggio, significa che hai dati di analisi ma non ci sono dati per l'intervallo di date selezionato. Selezionare un intervallo di date o un intervallo di date personalizzato diverso per visualizzare i dati dal 2010. Per ulteriori informazioni, vedere la sezione Intervallo di date di Dashboard di [riepilogo nell'analisi](./summary-dashboard.md)di Commercial Marketplace .

## <a name="dictionary-of-data-terms"></a>Dizionario dei termini dei dati

| Nome attributo | Report | Definizione|
|---|---|---|
| Azure License Type | Cliente, Ordine | Tipo di contratto di licenza usato dai clienti per acquistare Azure. Noto anche come Canale |
| Azure License Type: Cloud Solution Provider | Cliente, Ordine | Il cliente finale ottiene Azure e l'offerta del Marketplace dell'utente tramite il Cloud Solution Provider, che agisce come rivenditore.|
| Azure License Type: Enterprise | Cliente, Ordine | Il cliente finale ottiene Azure e l'offerta del Marketplace dell'utente tramite un contratto Enterprise Agreement firmato direttamente con Microsoft.|
| Azure License Type: Enterprise through Reseller  | Cliente, Ordine | Il cliente finale acquista l'offerta di Azure e Marketplace tramite un rivenditore che facilita il contratto Enterprise Agreement con Microsoft.|  |
| Azure License Type: Pay as You Go| Cliente, Ordine | Il cliente finale acquista Azure e l'offerta Marketplace tramite un contratto "Pay as You Go", firmato direttamente con Microsoft.||
| Nome dell'istanza cloud| JSON| Microsoft Cloud in cui sono state distribuite le macchine virtuali.||
| Nome istanza cloud: Azure GlobalCloud Instance Name: Azure Global| JSON| Il cloud Microsoft globale pubblico.|| |
| Nome dell'istanza cloud: Azure per enti pubbliciCloud Instance Name: Azure Government | JSON| Cloud Microsoft specifici per il governo per uno dei seguenti governi: Cina, Germania o Stati Uniti d'America.| |
| Città del cliente| Customer| Nome della città fornito dal cliente. Città potrebbe essere diversa dalla città nella sottoscrizione di Azure di un cliente.||
| Customer Communication Language  | Customer| Lingua di comunicazione preferita dal cliente.||
| Customer Company Name | Cliente, Ordine | Nome della società fornito dal cliente. Il nome potrebbe essere diverso dalla città nella sottoscrizione di Azure di un cliente.|  |
| Customer Country | Cliente, Ordine | Nome del paese fornito dal cliente. Il paese potrebbe essere diverso da quello della sottoscrizione di Azure di un cliente.|  |
| Customer Email| Customer| Indirizzo e-mail fornito dal cliente finale. La posta elettronica potrebbe essere diversa dall'indirizzo di posta elettronica nella sottoscrizione di Azure di un cliente.||
| Customer First Name| Customer| Nome fornito dal cliente. Il nome potrebbe essere diverso da quello fornito nella sottoscrizione di Azure di un cliente.| |
| ID cliente | Cliente, Ordine | Identificatore univoco assegnato a un cliente. Un cliente può avere zero o più sottoscrizioni di Azure Marketplace.A customer may have zero or more Azure Marketplace Subscriptions.|  |
| Customer Postal Code  | Customer| Il codice postale fornito dal cliente. Il codice potrebbe essere diverso dal codice postale fornito nella sottoscrizione di Azure di un cliente.| |
| Customer State| Customer| Lo stato (indirizzo) fornito dal cliente. Lo stato potrebbe essere diverso dallo stato fornito nella sottoscrizione di Azure di un cliente.| |
| Date Acquired| Customer| La prima data in cui il cliente ha acquistato qualsiasi offerta pubblicata dall'utente.| |
| Date Lost| Customer| L'ultima data in cui il cliente ha annullato l'ultima di tutte le offerte acquistate in precedenza.||
| È nuovo cliente  | JSON| Il valore identificherà un nuovo cliente che acquisisce una o più delle tue offerte per la prima volta (o meno). Il valore sarà "Sì" se all'interno dello stesso mese di calendario per "Data acquisizione". Il valore sarà "No" se il cliente ha acquistato una delle tue offerte prima del mese di calendario segnalato. |
| È lo SKU di anteprima| JSON| Il valore ti informerà se hai taggato lo SKU come "anteprima". Il valore sarà "Sì" se lo SKU è stato contrassegnato di conseguenza e solo le sottoscrizioni di Azure autorizzate dall'utente possono distribuire e usare questa immagine. Il valore sarà "No" se lo SKU non è stato identificato come "anteprima".  |
| È contatto promozionale Opt-In| Customer| Il valore ti informerà se il cliente ha scelto in modo proattivo il contatto promozionale degli editori. Al momento l'opzione non è presentata ai clienti, pertanto è stato indicato "No" a livello generale. Dopo la distribuzione della funzionalità, si procederà all'aggiornamento dovuto.|
| Marketplace License Type| JSON| Metodo di fatturazione dell'offerta del Marketplace dell'utente.||
| Marketplace License Type: Billed Through Azure| JSON| Microsoft è l'agente per questa offerta del Marketplace dell'utente ed emette le fatture al cliente per conto dell'utente. Il pagamento avviene con l'uso di carta di credito per pagamento in base al consumo o fattura aziendale.||
| Marketplace License Type: Bring Your Own License | JSON| La macchina virtuale richiede un codice di licenza fornito dal cliente per la distribuzione. Microsoft non fattura ai clienti per elencare le loro offerte in questo modo attraverso il mercato.||
| Marketplace License Type: Free| JSON| L'offerta è configurata per essere gratuita per tutti gli utenti. Microsoft non fattura ai clienti l'utilizzo di questa offerta.||
| Marketplace License Type: Microsoft as Reseller  | JSON| Microsoft è il rivenditore di questa offerta del Marketplace dell'utente.|  |
| ID sottoscrizione Marketplace | Cliente, Ordine | Identificatore univoco associato alla sottoscrizione di Azure usato dal cliente per acquistare l'offerta Marketplace. L'ID era in precedenza il GUID della sottoscrizione di Azure.ID was formerly the Azure Subscription GUID.||
| Nome offerta  | JSON| Nome dell'offerta Marketplace.|| |
| Tipo di offerta  | JSON| Tipo di offerta di Microsoft Marketplace.|||
| Offer Type: Managed Application  | Ordine | Usare il tipo di offerta dell'app di Azure: dell'app gestita quando sono necessarie le condizioni seguenti: si distribuisce una soluzione basata su sottoscrizione per il cliente usando una macchina virtuale o un'intera soluzione basata su IaaS.Use the Azure app: managed app offer type when the following conditions are required: You deploy either a subscription-based solution for your customer using either a VM or an entire IaaS-based solution. L'utente o il cliente richiede che la soluzione sia gestita da un partner. |
| Tipo di offerta: Applicazione Azure| Ordine | Usare il tipo di offerta del modello di soluzione Applicazione di Azure quando la soluzione richiede ulteriore automazione della distribuzione e della configurazione oltre a una macchina virtuale semplice.||
| Tipo di offerta: Servizio di consulenza| JSON| I Servizi di consulenza in Azure Marketplace consentono di connettere i clienti con servizi per supportare ed estendere l'uso di Azure.| |
| Tipo di offerta: Contenitore | JSON| Usare il tipo di offerta Contenitore quando la soluzione è un'immagine del contenitore Docker di cui è stato effettuato il provisioning come un servizio Azure Container basato su Kubernetes.||
| Tipo di offerta: Dynamics 365 Business Central| JSON| Utilizzare questo tipo di offerta quando la soluzione è integrata con Dynamics 365 for Finance and Operations| |
| Tipo di offerta: Dynamics 365 per il coinvolgimento dei clienti | JSON| Utilizza questo tipo di offerta quando la soluzione è integrata con Dynamics 365 for Customer Engagement.||
| Tipo di offerta: Modulo Edge IoT | JSON| I moduli di Azure IoT Edge sono le unità di calcolo più piccole gestite da IoT Edge e possono contenere servizi Microsoft (ad esempio Azure Stream Analytics), servizi di terze parti o il codice specifico della soluzione. |
| Tipo di offerta: Applicazione Power BI | JSON| Usare il tipo di offerta Applicazione Power BI quando si distribuisce un'applicazione integrata con Power BI.|  |
| Tipo di offerta: Applicazione SaaS| JSON| Usare il tipo di offerta di app SaaS per consentire ai clienti di acquistare la soluzione tecnica basata su SaaS come una sottoscrizione.||
| Tipo di offerta: Macchina virtuale | JSON| Usare il tipo di offerta per le macchine virtuali quando si distribuisce un'appliance virtuale all'abbonamento associato al cliente.||
| Tipo di offerta: Visual Studio Marketplace Extension  | JSON| Tipo di offerta precedentemente disponibile per gli sviluppatori di estensioni DevOps di Azure.Offer type previously available to Azure DevOps extension developers. Gli sviluppatori di estensioni DevOps di Azure possono vendere l'estensione direttamente ai clienti. Le offerte di estensione possono essere configurate come a pagamento o includendo una versione di prova. |
| Order Cancel Date| JSON| Data in cui l'ordine del Marketplace è stato eliminato.||
| ID dell'ordine| JSON| Identificatore univoco dell'ordine cliente per il servizio Marketplace. Le offerte basate sull'utilizzo di macchine virtuali non sono associate a un ordine.| |
| Order Purchase Date| JSON| Data di creazione dell'ordine del Marketplace.|||
| Order Status| JSON| Lo stato di un ordine del Marketplace al momento dell'ultimo aggiornamento dei dati.|     |
| Order Status: Active  | JSON| Il cliente ha acquistato un ordine e non ha annullato l'ordine.|         |
| Stato dell'ordine: annullato | JSON| Il cliente ha precedentemente acquistato un ordine e successivamente ha annullato l'ordine.||
| Provider Email| Customer| Indirizzo di posta elettronica del provider coinvolto nella relazione tra Microsoft e il cliente finale. Se il cliente è un Enterprise tramite Rivenditore, questo sarà il rivenditore. Se è coinvolto un Cloud Solution Provider (CSP), questo sarà il CSP.|
| Provider Name| Customer| Nome del provider coinvolto nella relazione tra Microsoft e il cliente finale. Se il cliente è un Enterprise tramite Rivenditore, questo sarà il rivenditore. Se è coinvolto un Cloud Solution Provider (CSP), questo sarà il CSP.|
| SKU| JSON| Nome dello SKU come definito durante la pubblicazione. Un'offerta può includere molti SKU, ma uno SKU può essere associato solo a una singola offerta.||
| Trial End Date| JSON| Data di scadenza del periodo di valutazione per questo ordine.||

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel Marketplace commerciale del Centro per i partner, vedere Analisi per il Marketplace commerciale nel Centro per i [partner.](./analytics.md)
- Per grafici, tendenze e valori dei dati aggregati che riepilogano l'attività del marketplace per la tua offerta, vedi [Dashboard di riepilogo nell'analisi](./summary-dashboard.md)di Commercial Marketplace.
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Orders Dashboard in Commercial Marketplace analytics](./orders-dashboard.md).
- Per la macchina virtuale (VM) offre l'utilizzo e le metriche di fatturazione a consumo, vedere [Dashboard di utilizzo nell'analisi](./usage-dashboard.md)di Commercial Marketplace.
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi](./customer-dashboard.md)di Commercial Marketplace .
- Per un elenco delle richieste di download negli ultimi 30 giorni, vedere [Download Dashboard in Commercial Marketplace analytics](./downloads-dashboard.md).
- Per visualizzare una visualizzazione consolidata dei commenti e suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Valutazioni e recensioni del dashboard nell'analisi](./ratings-reviews.md)di Commercial Marketplace.
