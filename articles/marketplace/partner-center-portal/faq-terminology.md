---
title: Domande frequenti sull'analisi per il marketplace commerciale e terminologia - Centro per i partner
description: Risposte alle domande frequenti sull'analisi per il marketplace commerciale nel Centro per i partner. Questo articolo include un dizionario di dati per la terminologia di analisi.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: eb91e498fa757c5cec7bd466c60aaf7e8758304c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744573"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Terminologia di analisi per il marketplace commerciale e domande comuni

Questo articolo risponde alle domande frequenti sui messaggi di analisi nel Centro per i partner e fornisce anche un dizionario della terminologia di analisi.

## <a name="common-questions"></a>Domande frequenti

**Non è possibile visualizzare i dati analitici nel Centro per i partner. Quando si accede a queste pagine, viene visualizzato il messaggio seguente. Perché?**

![È possibile che non ci siano ancora dati per le offerte o che l'analisi per il tipo di offerta non sia stata migrata al Centro per i partner](./media/analytics-faq-no-data.png)

Perché può essere visualizzato questo messaggio:

- Non esistono attualmente acquisti per le offerte pubblicate nel marketplace. Questo può significare che le offerte sono disponibili nel marketplace e ottengono visualizzazioni dai clienti nelle pagine di visualizzazione dei prodotti, ma i clienti non hanno ancora intrapreso alcuna azione per acquistarle e distribuirle.
- La pubblicazione dell'offerta può essere in corso e non ancora attiva. Solo le offerte già attive possono essere acquistate dai clienti. Per verificare lo stato delle offerte, vedere Panoramica nel [dashboard di analisi](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Per altre informazioni, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Le offerte possono essere presentate come **Contattami**, ovvero si tratta di offerte visualizzate per la presentazione che non possono essere acquistate dai clienti nel marketplace. Anche se queste offerte generano lead e vengono condivise, non vengono creati ordini per tali offerte, in quanto non possono essere acquistate. Per controllare il tipo di presentazione dell'offerta, passare alla pagina di configurazione.

**Anche se sono disponibili dati di analisi, viene visualizzato il messaggio seguente:**

![Non sono presenti dati durante l'intervallo di date selezionato](./media/analytics-faq-data-range.png)

Se si riceve questo messaggio, sono presenti dati analitici, ma non per l'intervallo di date selezionato. Selezionare un intervallo di date diverso o uno personalizzato in modo da visualizzare tutti i dati a partire dal 2010. Per altre informazioni, vedere la sezione Intervallo di date in [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Dizionario dei termini relativi ai dati

| Nome attributo | Report | Definizione|
|---|---|---|
| Azure License Type | Cliente, ordine | Tipo di contratto di licenza usato dai clienti per acquistare Azure. È noto anche come canale |
| Tipo di licenza di Azure: Provider di soluzioni cloud | Cliente, ordine | Il cliente finale ottiene Azure e l'offerta del Marketplace dell'utente tramite il Cloud Solution Provider, che agisce come rivenditore.|
| Tipo di licenza di Azure: Enterprise | Cliente, ordine | Il cliente finale ottiene Azure e l'offerta del Marketplace dell'utente tramite un contratto Enterprise Agreement firmato direttamente con Microsoft.|
| Tipo di licenza di Azure: Enterprise tramite rivenditore  | Cliente, ordine | Il cliente finale ottiene Azure e l'offerta nel marketplace tramite un rivenditore che ne semplifica il Contratto Enterprise con Microsoft.|  |
| Tipo di licenza di Azure: Pagamento a consumo| Cliente, ordine | Il cliente finale ottiene Azure e l'offerta nel marketplace tramite un contratto con pagamento in base al consumo firmato direttamente con Microsoft.||
| Nome dell'istanza cloud| Ordine| Microsoft Cloud in cui sono state distribuite le macchine virtuali.||
| Nome istanza cloud: Azure Global| JSON| Cloud Microsoft globale pubblico.|| |
| Nome istanza cloud: Azure Government | Ordine| Cloud Microsoft specifici per enti pubblici per uno degli enti pubblici seguenti: Cina, Germania o Stati Uniti.| |
| Città del cliente| Customer| Nome della città fornito dal cliente. La città può essere diversa da quella indicata nella sottoscrizione di Azure di un cliente.||
| Customer Communication Language  | Customer| Lingua di comunicazione preferita dal cliente.||
| Customer Company Name | Cliente, ordine | Nome dell'azienda fornito dal cliente. Il nome può essere diverso da quello indicato nella sottoscrizione di Azure di un cliente.|  |
| Paese/area geografica del cliente | Cliente, ordine | Nome del paese/area geografica fornito dal cliente. Il paese/area geografica può essere diverso da quello indicato nella sottoscrizione di Azure di un cliente.|  |
| Customer Email| Customer| Indirizzo di posta elettronica fornito dal cliente finale. L'indirizzo di posta elettronica può essere diverso da quello indicato nella sottoscrizione di Azure di un cliente.||
| Customer First Name| Customer| Nome fornito dal cliente. Il nome può essere diverso da quello fornito nella sottoscrizione di Azure di un cliente.| |
| ID cliente | Cliente, ordine | Identificatore univoco assegnato a un cliente. Un cliente può avere nessuna o più sottoscrizioni di Azure Marketplace.|  |
| Customer Postal Code  | Customer| Codice postale fornito dal cliente. Il codice postale può essere diverso da quello fornito nella sottoscrizione di Azure di un cliente.| |
| Customer State| Customer| Stato (indirizzo) fornito dal cliente. Lo stato può essere diverso da quello fornito nella sottoscrizione di Azure di un cliente.| |
| Date Acquired| Customer| Prima data in cui il cliente ha acquistato qualsiasi offerta pubblicata.| |
| Date Lost| Customer| Ultima data in cui il cliente ha annullato l'ultima delle offerte acquistate in precedenza.||
| Nuovo cliente  | JSON| Il valore identificherà un nuovo cliente che acquista (o no) una o più offerte per la prima volta. Il valore sarà "Sì" se l'acquisto rientra nello stesso mese del calendario per "Data acquisizione". Il valore sarà "No" Se il cliente ha acquistato una delle offerte prima del mese del calendario indicato. |
| Anteprima SKU| Ordine| Il valore indica se lo SKU è stato contrassegnato come "Anteprima". Il valore sarà "Sì" se lo SKU è stato contrassegnato di conseguenza e solo le sottoscrizioni di Azure autorizzate dal partner possono distribuire e usare questa immagine. Il valore sarà "No" se lo SKU non è stato identificato come "Anteprima".  |
| Is Promotional Contact Opt In (Consenso esplicito contatti promozionali)| Customer| Indica se il cliente ha acconsentito esplicitamente ai contatti promozionali da parte degli editori. Al momento l'opzione non è presentata ai clienti, pertanto è stato indicato "No" a livello generale. Dopo la distribuzione della funzionalità, si procederà all'aggiornamento dovuto.|
| Marketplace License Type| Ordine| Metodo di fatturazione dell'offerta del Marketplace dell'utente.||
| Tipo di licenza Marketplace: Fatturazione con Azure| Ordine| Microsoft è l'agente per questa offerta del Marketplace dell'utente ed emette le fatture al cliente per conto dell'utente. Il pagamento avviene con l'uso di carta di credito per pagamento in base al consumo o fattura aziendale.||
| Tipo di licenza Marketplace: Bring Your Own License | Ordine| Per poter essere distribuita, la macchina virtuale richiede un codice di licenza fornito dal cliente. Microsoft non addebita ai clienti la presentazione delle proprie offerte in questo modo tramite il marketplace.||
| Tipo di licenza Marketplace: Gratuito| JSON| L'offerta è configurata come gratuita per tutti gli utenti. Microsoft non addebita ai clienti l'utilizzo di questa offerta.||
| Tipo di licenza Marketplace: Microsoft come rivenditore  | Ordine| Microsoft è il rivenditore di questa offerta del Marketplace dell'utente.|  |
| ID sottoscrizione Marketplace | Cliente, ordine | Identificatore univoco associato alla sottoscrizione di Azure usata dal cliente per acquistare l'offerta nel marketplace. L'ID è stato in precedenza il GUID della sottoscrizione di Azure.||
| Nome offerta  | JSON| Nome dell'offerta nel marketplace.|| |
| Tipo di offerta  | Ordine| Tipo di offerta in Microsoft Marketplace.|||
| Tipo di offerta: Applicazione gestita  | Ordine | Usare l'app di Azure: tipo di offerta di app gestita quando sono necessarie le condizioni seguenti: Si distribuisce una soluzione basata su sottoscrizione per i clienti che usano una macchina virtuale o un'intera soluzione basata su IaaS. L'editore o il cliente richiedono che la soluzione venga gestita da un partner. |
| Tipo di offerta: Applicazione di Azure| Ordine | Usare il tipo di offerta modello di soluzione per app Azure quando la soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una semplice macchina virtuale.||
| Tipo di offerta: Servizio di consulenza| Ordine| I Servizi di consulenza in Azure Marketplace consentono di connettere i clienti con servizi per supportare ed estendere l'uso di Azure.| |
| Tipo di offerta: Contenitore | Ordine| Usare il tipo di offerta Contenitore quando la soluzione è un'immagine del contenitore Docker di cui è stato effettuato il provisioning come un servizio Azure Container basato su Kubernetes.||
| Tipo di offerta: Dynamics 365 Business Central| Ordine| Usare questo tipo di offerta quando la soluzione è integrata con Dynamics 365 for Finance and Operations| |
| Tipo di offerta: Dynamics 365 for Customer Engagement | Ordine| Usare questo tipo di offerta quando la soluzione è integrata con Dynamics 365 for Customer Engagement.||
| Tipo di offerta: Modulo IoT Edge | Ordine| I moduli Azure IoT Edge sono le unità di calcolo più piccole gestite da IoT Edge e possono includere servizi Microsoft, ad esempio Analisi di flusso di Azure, servizi di terze parti o il codice specifico per la soluzione. |
| Tipo di offerta: Applicazione Power BI | Ordine| Usare il tipo di offerta applicazione Power BI quando si distribuisce un'applicazione integrata con Power BI.|  |
| Tipo di offerta: Applicazione SaaS| Ordine| Usare il tipo di offerta di app SaaS per consentire ai clienti di acquistare la soluzione tecnica basata su SaaS come una sottoscrizione.||
| Tipo di offerta: Macchina virtuale | Ordine| Usare il tipo di offerta per le macchine virtuali quando si distribuisce un'appliance virtuale all'abbonamento associato al cliente.||
| Tipo di offerta: Visual Studio Marketplace Extension (Estensione Visual Studio Marketplace)  | Ordine| Tipo di offerta disponibile in precedenza per gli sviluppatori di estensioni Azure DevOps. In futuro gli sviluppatori di estensioni Azure DevOps potranno vendere la propria estensione direttamente ai clienti. Le offerte di estensioni possono essere configurate come a pagamento o con versione di valutazione inclusa. |
| Order Cancel Date| Ordine| Data in cui l'ordine del Marketplace è stato eliminato.||
| ID dell'ordine| Ordine| Identificatore univoco dell'ordine del cliente per il servizio marketplace. Le offerte basate sull'utilizzo di macchine virtuali non sono associate a un ordine.| |
| Order Purchase Date| Ordine| Data di creazione dell'ordine nel marketplace.|||
| Order Status| Ordine| Lo stato di un ordine del Marketplace al momento dell'ultimo aggiornamento dei dati.|     |
| Stato ordine: Attivo  | Ordine| Il cliente ha acquistato un ordine e non l'ha annullato.|         |
| Stato ordine: Annullato | Ordine| Il cliente ha inizialmente acquistato un ordine e successivamente lo ha annullato.||
| Posta elettronica del provider| Customer| Indirizzo di posta elettronica del provider coinvolto nella relazione tra Microsoft e il cliente finale. Se il cliente è di tipo Enterprise tramite rivenditore, sarà il rivenditore. Se è coinvolto un provider di soluzioni cloud (CSP), sarà il CSP.|
| Provider Name| Customer| Nome del provider coinvolto nella relazione tra Microsoft e il cliente finale. Se il cliente è di tipo Enterprise tramite rivenditore, sarà il rivenditore. Se è coinvolto un provider di soluzioni cloud (CSP), sarà il CSP.|
| SKU| JSON| Nome dello SKU come definito durante la pubblicazione. Un'offerta può includere molti SKU, mentre uno SKU può essere associato a una sola offerta.||
| Trial End Date| JSON| Data di scadenza del periodo di valutazione per questo ordine.||

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dei report di analisi disponibili nel marketplace commerciale del Centro per i partner, vedere [Analisi per il marketplace commerciale nel Centro per i partner](./analytics.md).
- Per i grafici, le tendenze e i valori dei dati aggregati che riassumono l'attività del marketplace per l'offerta, vedere [Dashboard di riepilogo nell'analisi per il marketplace commerciale](./summary-dashboard.md).
- Per informazioni sugli ordini in un formato grafico e scaricabile, vedere [Dashboard degli ordini nell'analisi per il marketplace commerciale](./orders-dashboard.md).
- Per informazioni sull'utilizzo di offerte con macchine virtuali (VM) e sull'analisi della fatturazione a consumo, vedere [Dashboard di utilizzo nell'analisi per il marketplace commerciale](./usage-dashboard.md).
- Per informazioni dettagliate sui clienti, incluse le tendenze di crescita, vedere [Dashboard dei clienti nell'analisi per il marketplace commerciale](./customer-dashboard.md).
- Per un elenco delle richieste di download degli ultimi 30 giorni, vedere [Dashboard dei download nell'analisi per il marketplace commerciale](./downloads-dashboard.md).
- Per una visualizzazione consolidata dei commenti e suggerimenti dei clienti per le offerte in Azure Marketplace e AppSource, vedere [Dashboard delle classificazioni e recensioni nell'analisi per il marketplace commerciale](./ratings-reviews.md).
