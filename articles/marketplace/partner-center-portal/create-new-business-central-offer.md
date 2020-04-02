---
title: Creare una nuova offerta Dynamics 365 Business Central nel Marketplace commerciale
description: Come creare una nuova offerta Dynamics 365 Business Central per l'inserzione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) tramite il portale Di Business Commerciale nel Centro per i partner Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 908d0fb9d494ec048e6b254f91ec1faf56b47c90
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529411"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Creare una nuova offerta Dynamics 365 Business Central

In questo argomento viene illustrato come creare una nuova offerta Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) è un sistema di pianificazione delle risorse aziendali (ERP) che gestisce un'ampia gamma di processi aziendali, tra cui finanza, operazioni, supply chain, CRM e gestione dei progetti e commercio elettronico. I pacchetti Premium supportano anche il modello di distribuzione e la produzione classici. Tutte le offerte per Dynamics 365 Business Central devono seguire il processo di certificazione.

Per iniziare a creare offerte di Dynamics 365 Business Central, assicurati di creare prima [un account del Centro per i partner](./create-account.md) e aprire il dashboard Marketplace [commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)con la pagina **Panoramica** selezionata.

![Dashboard Marketplace commerciale nel Centro per i partner](./media/new-offer-overview.png)

>[!Note]
> Una volta pubblicata un'offerta, le modifiche apportate all'offerta effettuata nel Centro per i partner verranno aggiornate nel sistema e archiviate solo dopo la ripubblicazione. Assicurati di inviare l'offerta per la pubblicazione dopo aver apportato le modifiche.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

Seleziona il pulsante **Nuova offerta,** quindi seleziona la voce di menu **Dynamics 365 Business Central.** Verrà visualizzata la finestra di dialogo **Nuova offerta.**

### <a name="offer-id-and-alias"></a>ID e alias dell'offerta

- **ID offerta**: Identificatore univoco per ogni offerta nel tuo account. Questo ID sarà visibile ai clienti nell'indirizzo URL dell'offerta del marketplace e nei modelli di Azure Resource Manager (se applicabile). L'ID offerta deve essere carattere alfanumerico minuscolo (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti), limitato a 50 caratteri e non può essere modificato dopo aver selezionato **Crea**.  Ad esempio, se si immette *test-offer-1* in `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`questo campo, l'URL dell'offerta sarà .

- **Alias dell'offerta:** nome utilizzato per fare riferimento all'offerta all'interno del Centro per i partner. Questo nome non verrà utilizzato nel marketplace ed è diverso dal nome dell'offerta e da altri valori che verranno visualizzati ai clienti. Questo valore non può essere modificato dopo aver selezionato **Crea**.

Una volta inseriti **l'ID offerta** e **l'alias dell'offerta,** selezionare **Crea**. Potrai quindi lavorare su tutte le diverse parti della tua offerta.

## <a name="offer-setup"></a>Configurazione dell'offerta

Nella pagina **Impostazione offerta** vengono richieste le seguenti informazioni. Assicurarsi di selezionare **Salva** dopo aver completato questi campi.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Come vuoi che i potenziali clienti interagiscano con questa offerta?

Seleziona l'opzione che desideri utilizzare per questa offerta.

#### <a name="get-it-now-free"></a>Scaricalo ora (gratuito)

Elenca gratuitamente la tua offerta ai clienti fornendo un URL valido (a partire da *http* o *https)* da cui possono accedere alla tua app.  Ad esempio: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Versione di prova gratuita (elenco)

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (a partire da *http* o *https)* in cui è possibile ottenere una versione di valutazione.  Ad esempio `https://contoso.com/trial/my-app`. L'offerta che elenca le versioni di valutazione gratuite viene creata, gestita e configurata dal servizio e non dispone di sottoscrizioni gestite da Microsoft.Offer listing free trials are created, managed, and configured by your service and do not have subscriptions managed by Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento di valutazione possono essere usati solo per ottenere informazioni utente tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione tramite questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Raccogliere le informazioni di contatto del cliente collegando il sistema CRM (Customer Relationship Management). Al cliente verrà chiesto il permesso di condividere le proprie informazioni. Questi dettagli del cliente, insieme al nome dell'offerta, all'ID e all'origine del marketplace in cui hanno trovato l'offerta, verranno inviati al sistema CRM configurato. Per ulteriori informazioni sulla configurazione di CRM, vedere [Connect lead management](#connect-lead-management). 

### <a name="test-drive"></a>Test drive

Un test drive è un ottimo modo per mostrare la tua offerta ai potenziali clienti dando loro la possibilità di "provare prima di acquistare", con conseguente aumento della conversione e la generazione di lead altamente qualificati. [Ulteriori informazioni sui test drive.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Per abilitare un'unità di test, selezionare la casella **Abilita un'unità** di test. Sarà quindi necessario configurare un ambiente dimostrativo nella [configurazione tecnica](#test-drive-technical-configuration) del test drive per consentire ai clienti di provare l'offerta per un periodo di tempo fisso. 

#### <a name="type-of-test-drive"></a>Tipo di test drive

Selezionare una delle seguenti opzioni:

- **[Azure Resource Manager:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che si adattano a questo scenario usano solo le risorse di Azure.Products that fit this scenario use only Azure resources.
- **[Dynamics 365 for Business Central:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** Microsoft ospita e gestisce il servizio test drive (inclusi provisioning e distribuzione) per un sistema di pianificazione delle risorse aziendali di Business Central (finanza, operazioni, catena di fornitura, CRM e così via).  
- **[Dynamics 365 for Customer Engagement:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** Microsoft ospita e gestisce il servizio test drive (inclusi provisioning e distribuzione) per un sistema Customer Engagement (vendite, servizio, servizio di progetto, servizio sul campo e così via).  
- **[Dynamics 365 for Operations:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** Microsoft ospita e gestisce il servizio di test drive (inclusi provisioning e distribuzione) per un sistema di pianificazione delle risorse aziendali Finance and Operations (finanza, operazioni, produzione, supply chain e così via). 
- **[App per](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** la logica : un modello di distribuzione che comprende tutte le architetture di soluzioni complesse. Tutti i prodotti personalizzati devono utilizzare questo tipo di Test Drive.
- **[Power BI:](https://docs.microsoft.com/power-bi/service-template-apps-overview)** collegamento incorporato a un dashboard personalizzato. I prodotti che vogliono dimostrare un oggetto visivo interattivo di Power BI devono usare questo tipo di Test Drive. È sufficiente caricare l'URL di Power BI incorporato qui.

#### <a name="additional-test-drive-resources"></a>Risorse aggiuntive per il test drive

- [Procedure consigliate tecniche per test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Procedure consigliate per il marketing dei test](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Panoramica del test Su un cercapersone](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Gestione dei lead di Connect

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per ulteriori informazioni, vedere [Panoramica](./commercial-marketplace-get-customer-leads.md)della gestione dei lead .

Ricordati di **salvare** prima di passare alla sezione successiva!

## <a name="properties"></a>Proprietà

La pagina **Proprietà** consente di definire le categorie e i settori utilizzati per raggruppare l'offerta nel marketplace, la versione dell'app e i contratti legali che supportano l'offerta. Seleziona **Salva** dopo aver completato questa pagina.

### <a name="category"></a>Category

Seleziona un minimo di una e un massimo di tre categorie, che verranno utilizzate per inserire l'offerta nelle aree di ricerca del marketplace appropriate. Assicurati di sapere come la tua offerta supporta queste categorie nella descrizione dell'offerta. 

### <a name="industry"></a>Settore

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versione dell'app

Inserisci il numero di versione dell'offerta. I clienti vedranno questa versione elencata nella pagina dei dettagli dell'offerta.

### <a name="terms-and-conditions"></a>Termini e condizioni

Fornire i propri termini e condizioni legali nel campo **Termini e condizioni.** Puoi anche fornire l'URL in cui trovare i termini e le condizioni. I clienti saranno tenuti ad accettare questi termini prima di poter provare la tua offerta.

## <a name="offer-listing"></a>Inserzione dell'offerta

La pagina Dell'offerta mostra le lingue in cui verrà elencata l'offerta. Attualmente, **English (Stati Uniti)** è l'unica opzione disponibile.

Dovrai definire i dettagli del marketplace (nome dell'offerta, descrizione, immagini, ecc.) per ogni lingua/mercato. Selezionare la lingua/nome del mercato per fornire queste informazioni.

> [!NOTE]
> Il contenuto dell'elenco dell'offerta (come la descrizione, i documenti, le schermate, le condizioni per l'utilizzo, ecc.) non è necessario per essere in inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non in lingua inglese]". È inoltre accettabile fornire un *URL di collegamento utile* per offrire contenuti in una lingua diversa da quella utilizzata nel contenuto della scheda Offerta.

### <a name="name"></a>Nome

Il nome che inserisci qui verrà mostrato ai clienti come titolo della tua offerta. Questo campo viene prepopolato con il testo immesso per **l'alias dell'offerta** al momento della creazione dell'offerta, ma è possibile modificare questo valore. Questo nome può essere registrato (e l'utente può includere simboli di marchio o copyright). Il nome non può contenere più di 50 caratteri e non può includere emoji.

### <a name="short-description"></a>Breve descrizione

Fornisci una breve descrizione dell'offerta (fino a 100 caratteri), che può essere utilizzata nei risultati di ricerca del marketplace.

### <a name="description"></a>Descrizione

Fornisci una descrizione più lunga della tua offerta (fino a 3.000 caratteri). Questa descrizione verrà visualizzata ai clienti nella panoramica dell'elenco dei marketplace. Includi la proposta di valore della tua offerta, i vantaggi principali, le associazioni di categoria e/o di settore, le opportunità di acquisto in-app e le eventuali informazioni richieste. 

Alcuni suggerimenti per scrivere la descrizione:  

- Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includi i seguenti elementi nella tua proposta di valore:
  - Descrizione del prodotto
  - Il tipo di utente che beneficia del prodotto
  - Esigenze o dolore del cliente affrontate dal prodotto
- Tenere presente che le prime frasi possono essere visualizzate nei risultati dei motori di ricerca.  
- Per vendere un prodotto, non basarsi sulle funzionalità, ma concentrarsi sul valore che può offrire.  
- Usare il più possibile la terminologia specifica del settore o formule che mettano in evidenza i vantaggi offerti.
- Prendi in considerazione l'uso di tag HTML per formattare la descrizione e renderla più coinvolgente.

Per rendere più coinvolgente la descrizione dell'offerta, usa l'editor RTF per formattare la descrizione.

![Utilizzo dell'editor DI testo RTF](./media/text-editor2.png)

Utilizzare le istruzioni seguenti per utilizzare l'editor RTF:

- Per modificare il formato del contenuto, evidenziare il testo da formattare e selezionare uno stile di testo, come illustrato di seguito:

     ![Utilizzo dell'editor RTF per modificare il formato del testo](./media/text-editor3.png)

- Per aggiungere un elenco puntato o numerato al testo, utilizzare le opzioni seguenti:

     ![Utilizzo dell'editor RTF per aggiungere elenchi](./media/text-editor4.png)

- Per aggiungere o rimuovere il rientro del testo, utilizzare le opzioni seguenti:

     ![Utilizzo dell'editor RTF per il rientro](./media/text-editor5.png)

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Facoltativamente, puoi inserire fino a tre parole chiave di ricerca per aiutare i clienti a trovare la tua offerta nel marketplace. Per ottenere risultati ottimali, prova a utilizzare queste parole chiave anche nella tua descrizione.

### <a name="products-your-app-works-with"></a>Prodotti interrelati con l'app

Se vuoi informare i clienti che la tua app funziona con prodotti specifici, inserisci qui fino a tre nomi di prodotto.

### <a name="support-urls"></a>URL di supporto

Questa sezione ti consente di fornire link per aiutare i clienti a comprendere meglio la tua offerta.

#### <a name="help-link"></a>Collegamento alla Guida

Inserisci l'URL in cui i clienti possono saperne di più sulla tua offerta.

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'app sia conforme alle leggi e ai regolamenti sulla privacy e di fornire una valida informativa sulla privacy.

### <a name="contacts"></a>Contatti

In questa sezione è necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono di un **contatto di supporto** e di un contatto **Engineering**. Queste informazioni non vengono mostrate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP.

Nella sezione **Contatto supporto** è inoltre necessario fornire l'URL del **supporto** in cui i partner CSP possono trovare supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire qui almeno un (e fino a tre) documenti di marketing correlati, ad esempio white paper, brochure, elenchi di controllo o presentazioni. Questi documenti devono essere in formato .pdf.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione, è possibile fornire loghi e immagini che verranno utilizzati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato PNG.

#### <a name="store-logos"></a>Loghi del Negozio

Fornisci il logo della tua offerta in due formati: **Piccolo (48 x 48)** e **Grande (216 x 216)**.

#### <a name="hero"></a>Banner

L'immagine dell'eroe è facoltativa. Se ne fornisci uno, deve misurare 815 x 290 pixel.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungi screenshot che mostrano come funziona la tua offerta. Sono necessarie almeno tre schermate ed è possibile aggiungere fino a cinque. Tutte le schermate devono essere 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, puoi aggiungere fino a cinque video che dimostrano la tua offerta. Questi video devono essere ospitati su YouTube e/o Vimeo. Per ognuno di essi, inserisci il nome del video, il suo URL e un'immagine di anteprima del video (1280 x 720 pixel)

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione del marketplace

- [Procedure consigliate per le inserzioni con offerte di marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Disponibilità

La pagina **Disponibilità** offre opzioni su dove e come rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Questa sezione consente di specificare i mercati in cui l'offerta deve essere disponibile. A tale scopo, selezionare **Modifica mercati**, che visualizzerà la finestra popup **Selezione mercato.**

Per impostazione predefinita, non è selezionato alcun mercato, ma è necessario selezionare almeno un mercato per pubblicare l'offerta. Fai clic su **Seleziona tutto** per rendere l'offerta disponibile in ogni possibile mercato oppure seleziona i mercati specifici che desideri aggiungere. Al termine, selezionare **Salva**.

Le tue selezioni qui si applicano solo alle nuove acquisizioni; se qualcuno ha già la tua app in un determinato mercato, e in seguito rimuovi quel mercato, le persone che hanno già l'offerta in quel mercato possono continuare a usarla, ma nessun nuovo cliente in quel mercato sarà in grado di ottenere la tua offerta.

> [!IMPORTANT]
> È tua responsabilità soddisfare eventuali requisiti legali locali, anche se tali requisiti non sono elencati qui o nel Centro per i partner.

Tieni presente che anche se selezioni tutti i mercati, le leggi locali e le restrizioni o altri fattori potrebbero impedire che determinate offerte vengano elencate in alcuni paesi e aree geografiche.

### <a name="preview-audience"></a>Visualizzare l'anteprima del gruppo di destinatari

Prima di pubblicare la tua offerta in diretta sull'offerta più ampia del marketplace, devi prima renderla disponibile a un pubblico di **anteprima**limitato. Immettere una **chiave Nascondi** (qualsiasi stringa che utilizza solo lettere minuscole e/o numeri) qui. I membri del pubblico di anteprima possono usare questa chiave Nascondi come token per visualizzare un'anteprima dell'offerta nel marketplace.

Quindi, quando sei pronto a rendere disponibile la tua offerta e rimuovere la restrizione di anteprima, dovrai rimuovere la **chiave Nascondi** e pubblicarla di nuovo.

## <a name="technical-configuration"></a>Configurazione tecnica

La pagina **Configurazione tecnica** definisce i dettagli tecnici utilizzati per connettersi all'offerta. Questa connessione ci consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquistarla.

### <a name="package-type"></a>Tipo di pacchetto

Seleziona l'opzione che si applica alla tua offerta:

- **Aggiungi su:** un'app componente aggiuntivo estende l'esperienza e le funzionalità esistenti di Dynamics 365 Business Central. Per altre info, vedi [App aggiuntive.](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)
- **Connetti:** un'app Connect può essere utilizzata nello scenario in cui è necessario stabilire una connessione point-to-point tra Dynamics 365 Business Central e una soluzione o servizio di terze parti. Per ulteriori informazioni, vedere [Connetti.](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)

### <a name="file-upload"></a>Caricamento di file

Se hai selezionato **Aggiungi su,** dove caricherai il file del pacchetto dell'offerta, insieme ai file di pacchetto per qualsiasi estensione da cui ha dipendenze.

#### <a name="extensions-package-file"></a>File del pacchetto delle estensioni

Caricare il file del pacchetto di estensione (.app) per l'offerta.

#### <a name="library-package-file"></a>File del pacchetto di libreria

Obbligatorio se l'offerta deve essere installata insieme a un'altra estensione che non verrà pubblicata nel marketplace. Se è così caricare il suo file .app qui.

#### <a name="dependency-package-file"></a>File del pacchetto di dipendenze

Obbligatorio se l'offerta deve essere installata insieme a un'altra estensione già pubblicata nel marketplace. In tal caso, carica il relativo `.app` o `.zip` il file qui.

### <a name="url-to-app-installation"></a>URL per l'installazione dell'app

Se hai selezionato **Connetti** sopra, fornisci qui l'URL per l'installazione dell'app. Per i servizi connessi che non richiedono l'installazione, fornisci l'URL per la pagina di destinazione del servizio o la pagina di iscrizione.

## <a name="test-drive-technical-configuration"></a>Configurazione tecnica del test drive

Se hai selezionato **Abilita un test drive** nella pagina Configurazione [offerta,](#offer-setup) dovrai fornire qui i dettagli per consentire ai clienti di sperimentare un test drive della tua offerta.

La pagina **Test drive** consente di impostare una dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di impegnarsi ad acquistarla. Ulteriori informazioni sono riportate nell'articolo [Che cos'è Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina **[Impostazione offerta](#offer-setup)** e deselezionare **Abilita test drive**.

Sono disponibili i seguenti tipi di test drive, ognuno con i propri requisiti di configurazione tecnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [App per la logica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configurazione tecnica non necessaria)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configurazione tecnica per il test drive di Azure Resource ManagerTechnical configuration for Azure Resource Manager test drive

Modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che si adattano a questo scenario usano solo le risorse di Azure.Products that fit this scenario use only Azure resources. Altre informazioni sulla configurazione di un test drive di [Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Aree** (obbligatorio): attualmente sono presenti 26 aree supportate da Azure in cui è possibile redimare il test drive. In genere, è consigliabile rendere disponibile l'unità di test nelle aree in cui si prevede il maggior numero di clienti, in modo che possano selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che la sottoscrizione sia autorizzata a distribuire tutte le risorse necessarie in ognuna delle aree selezionate.

- **Istanze**: Selezionare il tipo (caldo o freddo) e il numero di istanze disponibili, che verranno moltiplicate per il numero di regioni in cui l'offerta è disponibile.

**Hot**: Questo tipo di istanza viene distribuito e in attesa di accesso per ogni area selezionata. I clienti possono accedere istantaneamente alle istanze *Hot* di un test drive, anziché dover attendere una distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. È consigliabile avere almeno un'istanza *Hot,* poiché la maggior parte dei clienti non desidera attendere le distribuzioni complete, con conseguente calo dell'utilizzo dei clienti se non è disponibile alcuna istanza *Hot.*

**Cold**: Questo tipo di istanza rappresenta il numero totale di istanze che possono essere distribuite per ogni area. Le istanze fredde richiedono la distribuzione dell'intero modello di Test Drive Resource Manager quando un cliente richiede l'unità di test, pertanto le istanze *Cold* sono molto più lente da caricare rispetto alle istanze *Hot.* Il compromesso è che è necessario pagare solo per la durata del test drive, non è sempre in esecuzione nella sottoscrizione di Azure come con un'istanza *Hot.The* tradeoff is that You only have to pay for the duration of the test drive, it is *not* always running on your Azure subscription as with a Hot instance.

- **Test test test modello di Azure Resource Manager:** caricare il file .zip contenente il modello di Azure Resource Manager.Test drive Azure Resource Manager template : Upload the .zip containing your Azure Resource Manager template.  Per altre informazioni sulla creazione di un modello di Azure Resource Manager, vedere l'articolo delle guide rapide Creare e distribuire modelli di [Azure Resource Manager tramite il portale](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)di Azure.

- **Durata dell'unità** di test (obbligatorio): immettere il periodo di tempo in cui il Test Drive rimarrà attivo, in numero di ore. Il test drive termina automaticamente alla fine di questo periodo di tempo. Questa durata può essere impostata solo da un numero intero di ore (ad esempio, "2 ore", "1,5" non è valido).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configurazione tecnica per il test drive di Dynamics 365

Microsoft può rimuovere la complessità della configurazione di un test drive ospitando e mantenendo il provisioning e la distribuzione del servizio utilizzando questo tipo di test drive. La configurazione per questo tipo di test drive ospitato è la stessa indipendentemente dal fatto che il test drive sia destinato a un gruppo di destinatari Business Central, Customer Engagement o Operations.

- Numero massimo di **test simultanei** (obbligatorio): impostare il numero massimo di clienti che possono utilizzare il test drive contemporaneamente. Ogni utente simultaneo utilizzerà una licenza di Dynamics 365 mentre il test drive è attivo, pertanto dovrai assicurarti di disporre di un numero sufficiente di licenze disponibili per supportare il set di limiti massimo. Valore consigliato da 3 a 5.

- **Durata dell'unità** di test (obbligatorio): immettere il periodo di tempo in cui il Test Drive rimarrà attivo definendo il numero di ore. Dopo queste ore, la sessione terminerà e non utilizzerà più una delle licenze. Consigliamo un valore di 2-24 ore a seconda della complessità della tua offerta. Questa durata può essere impostata solo da un numero intero di ore (ad esempio, "2 ore", "1,5" non è valido).  L'utente può richiedere una nuova sessione se ha esaurito il tempo e desidera accedere nuovamente al test drive.

- **URL istanza** (obbligatorio): l'URL in cui il cliente inizierà il test drive. In genere l'URL dell'istanza di Dynamics 365 che `https://testdrive.crm.dynamics.com`esegue l'app con i dati di esempio installati, ad esempio .

- **URL dell'API Web dell'istanza** (obbligatorio): recupera l'URL dell'API Web per l'istanza di Dynamics 365 accedendo al tuo account Microsoft 365 e accedendo a **Impostazioni** \&gt; **Personalizzazione** \&gt; **Risorse** \&per sviluppatori gt; **API Web dell'istanza (URL radice del servizio)** `https://testdrive.crm.dynamics.com/api/data/v9.0`, copiare l'URL trovato qui (ad esempio, ).

- **Nome ruolo** (obbligatorio): specificare il nome del ruolo di sicurezza definito nell'unità di test Dynamics 365 personalizzata, che verrà assegnata all'utente durante il test drive (ad esempio, test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configurazione tecnica per l'unità di test dell'app per la logicaTechnical configuration for Logic app test drive

Tutti i prodotti personalizzati devono usare questo tipo di modello di distribuzione di test drive che include un'ampia gamma di architetture di soluzioni complesse. Per altre informazioni sull'impostazione dei test di App logica, vedere Operations and Customer Engagement su GitHub.For more information about setting up Logic App test drives, visit [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) on GitHub.

- **Area** (obbligatorio, elenco a discesa a selezione singola): attualmente sono disponibili 26 aree supportate da Azure in cui è possibile redimare il test drive. Le risorse per l'app per la logica verranno distribuite nell'area selezionata. Se l'app per la logica include risorse personalizzate archiviate in un'area specifica, assicurarsi che tale area sia selezionata qui. Il modo migliore consiste nel distribuire completamente l'app per la logica in locale nella sottoscrizione di Azure nel portale e verificare che funzioni correttamente prima di effettuare questa selezione.

- Numero massimo di **test simultanei** (obbligatorio): impostare il numero massimo di clienti che possono utilizzare il test drive contemporaneamente. Questi test drive sono già distribuiti, consentendo ai clienti di accedervi immediatamente senza attendere una distribuzione.

- **Durata dell'unità** di test (obbligatorio): immettere il periodo di tempo in cui il Test Drive rimarrà attivo, in numero di ore. L'unità di prova termina automaticamente al termine di questo periodo di tempo.

- **Nome del gruppo** di risorse di Azure (obbligatorio): immettere il nome del gruppo di risorse di [Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in cui viene salvata l'unità di test dell'app per la logica.

- **Nome app per la logica** di Azure (obbligatorio): immettere il nome dell'app per la logica che assegna l'unità di test all'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- **Deprovisioning del nome dell'app per** la logica (obbligatorio): immettere il nome dell'app per la logica che esegue il deprovisioning del test drive al termine del cliente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configurazione tecnica non necessaria per i test drive di Power BITechnical configuration not required for Power BI test drives

I prodotti che vogliono dimostrare un oggetto visivo interattivo di Power BI possono usare un collegamento incorporato per condividere un dashboard personalizzato come test drive, senza richiedere ulteriori configurazioni tecniche. Altre informazioni sulla configurazione delle app modello di[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione distribuzione

Per distribuire test Drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca. (Non richiesto per i test drive di Power BI).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la creazione di report e la fatturazione dell'utilizzo delle risorse. È consigliabile [creare una sottoscrizione](https://docs.microsoft.com/azure/billing/billing-create-subscription) di Azure separata da usare per i test drive se non ne è già presente una. È possibile trovare l'ID sottoscrizione di Azure accedendo al portale di [Azure](https://portal.azure.com/) e passando alla scheda **Sottoscrizioni** del menu a sinistra. Selezionando la scheda verrà visualizzato l'ID sottoscrizione (ad esempio, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID tenant di Azure AD** (obbligatorio): immettere l'ID [tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (AD). Per trovare questo ID, accedere al portale di [Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare , quindi cercare il numero **ID directory** elencato (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e). È inoltre possibile cercare l'ID tenant dell'organizzazione [https://www.whatismytenantid.com](https://www.whatismytenantid.com)utilizzando l'URL del nome di dominio all'indirizzo: .

- **Nome tenant di Azure AD** (obbligatorio per Dynamic 365): immettere il nome di Azure Active Directory (AD). Per trovare questo nome, accedere al portale di [Azure](https://portal.azure.com/), nell'angolo superiore destro il nome del tenant verrà elencato sotto il nome dell'account.

- **ID app Azure AD** (obbligatorio): immettere [l'ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (AD). Per trovare questo ID, accedere al portale di [Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Registrazioni app**, quindi cercare il numero ID **applicazione** elencato (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e).

- **Segreto client dell'app Azure AD** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure AD.Azure AD app client secret (required): Enter your Azure AD application client secret . Per trovare questo valore, accedere al portale di [Azure](https://portal.azure.com/). Seleziona la scheda **Azure Active Directory** nel menu a sinistra, seleziona **Registrazioni app**, quindi seleziona l'app di test drive. Selezionare quindi **Certificati e segreti**, Nuovo **segreto client**, immettere una descrizione, **selezionare Mai** in **Scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. (Non uscire dalla pagina prima di eseguire questa operazione, altrimenti non avrai accesso al valore).

Ricordati di **salvare** prima di passare alla sezione successiva!

### <a name="test-drive-marketplace-listings"></a>Elenchi di marketplace per test drive

L'opzione **di presentazione Marketplace** disponibile nella scheda Unità di **test** visualizza le lingue in cui è disponibile il test drive. Attualmente **inglese (Stati Uniti)** è l'unica posizione disponibile. Selezionare il nome della lingua per immettere informazioni che descrivono l'esperienza di test drive.

- **Descrizione** (obbligatorio): descrivere il test drive, ciò che verrà dimostrato, gli obiettivi per l'utente da sperimentare, le funzionalità da esplorare e tutte le informazioni pertinenti che consentono all'utente di determinare se acquistare l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

- **Accedere alle informazioni** (necessarie per Azure Resource Manager e test drive della logica): spiegare ciò che un cliente deve sapere per poter accedere e usare questo test drive. Esaminare uno scenario per l'utilizzo dell'offerta e esattamente ciò che il cliente deve sapere per accedere alle funzionalità durante il test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale utente** (obbligatorio): una procedura dettagliata dell'esperienza di test drive. Il Manuale dell'utente dovrebbe coprire esattamente ciò che si desidera che il cliente di ottenere da sperimentare il test drive e servire come riferimento per tutte le domande che possono avere. Il file deve essere in formato PDF e deve essere denominato (255 caratteri max) dopo il caricamento.

- **Video: aggiungi video** (facoltativo): i video possono essere caricati su YouTube o Vimeo e referenziati qui con un link e un'immagine di anteprima (533 x 324 pixel) in modo che un cliente possa visualizzare una procedura dettagliata di informazioni per aiutarli a comprendere meglio il test drive, incluso come utilizzare con successo le funzionalità della tua offerta e comprendere gli scenari che ne evidenziano i vantaggi.
  - **Nome** (obbligatorio)
  - **URL (solo YouTube o Vimeo)** (obbligatorio)
  - **Miniatura (533 x 324 px):** il file di immagine deve essere in formato PNG.

## <a name="supplemental-content"></a>Contenuti supplementari

Questa pagina ti consente di fornire ulteriori informazioni sulla tua offerta per aiutarci a convalidare la tua offerta. Queste informazioni non vengono mostrate ai clienti o pubblicate sul marketplace.

### <a name="target-release"></a>Rilascio di destinazione

Indicare la versione di Microsoft Dynamics Business Central a cui è destinata la soluzione: **Corrente**, **Successivo principale**o **Successivo secondario**. Queste informazioni ci consentono di testare la soluzione in modo appropriato.

### <a name="supported-editions"></a>Edizioni supportate

Se l'offerta richiede l'edizione Premium di Microsoft Dynamics 365 Business Central, selezionare Solo **Premium.** In caso contrario, selezionare **Essentials** e **Premium**.

### <a name="key-usage-scenario"></a>Scenario di utilizzo chiaveKey usage scenario

È necessario `.pdf` caricare un file che elenca gli scenari di utilizzo principali dell'offerta elencati in un documento (formato PDF). Tutti gli scenari elencati di seguito possono essere verificati dal nostro team di convalida prima di approvare l'offerta per il marketplace.

### <a name="app-tests-automation"></a>Automazione dei test delle app

Facoltativamente, puoi caricare un file di **automazione** dei test dell'app qui (.app).

### <a name="test-accounts"></a>Account di test

Se è necessario un account di prova per consentire al nostro team di certificazione di rivedere correttamente la tua offerta, carica un file .pdf, .doc o .docx con le tue informazioni sugli **account test.**

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Dopo aver completato tutte le sezioni richieste dell'offerta, selezionare **Pubblica** nell'angolo superiore destro del portale. Si verrà reindirizzati alla pagina **Revisione e pubblicazione.** 

Se è la prima volta che pubblichi questa offerta, puoi:

- Vedi lo stato di completamento per ogni sezione dell'offerta.
    - *Non avviato* - significa che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* - significa che la sezione contiene errori che devono essere corretti o che richiedono ulteriori informazioni. Tornare alle sezioni e aggiornarle.
    - *Completo* - significa che la sezione è completa, tutti i dati richiesti sono stati forniti e non ci sono errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Nella sezione Note per la **certificazione** fornire istruzioni di test al team di certificazione per assicurarsi che l'app sia testata correttamente, oltre a eventuali note aggiuntive utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Ti invieremo un'email quando è disponibile una versione di anteprima dell'offerta da esaminare e approvare. Torna al Centro per i partner e seleziona **Go-live** per l'offerta di pubblicare la tua offerta al pubblico (o se un'offerta privata, al pubblico privato).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
