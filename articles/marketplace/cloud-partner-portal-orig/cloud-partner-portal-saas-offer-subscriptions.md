---
title: Vendita SaaS tramite Azure | Microsoft Docs
description: Descrive il modello di fatturazione della sottoscrizione per le applicazioni SaaS e come implementarlo.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808097"
---
<a name="saas---sell-through-azure"></a>Vendita di app SaaS tramite Azure
=========================

Questo articolo descrive il modello di fatturazione della sottoscrizione per le applicazioni SaaS e come implementarlo nel portale Cloud Partner.


<a name="overview"></a>Panoramica
--------

Azure Marketplace consente di pubblicare e monetizzare le offerte SaaS. È ora possibile addebitare importi ai clienti direttamente nella fatturazione di Azure e inviare un'unica fattura di Azure per tutti i servizi e le risorse attivati. Per quanto riguarda la pubblicazione, le sottoscrizioni SaaS hanno i vantaggi seguenti:

-   **Esperienza di pubblicazione coerente**: se si pubblicano già offerte SaaS o qualsiasi altra offerta in Azure Marketplace, l'esperienza di pubblicazione avviene attraverso lo stesso portale di pubblicazione.
-   **Nuova vetrina espositiva**: tutte le offerte pubblicate saranno visibili sia nella vetrina esterna di Azure Marketplace, sia nell'estensione di Azure Marketplace all'interno del portale di Azure.
-   **Fatturazione integrata**: è ora possibile vendere in tutte le aree di vendita di Azure e affidarsi alle funzionalità di fatturazione di Azure.
-   **Generazione di lead integrata**: è ora possibile ricevere lead automaticamente da Azure nel sistema CRM preferito ogni volta che un utente di Azure sottoscrive il servizio SaaS tramite Azure Marketplace.
-   **Co-selling con i venditori Microsoft**: è possibile ottenere l'idoneità per la condivisione bidirezionale di lead, la presentazione prioritaria nel catalogo e l'opportunità di interagire e concludere trattative comuni con i venditori Microsoft.

<a name="billing-models"></a>Modelli di fatturazione
--------------

L'unico modello di fatturazione attualmente supportato è una tariffa fissa mensile per ogni sottoscrizione del servizio SaaS.

**Nota:** in futuro potrebbero essere disponibili altri modelli di fatturazione.

Ogni offerta SaaS può avere uno o più piani, ad esempio Basic o Premium. A ogni piano sono associati alcuni metadati di base e il prezzo del piano.

Il partner ha il pieno controllo sulla definizione di un piano. Ad esempio, cosa include un piano Basic? Il piano viene definito dal partner che può indicare il testo necessario per descriverlo nell'ambito della pubblicazione del piano.

Il prezzo associato al piano è la tariffa fissa mensile che gli utenti di Azure pagheranno per usare il servizio.

### <a name="what-is-not-supported-today"></a>Cosa non è attualmente supportato?

-   Fatturazione basata su unità personalizzate: ad esempio, un prezzo stabilito in base al numero di richieste.
-   Fatturazione basata sull'allocazione di postazioni: ad esempio, consentire agli utenti di Azure di acquistare licenze in base al numero di utenti.

<a name="end-to-end-flow"></a>Flusso end-to-end
---------------

Flusso dell'offerta di sottoscrizione SaaS dal punto di vista dell'utente finale

**Nota:** nella descrizione di questo flusso si presuppone che sia stata pubblicata l'offerta SaaS in Azure Marketplace denominata "Contoso demo SaaS".

![Flusso di sottoscrizione SaaS per l'utente](media/saas-offer-subscription/saas-subscription-user-flow.png)

Un utente di Azure può avere le interazioni seguenti con il servizio SaaS:

-   Individuare il servizio SaaS in Azure Marketplace
-   Sottoscrivere il servizio SaaS in Azure
-   Passare al servizio SaaS dal portale di Azure
-   Creare un account nel servizio SaaS e gestire (modifica del piano/eliminazione) l'account in SaaS
-   Annullare la sottoscrizione al servizio SaaS dal portale di Azure

<a name="discover-your-saas-service-in-azure-marketplace"></a>Individuare il servizio SaaS in Azure Marketplace
-----------------------------------------------

All'avvio di Azure Marketplace nel portale di Azure, gli utenti vedranno una categoria denominata "Software as a service (SaaS)".

![Individuare SaaS tramite il menu delle categorie](media/saas-offer-subscription/saas-category-menu.png)

Gli utenti possono anche cercare il servizio SaaS.

![Individuare SaaS tramite la ricerca](media/saas-offer-subscription/saas-cpp-search.png)

Gli utenti possono quindi visualizzare i dettagli del servizio e fare clic su **Crea**.

![Creazione di una sottoscrizione SaaS](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>Sottoscrivere il servizio SaaS in Azure

L'utente può quindi sottoscrivere il servizio SaaS da Azure.

![Sottoscrizione a un servizio SaaS](media/saas-offer-subscription/saas-subscribe-signup.png)

Al momento della sottoscrizione al servizio SaaS, l'utente immette le informazioni seguenti

-   Nome: il nome che gli utenti possono usare per individuare o gestire questa istanza della sottoscrizione SaaS in Azure.
-   Sottoscrizione: il contesto di sottoscrizione a cui associare la fatturazione per il servizio SaaS.
-   Piano: il piano del servizio SaaS da sottoscrivere.

Il documento Condizioni per l'utilizzo incluso nella procedura di pubblicazione dell'offerta viene presentato all'utente prima della sottoscrizione al servizio SaaS.

L'utente può ora sottoscrivere il servizio facendo clic su **Sottoscrivi**.
Al termine della sottoscrizione, Azure invia una notifica nel portale.

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Passare al servizio SaaS dal portale di Azure

Gli utenti quindi fanno clic su **Vai alla risorsa** per visualizzare o gestire l'istanza della sottoscrizione SaaS.

![Visualizzare o gestire l'istanza SaaS](media/saas-offer-subscription/saas-go-to-resource.png)

All'utente viene chiesto di configurare prima di tutto l'account nel servizio SaaS. La fatturazione inizierà dopo che il servizio SaaS avrà chiesto ad Azure di avviare la fatturazione, ovvero quando l'utente crea un account nel sito del servizio SaaS.

![Configurare l'istanza SaaS](media/saas-offer-subscription/saas-configure-account.png)

Quando l'utente fa clic su **Configura account** verrà reindirizzato all'endpoint del servizio SaaS. Durante questo reindirizzamento, viene passato un token come parametro di query. Ad esempio: 

![Token dell'account SaaS](media/saas-offer-subscription/saas-account-token.png)

Si noti il valore del token. Il token è di breve durata e deve essere risolto per ottenere un identificatore di sottoscrizione in Azure.

<a name="creating-a-saas-offer-subscription"></a>Creazione della sottoscrizione di un'offerta SaaS
----------------------------------

Per creare questa esperienza è necessario completare due passaggi:

-   Connettere il sito Web del servizio SaaS alle API SaaS Microsoft\'. Il documento [Vendita SaaS tramite Azure - API](./cloud-partner-portal-saas-subscription-apis.md) illustra come creare questa connessione.  
-   Abilitare **Sell through Azure** (Vendi tramite Azure) nella sezione **Technical Info** (Informazioni tecniche) del portale Cloud Partner e specificare tutti i dettagli di configurazione.

![Informazioni tecniche relative alla nuova offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

Di seguito è riportata una spiegazione di tutti i campi obbligatori nella sezione **Technical Info** (Informazioni tecniche):

|  **Campi offerta**                 |  **Descrizione**                                   |
|  ----------------                 |  -------------------------------------             |
| Preview Subscription IDs (ID sottoscrizione di anteprima)          | Tutti gli identificatori della sottoscrizione di Azure usati per testare l'offerta in anteprima prima che sia pubblicamente disponibile. |
| Getting Started Instructions (Istruzioni per le attività iniziali)      | Indicazioni da condividere con i clienti per aiutarli a connettersi all'app SaaS. È consentito l'uso di codice HTML di base, ad esempio tag quali `<p>`, `<h1>`, `<li>` e così via.  |
| Landing Page URL (URL della pagina di destinazione)                  | L'URL del sito al quale verranno indirizzati i clienti dopo l'acquisto nel portale di Azure. Questo URL sarà anche l'endpoint che riceverà le API di connessione che agevolano l'attività commerciale con Microsoft.  |
| Connection Webhook (Webhook di connessione)                | Per tutti gli eventi asincroni che Microsoft deve inviare al partner per conto del cliente, ad esempio una sottoscrizione di Azure non più valida, è necessario specificare un webhook di connessione. Se non si ha un sistema di webhook, la configurazione più semplice è costituita da un'app per la logica con endpoint HTTP che rimane in ascolto degli eventi inseriti e li gestisce in modo appropriato.  Per altre informazioni, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP in app per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint). |
| Azure AD Tenant ID (ID tenant di Azure AD) e Azure AD App ID (ID app di Azure AD)     | All'interno del portale di Azure è necessario creare un'app Active Directory in modo che la connessione tra i due servizi sia protetta da una comunicazione autenticata. Per questi campi, creare un'app Active Directory e inserire l'ID tenant e l'ID app corrispondenti. |
|  |  |


Infine, se si seleziona **Sell through Azure** (Vendi tramite Azure) verrà visualizzata una sezione aggiuntiva denominata **Plans** (Piani). I piani sono necessari solo se è stata selezionata l'opzione relativa alla vendita tramite Azure. Questa sezione elenca i piani specifici e i prezzi corrispondenti supportati dall'app SaaS. Al momento sono consentiti prezzi mensili, con la possibilità di offrire 1 o 3 mesi di accesso gratuito. Questi piani e prezzi devono corrispondere esattamente ai piani e ai prezzi indicati nel sito dell'app SaaS del partner.
