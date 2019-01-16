---
title: Cortana Intelligence | Microsoft Docs
description: Pubblicazione di un'offerta Cortana Intelligence.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: a6357640ceeb7c5f1bde4eea85aa5265990935f4
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157157"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>Pubblicare un'offerta Cortana Intelligence tramite il portale Cloud Partner

Questo articolo descrive come pubblicare un'offerta Cortana Intelligence tramite il portale Cloud Partner.

## <a name="prerequisites"></a>Prerequisiti

Il portale Cloud Partner supporta l'accesso al portale in base al ruolo, il che consente ai collaboratori di collaborare alla pubblicazione di un'offerta. Per altre informazioni, consultare [Gestione utenti nel portale Cloud Partner](./cloud-partner-portal-manage-users.md).

Prima di poter pubblicare un'offerta per conto di un account server di pubblicazione, uno degli utenti con ruolo \"proprietario\" deve accettare di essere conforme a: [Condizioni per l'utilizzo](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Informativa sulla privacy di Microsoft](https://www.microsoft.com/privacystatement/default.aspx) e [Contratto relativo al programma Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>Per iniziare a creare un'offerta Cortana Inteligence

Una volta soddisfatti tutti i prerequisiti, è possibile avviare la creazione dell'offerta Cortana Inteligence.

1.  Accedere al [portale Cloud Partner](http://cloudpartner.azure.com/).
2.  Selezionare **+ Nuova offerta** dalla barra di spostamento a sinistra.
3. Selezionare **Cortana Intelligence**.
4. Selezionare la scheda **Editor** nella vista **Nuove offerte** per configurare le opzioni seguenti:
    -   Impostazioni dell'offerta
    -   Informazioni tecniche
    -   Dati da visualizzare nella vetrina
    -   Contatti

    Per ciascuna delle opzioni precedenti, verrà visualizzato un modulo da compilare.  I campi obbligatori di ciascun modulo sono contrassegnati con un asterisco rosso (\*).

## <a name="to-configure-offer-settings"></a>Per configurare le Impostazioni dell'offerta

Le Impostazioni dell'offerta forniscono informazioni di base sull'offerta, ad esempio l'ID offerta, l'ID server di pubblicazione e il nome dell'offerta.

### <a name="offer-id"></a>Offer ID (ID offerta)

Identificatore univoco dell'offerta in un profilo di pubblicazione.
Questo ID è visibile negli URL dei prodotti. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere al massimo 50 caratteri. 
>[!Note]
>Questo campo è bloccato dopo la pubblicazione dell'offerta.

**Esempio:**

Se il server di pubblicazione **contoso** crea un'offerta con ID offerta *sample-Cortana Intelligence*, l'offerta verrà visualizzata in AppSource come "https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-Cortana Intelligence*?tab=Overview".

### <a name="publisher-id"></a>Publisher ID (ID editore)

Un elenco a discesa dei profili di pubblicazione. Consente di scegliere il profilo di pubblicazione in cui si desidera pubblicare l'offerta.

>[!Note]
>Questo campo è bloccato dopo la pubblicazione dell'offerta.

### <a name="name"></a>NOME

Il nome visualizzato dell'offerta. Questo nome viene visualizzato in [AppSource](https://appsource.microsoft.com). Può contenere massimo 50 caratteri.

Una volta fornite le informazioni necessarie per Impostazioni dell'offerta, selezionare **Salva** per passare alla sezione Informazioni tecniche dell'offerta. opzione.

## <a name="to-configure-technical-info"></a>Per configurare Informazioni tecniche

Consente di fornire informazioni tecniche che verranno visualizzate nella pagina dell'offerta. In questa vista sono presenti i seguenti campi.

### <a name="partner-mpn-id"></a>ID Partner MPN

Se si è partner Microsoft registrato, andare sul [sito Web partner](https://partners.microsoft.com/) e accedere per ottenere l'ID Partner dell'organizzazione. Immettere l'ID nel campo **ID Partner MPN**.

### <a name="analytics-components-used"></a>Componenti di Analytics usati

Selezionare tutti i componenti usati per l'offerta. Selezionare almeno 1 componente. Selezionare **Microsoft R** solo se si usa una delle licenze MRS dei servizi SQL 2016 R, HDInsight Premium, o MRS in esecuzione nelle macchine virtuali.

### <a name="additional-components-used"></a>Componenti aggiuntivi usati

Selezionare tutti gli altri componenti usati dalla soluzione.

### <a name="customer-name-using-solution"></a>Nome del cliente che usa la soluzione

Specifica il nome del cliente che usa questa soluzione nell'ambiente di produzione. 

>[!Note]
>Questa informazione non viene pubblicata in AppSource. Viene usata solo ai fini della valutazione della soluzione.

### <a name="azure-consumption-requirement-met"></a>Requisito di consumo di Azure soddisfatto?

Indica se la soluzione genera almeno 1000 $ al mese per ogni cliente di componenti della famiglia di prodotti Azure o se la soluzione usa Microsoft R.

>[!Note]
>Questa informazione non viene pubblicata in AppSource. Viene usata solo ai fini della valutazione della soluzione.

### <a name="demo-video-url"></a>URL del video dimostrativo

Fornisce l'URL del video dimostrativo per la soluzione/app che il team di pre-vendita può mostrare ai clienti. 

>[!Note]
>Questa informazione non viene pubblicata in AppSource. Viene usata solo ai fini della valutazione della soluzione.

#### <a name="demo-video-guidelines"></a>Linee guida per il video dimostrativo

- La durata del video deve essere inferiore a 15 minuti.
- Il video deve essere una registrazione un minimo editata che illustra le funzionalità della soluzione. 
- Il video illustra gli aspetti principali delle funzionalità destinate all'utente e si concentra sull'integrazione della funzione di analitica avanzata. 
- I video dimostrativi **non** saranno pubblicamente disponibili ai clienti, ma devono essere rappresentativi del modo in cui la soluzione *si presenterebbe* a un potenziale cliente. Di conseguenza, devono essere dotati di script e ripetibili.
- Per creare il video dimostrativo è possibile usare qualsiasi strumento di registrazione dello schermo che consente di esportare un formato video standard (ad esempio MPEG). 

Le istruzioni seguenti illustrano come creare un video usando Skype for Business. 

1. [Avviare una riunione](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [Condividere il desktop](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [Avviare la registrazione](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. Al termine della registrazione, [utilizzare Gestione registrazione per pubblicare la registrazione](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

Caricare il video registrato in un servizio che consente di generare un URL condiviso. Ad esempio, un [collegamento guest in OneDrive o SharePoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232).

### <a name="supported-regions"></a>Aree supportate

Selezionare tutte le aree supportate dalla soluzione. Selezionare almeno 1 area.

### <a name="power-bi-desktop-file-pbix"></a>File Power BI Desktop (.pbix)

Se applicabile, caricare un file .pbix. Assicurarsi che i dati vengano importati nel file e che non abbiano un riferimento esterno. Verrà creato il report incorporato per l'utente.

### <a name="solution-architecture"></a>Architettura della soluzione

Caricare un documento che descriva in dettaglio l'architettura della soluzione. Per le linee guida su come caricare il diagramma dell'architettura della soluzione, consultare il documento relativo al [modello di flusso di lavoro della soluzione di analisi avanzata](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx).

>[!Note]
>Questa informazione non viene pubblicata in AppSource. Viene usata solo ai fini della valutazione della soluzione.

### <a name="select-segments"></a>Selezionare i segmenti

Selezionare tutti i segmenti del settore correlato. Se nessun segmento in elenco è adatto per l'app, immettere il nome del segmento nel campo **Altri**. Il segmento può contenere un massimo di tre parole.

### <a name="select-business-processes"></a>Selezionare i processi aziendali

Selezionare i processi aziendali che descrivono al meglio la soluzione. Se nessuno dei processi in elenco è adatto per l'app, immettere il nome del processo nel campo **Altri**. Il processo può contenere un massimo di tre parole.

### <a name="trial-info"></a>Informazioni sulla versione di valutazione

-   **URL versione di valutazione SaaS:** immettere l'URL per la versione di valutazione dell'app.
-   **URL versione di valutazione test drive:** immettere l'URL per la versione test drive dell'app.

Per altre informazioni sulle versioni di valutazione, consulta **Tipo di app** nella sezione successiva di questo articolo.

Una volta fornite le informazioni necessarie per Informazioni tecniche, selezionare **Salva** per passare alla sezione Dati da visualizzare nella vetrina dell'offerta. 

## <a name="to-configure-storefront-details"></a>Per configurare Dati da visualizzare nella vetrina

### <a name="offer-summary"></a>Riepilogo offerta

Il riepilogo della proposta di valore dell'offerta, Verrà visualizzato nella pagina di ricerca dell'offerta. La lunghezza massima del riepilogo è di 100 caratteri.

### <a name="offer-description"></a>Descrizione dell'offerta

La descrizione che verrà visualizzata nella pagina dei dettagli dell'app.
La lunghezza massima della descrizione è di 1300 caratteri.

Si noti che in questo campo è possibile immettere contenuto html con tag quali \<p\>, \<h1\>, \<h2\>, \<li\>, ecc. Ciò consente di rendere il contenuto molto più presentabile. Il team del portale di pubblicazione sta lavorando per aggiungere una funzionalità che consenta all'utente di visualizzare un'anteprima dei dati da visualizzare nella vetrina, in modo da rendere sempre il contenuto più presentabile. Nel frattempo, per scoprire come verrà visualizzata la descrizione, è possibile usare qualsiasi strumento online html in tempo reale, ad esempio [http://htmledit.squarefree.com](http://htmledit.squarefree.com/).

Nel formato consigliato della descrizione, il testo viene suddiviso in sottosezioni in base alle proposte di valore, ognuna con una sottointestazione evidenziata. I visitatori in genere danno un'occhiata al campo di riepilogo dell'offerta e alle sottointestazioni per avere un'idea dell'app e capire in un attimo perché potrebbe fare al caso loro. È dunque importante catturare l'attenzione degli utenti e offrire loro un motivo per continuare a leggere le specifiche.

#### <a name="partner-examples"></a>Esempi di partner

- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview) (Ottimizzazione dell'inventario in Neal Analytics)
- [Plexure Retail Personalization](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>Settori

Selezionare il settore che corrisponde meglio all'app. Se l'app rientra in più settori, lasciare il campo vuoto.

### <a name="categories"></a>Categorie

Selezionare le categorie rilevanti per l'app. Selezionare un massimo di due categorie.

### <a name="app-type"></a>Tipo di app

Selezionare il tipo di versione di valutazione che verrà supportata dall'app in AppSource. Scegliere una delle versioni di valutazione seguenti:
- **Gratuita** significa che l'app è gratuita.
- **Versione di valutazione** significa che i clienti possono provare l'app le per un periodo specificato.
- **Richiesta per la versione di valutazione** significa che i clienti possono richiedere una versione di valutazione dell'app.

I partner possono offrire due tipi di versione di valutazione in AppSource.

- L'opzione **Versione di valutazione**, denominata anche **Customer Led Trials (CLT)** può essere uno dei tipi seguenti: 
    - Versione di valutazione SaaS
        - Il cliente può selezionare l'URL fornito dal creatore dell'offerta o dal suo partner. Il cliente passa attraverso l'accesso Single Sign-On federato ad Azure Active Directory per ottenere una esperienza di valutazione predefinita.
        - Si tratta di un'app con acceso Single Sign-On con multi-tenancy che impedisce ad altri utenti di avere accesso alla configurazione/ai dati di un utente. Oppure questa esperienza fornisce solo un subset che usa operazioni di sola lettura.

        **Esempi:**

        - [AFS POP Retail Execution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) (questa app offre un'esperienza curata con percorsi chiari per un set selezionato di utenti tipo).

     - Test drive
        - La soluzione (o un suo subset) del creatore dell'offerta (o del partner) può essere inclusa nel pacchetto usando i modelli di Azure Resource Manager per i quali AppSource crea un'istanza. AppSource può gestire l'app in una sottoscrizione partner in un tempo predefinito, mantenendo il pool di istanze a caldo/a freddo, ecc.
        - Sono inoltre disponibili modelli e codici di esempio che indirizzano nella scelta di questa opzione.

        **Esempi:**

        - [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) (Ottimizzazione dell'inventario in Neal Analytics)

- Scegliendo l'opzione **Richiesta per la versione di valutazione** (**Partner Led Trials/PLT**), ai clienti viene richiesto di compilare un modulo di informazioni di contatto per il follow-up del partner. Il partner si occupa del follow-up e offre una dimostrazione o prova dell'app. Per altre informazioni, guardare il video che illustra la [procedura dettagliata dell'esperienza di valutazione in AppSource](https://aka.ms/trialexperienceforwebapps) per una panoramica generale.

>[!Note]
>I dati mostrano che la capacità di **Customer Led Trials** di generare clienti potenziali è maggiore rispetto a **Partner Led Trials**.


### <a name="help-link-for-your-app"></a>Collegamento alle informazioni della Guida per l'app

Specificare un URL alla pagina che contiene le informazioni della Guida per l'app.

### <a name="supported-countriesregions"></a>Paesi/aree supportati

Questo campo specifica i paesi/le aree in cui sarà disponibile la versione di valutazione dell'offerta.

![Supported countries/regions (Paesi supportati)](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>Lingue supportate

Selezionare le lingue supportate dall'app. Se l'app supporta lingue che non sono presenti in questo elenco, pubblicare l'offerta e scrivere all'indirizzo <appsource@microsoft.com> per segnalare la necessità di supporto per altre lingue.

### <a name="app-version"></a>Versione dell'app

Immettere il numero di versione dell'app.

### <a name="products-your-app-works-with"></a>Prodotti interrelati con l'app

Elencare i prodotti specifici interrelati con l'app. È possibile elencare fino a 3 prodotti. Per elencare un prodotto, selezionare il **segno più** (accanto a nuovo) per creare un campo di testo aperto. Immettere il nome del prodotto interrelato con l'app.

### <a name="hide-key"></a>Chiave nascosta

Si tratta di una chiave combinata con l'URL di anteprima dell'offerta che impedisce la visualizzazione pubblica dell'offerta. Non è una password. È possibile immettere qualsiasi stringa alfanumerica.

### <a name="offer-logo-small"></a>Logo dell'offerta (piccolo)

Questo logo viene visualizzato nella pagina di ricerca dell'app. Il formato di immagine png è l'unico formato consentito. L'immagine deve avere dimensione di 48 x 48 pixel.

### <a name="offer-logo-large"></a>Logo dell'offerta (grande)

Questo logo viene visualizzato nella pagina dei dettagli dell'app. Il formato di immagine png è l'unico formato consentito. L'immagine deve avere dimensione di 48 x 48 pixel.

### <a name="video"></a>Video

È possibile caricare al massimo quattro video. Per ogni video che si desidera caricare:
- Specificare il nome del video
- Specificare l'URL (solo YouTube o Vimeo)
- Fornire un'anteprima da associare al video. L'anteprima deve essere in formato di immagine png e deve avere dimensione dii 1280 X 720 pixel. 

Per aggiungere nuovi video, selezionare **+ Nuovo**, come illustrato nella schermata successiva.

![Aggiungere un nuovo video](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>Documento

È possibile caricare un massimo di tre documenti. Ogni documento deve essere un file in formato PDF. Per creare un nuovo documento:

- Selezionare **+ Nuovo**
- Immettere il nome del documento
- Selezionare **Carica** per caricare il documento.

![Aggiungere un nuovo documento](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>Informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'app

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Immettere le condizioni per l'uso dell'app. Per ottenere la versione di valutazione dell'app, i clienti di AppSource devono accettare tali condizioni.

>[!Note]
>In questo campo è possibile immettere il contenuto che usa tag HTML, con tag quali < p\>, < h1\> e < li\>. È possibile usare questi tag per formattare il contenuto. 

### <a name="lead-destination"></a>Clienti potenziali destinatari

Selezionare un sistema CRM nel quale verranno archiviati i clienti potenziali. 

Selezionare **Tabella di Azure** se si usa uno dei sistemi CRM seguenti: Salesforce, Marketo o Microsoft Dynamics CRM. 

Per altre informazioni relative al sistema CRM che si vuole usare, selezionare uno dei seguenti collegamenti per i sistemi supportati.

-   [Tabella di Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
