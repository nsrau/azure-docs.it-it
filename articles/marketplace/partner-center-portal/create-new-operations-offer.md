---
title: Crea una nuova offerta di Dynamics 365 per le operazioni nel Marketplace commerciale
description: Come creare una nuova offerta di Dynamics 365 per le operazioni per l'inserzione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) usando il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 1ba7fca7576c6f015ad7bbcb47909b90c3065d58
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045952"
---
# <a name="create-a-new-dynamics-365-for-operations-offer"></a>Crea una nuova offerta di Dynamics 365 per le operazioni

In questo argomento viene illustrato come creare una nuova offerta di Dynamics 365 per le operazioni. [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) è un servizio ERP (Enterprise Resource Planning) che supporta la gestione avanzata di finanza, operazioni, produzione e supply chain. Tutte le offerte per Dynamics 365 per le operazioni devono passare attraverso il processo di certificazione.

Per iniziare a creare le offerte di Dynamics 365 per le operazioni, assicurarsi innanzitutto di [creare un account del centro](./create-account.md) per i partner e aprire il [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), con la pagina **Panoramica** selezionata.

![Dashboard del Marketplace commerciale nel centro per i partner](./media/new-offer-overview.png)

>[!Note]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel centro per i partner verranno aggiornate solo nel sistema e archiviano i front-end dopo la nuova pubblicazione. Assicurarsi di inviare l'offerta per la pubblicazione dopo avere apportato le modifiche.


## <a name="create-a-new-offer"></a>Creare una nuova offerta

Selezionare il pulsante **+ nuova offerta** , quindi selezionare la voce **di menu Dynamics 365 per operazioni** . Verrà visualizzata la finestra di dialogo **nuova offerta** .

### <a name="offer-id-and-alias"></a>ID offerta e alias

- **ID offerta**: identificatore univoco per ogni offerta nell'account. Questo ID sarà visibile ai clienti nell'indirizzo URL per l'offerta del Marketplace e i modelli Azure Resource Manager (se applicabile). L'ID offerta deve essere costituito da caratteri alfanumerici minuscoli (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti). Questo ID è limitato a 50 caratteri e non può essere modificato dopo aver selezionato **Crea**.  Se ad esempio si immette *test-offer-1* qui, l'URL dell'offerta verrà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Alias offerta**: nome usato per fare riferimento all'offerta all'interno del centro per i partner. Questo nome non verrà usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori che verranno visualizzati ai clienti. Questo valore non può essere modificato dopo aver selezionato **Crea**.

Dopo aver immesso l' **ID offerta** e l' **alias offerta**, selezionare **Crea**. Sarà quindi possibile lavorare su tutte le diverse parti dell'offerta.

## <a name="offer-setup"></a>Installazione dell'offerta

La pagina **installazione offerta** richiede le informazioni seguenti. Assicurarsi di selezionare **Salva** dopo aver completato questi campi.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>In che modo si desidera che i clienti possano interagire con questa offerta di inserzione?

Selezionare l'opzione che si vuole usare per questa offerta.

#### <a name="get-it-now-free"></a>Ottieni ora (gratuito)

Elenca gratuitamente l'offerta ai clienti fornendo un URL valido (a partire da *http* o *https*) dove possono accedere all'app.  Ad esempio: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Versione di valutazione gratuita (elenco)

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (a partire da *http* o *https*) dove è possibile ottenere una versione di valutazione.  Ad esempio: `https://contoso.com/trial/my-app`. Le versioni di valutazione gratuite dell'elenco di offerte vengono create, gestite e configurate dal servizio e non hanno sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione può essere usato solo per ottenere informazioni sugli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contattami

Raccogliere le informazioni di contatto del cliente connettendosi al sistema CRM (Customer Relationship Management). Al cliente verrà richiesta l'autorizzazione a condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e all'origine del Marketplace in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Per ulteriori informazioni sulla configurazione del CRM, vedere [Connect Lead Management](#connect-lead-management). 

### <a name="test-drive"></a>Test drive

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di provare prima di acquistare, ottenendo una maggiore conversione e la generazione di lead altamente qualificati. [Altre informazioni sulle unità di test.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Per abilitare una test drive, selezionare la casella **Abilita una test drive** . Sarà quindi necessario configurare un ambiente dimostrativo nella [configurazione tecnica di test drive](#test-drive-technical-configuration) per consentire ai clienti di provare l'offerta per un periodo di tempo fisso.

#### <a name="type-of-test-drive"></a>Tipo di test drive

Selezionare una delle seguenti opzioni:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che soddisfano questo scenario usano solo le risorse di Azure.
- **[Dynamics 365 for business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft ospita e gestisce il servizio di test drive (inclusi il provisioning e la distribuzione) per un sistema di pianificazione delle risorse aziendali centrale (finanza, operazioni, supply chain, CRM e così via).  
- **[Dynamics 365 per il coinvolgimento dei clienti](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft ospita e gestisce il servizio test drive (inclusi il provisioning e la distribuzione) per un sistema di engagement dei clienti (vendite, servizio, servizio del progetto, servizio campo e così via).  
- **[Dynamics 365 per le operazioni](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft ospita e gestisce il servizio test drive (inclusi il provisioning e la distribuzione) per un sistema di pianificazione delle risorse aziendali finanziarie e operative (finanza, operazioni, produzione, supply chain e così via). 
- **[App](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** per la logica: modello di distribuzione che comprende tutte le architetture di soluzioni complesse. Tutti i prodotti personalizzati devono usare questo tipo di test drive.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : un collegamento incorporato a un dashboard personalizzato. I prodotti che vogliono dimostrare un oggetto visivo Power BI interattivo devono usare questo tipo di test drive. È sufficiente caricare l'URL di Power BI incorporato qui.

#### <a name="additional-test-drive-resources"></a>Risorse test drive aggiuntive

- [Procedure consigliate tecniche di test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Procedure consigliate di marketing di test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Panoramica di test drive un cercapersone](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Gestione dei lead di connessione

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per altre informazioni, vedere [Cenni preliminari sulla gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

## <a name="properties"></a>Proprietà

La pagina delle **Proprietà** consente di definire le categorie e i settori usati per raggruppare l'offerta nel Marketplace, la versione dell'app e i contratti legali che supportano l'offerta. Selezionare **Salva** dopo aver completato questa pagina.

### <a name="category"></a>Categoria

Selezionare un minimo di uno e un massimo di tre categorie. Queste categorie verranno usate per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di richiamare il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta.

### <a name="industry"></a>Settore

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versione dell'app

Immettere il numero di versione dell'offerta. I clienti visualizzeranno questa versione nella pagina dei dettagli dell'offerta.

### <a name="standard-contract"></a>Contratto standard

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un modello di contratto standard per facilitare una transazione nel Marketplace.

Anziché creare termini e condizioni personalizzati, è possibile scegliere di offrire il software nel contratto standard, che i clienti devono solo controllare e accettare una sola volta.

Il contratto standard è disponibile qui: https://go.microsoft.com/fwlink/?linkid=2041178

Per usare il contratto standard, selezionare la casella **USA contratto standard?** .

#### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Se non si seleziona la casella **USA contratto standard** , sarà necessario fornire le proprie condizioni legali per l'utilizzo nel campo **condizioni per l'utilizzo** . Immettere un massimo di 10.000 caratteri di testo o, se le condizioni per l'utilizzo richiedono una descrizione più lunga, fornire l'URL in cui è possibile trovare le condizioni di licenza aggiuntive. Per poter provare l'app, ai clienti verrà richiesto di accettare le presenti condizioni.

## <a name="offer-listing"></a>Elenco offerte

Nella pagina di presentazione dell'offerta vengono visualizzate le lingue in cui verrà elencata l'offerta. Attualmente, l' **inglese (Stati Uniti)** è l'unica opzione disponibile.

Sarà necessario definire i dettagli del Marketplace (nome dell'offerta, descrizione, immagini e così via) per ogni lingua/mercato. Selezionare il nome del mercato/lingua per fornire queste informazioni.

> [!NOTE]
> Il contenuto dell'offerta (ad esempio la descrizione, i documenti, le schermate, le condizioni per l'utilizzo e così via) non deve essere in inglese, purché la descrizione dell'offerta inizi con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche accettabile fornire un URL di *collegamento utile* per offrire contenuto in una lingua diversa da quella usata nell'offerta di visualizzazione del contenuto.

### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato ai clienti come titolo dell'elenco di offerte. Questo campo viene prepopolato con il testo immesso per l' **alias offerta** al momento della creazione dell'offerta, ma è possibile modificare questo valore. Questo nome può essere registrato (e possono essere inclusi marchi o simboli di copyright). Il nome non può contenere più di 50 caratteri e non può includere emoji.

### <a name="short-description"></a>Breve descrizione

Fornire una breve descrizione dell'offerta (fino a 100 caratteri). Questa descrizione può essere usata nei risultati della ricerca nel Marketplace.

### <a name="description"></a>Description

Fornire una descrizione più lunga dell'offerta (fino a 3.000 caratteri). Questa descrizione verrà visualizzata ai clienti nella panoramica dell'inserzione sul Marketplace. Includere la proposta di valore dell'offerta, i vantaggi principali, le associazioni di categoria e/o di settore, le opportunità di acquisto in-app ed eventuali divulgazioni necessarie.

Alcuni suggerimenti per scrivere la descrizione:  

- Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includere le seguenti informazioni nella proposta di valore:
  - Descrizione del prodotto
  - Il tipo di utente che trae vantaggio dal prodotto
  - Esigenze del cliente o dolore che il prodotto indirizzi
- Tenere presente che le prime frasi possono essere visualizzate nei risultati dei motori di ricerca.  
- Per vendere un prodotto, non basarsi sulle funzionalità, ma concentrarsi sul valore che può offrire.  
- Usare il più possibile la terminologia specifica del settore o formule che mettano in evidenza i vantaggi offerti.
- Prendere in considerazione l'uso di tag HTML per formattare la descrizione e renderla più accattivante.

Per rendere più accattivante la descrizione dell'offerta, usare l'editor di testo RTF per formattare la descrizione.

![Uso dell'editor di testo RTF](./media/text-editor2.png)

Utilizzare le istruzioni seguenti per utilizzare l'editor di testo RTF:

- Per modificare il formato del contenuto, evidenziare il testo che si desidera formattare e selezionare uno stile di testo, come illustrato di seguito:

     ![Utilizzo dell'editor di testo RTF per modificare il formato del testo](./media/text-editor3.png)

- Per aggiungere un elenco puntato o numerato al testo, usare le opzioni seguenti:

     ![Uso dell'editor di testo RTF per aggiungere elenchi](./media/text-editor4.png)

- Per aggiungere o rimuovere il rientro nel testo, usare le opzioni seguenti:

     ![Utilizzo dell'editor di testo RTF per rientrare](./media/text-editor5.png)

### <a name="search-keywords"></a>Parole chiave di ricerca

Facoltativamente, è possibile immettere fino a tre parole chiave di ricerca per aiutare i clienti a trovare l'offerta nel Marketplace. Per ottenere risultati ottimali, provare a usare queste parole chiave anche nella descrizione.

### <a name="products-your-app-works-with"></a>Prodotti interrelati con l'app

Se si vuole informare i clienti che l'app funziona con prodotti specifici, immettere qui fino a tre nomi di prodotti.

### <a name="support-urls"></a>URL di supporto

Questa sezione consente di fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="help-link"></a>Collegamento alla guida

Immettere l'URL in cui i clienti possono ottenere ulteriori informazioni sull'offerta.

#### <a name="privacy-policy-url"></a>URL Informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida.

### <a name="contacts"></a>Contatti

In questa sezione, fornire il nome, il messaggio di posta elettronica e il numero di telefono per un **contatto di supporto** e un **contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e potranno essere fornite ai partner CSP.

Nella sezione **contatto del supporto tecnico** fornire l' **URL di supporto** in cui i partner CSP possono trovare il supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire qui almeno uno (e tre) documenti di marketing correlati, ad esempio white paper, brochure, elenchi di controllo o presentazioni. Questi documenti devono essere in formato PDF.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione è possibile fornire logo e immagini che verranno usati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato png.

#### <a name="store-logos"></a>Logo dello Store

Fornire il logo dell'offerta in due dimensioni: **Small (48 x 48)** e **Large (216 x 216)** .

#### <a name="hero"></a>Banner

L'immagine Hero è facoltativa. Se ne viene fornito uno, deve misurare 815 x 290 pixel.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere schermate che mostrano il funzionamento dell'offerta. È necessario almeno una schermata ed è possibile aggiungerne fino a cinque. Tutte le schermate devono avere 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, è possibile aggiungere fino a quattro video che illustrano l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ciascuna di esse, immettere il nome del video, il relativo URL e un'immagine di anteprima del video (1280 x 720 pixel)

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

- [Procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Disponibilità

La pagina di **disponibilità** offre le opzioni relative a dove e come rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Questa sezione consente di specificare i mercati in cui l'offerta deve essere disponibile. A tale scopo, selezionare **modifica mercati** . viene visualizzata la finestra popup **selezione mercato** .

Per impostazione predefinita, non è selezionato alcun mercato. Selezionare almeno un mercato per pubblicare l'offerta. Fare clic su **Seleziona tutto** per rendere disponibile l'offerta in ogni possibile mercato oppure selezionare i mercati specifici che si desidera aggiungere. Al termine, selezionare **Salva**.

Le selezioni in questo articolo si applicano solo alle nuove acquisizioni; Se un utente dispone già di un'app in un determinato mercato e successivamente si rimuove il mercato, le persone che dispongono già dell'offerta in quel mercato possono continuare a utilizzarlo, ma nessun nuovo cliente in quel mercato potrà ottenere l'offerta.

> [!IMPORTANT]
> È responsabilità dell'utente soddisfare eventuali requisiti legali locali, anche se tali requisiti non sono elencati qui o nel centro per i partner.

Tenere presente che, anche se si selezionano tutti i mercati, le leggi locali e le restrizioni o altri fattori possono impedire che alcune offerte siano elencate in alcuni paesi e aree geografiche.

### <a name="preview-audience"></a>Anteprima destinatari

Prima di pubblicare l'offerta in tempo reale nell'offerta del Marketplace più ampia, sarà prima di tutto necessario renderla disponibile per un gruppo di **destinatari di anteprima**limitato. Immettere un **tasto Nascondi** (qualsiasi stringa che usa solo lettere minuscole e/o numeri) qui. I membri dei destinatari di anteprima possono usare questa chiave Hide come token per visualizzare un'anteprima dell'offerta nel Marketplace.

Quindi, quando si è pronti per rendere disponibile l'offerta e rimuovere la restrizione di anteprima, è necessario rimuovere la **chiave Hide** e pubblicare di nuovo.

## <a name="technical-configuration"></a>Configurazione tecnica

La pagina **configurazione tecnica** definisce i dettagli tecnici usati per la connessione all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquisirla.

### <a name="solution-identifier"></a>Identificatore della soluzione

Fornire l'identificatore di soluzione (GUID) per la soluzione.

Per trovare l'identificatore della soluzione:
1. In Microsoft Dynamics Lifecycle Services (LCS) selezionare **Gestione soluzioni**.
2. Selezionare la soluzione, quindi cercare l' **identificatore della soluzione** nella **Panoramica del pacchetto**. Se l'identificatore è vuoto, selezionare **modifica** e ripubblicazione del pacchetto, quindi riprovare.

### <a name="release-version"></a>Versione di rilascio

Selezionare la versione di Dynamics 365 per Finanza e operazioni con cui funziona questa soluzione.

## <a name="test-drive-technical-configuration"></a>Configurazione tecnica test drive

Se è stata selezionata l'opzione **Abilita un test drive** nella pagina [installazione offerta](#offer-setup) , è necessario fornire i dettagli per consentire ai clienti di sperimentare una test drive dell'offerta.

La pagina **test drive** consente di configurare una dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di eseguire il commit per acquistarla. Per altre informazioni, vedere l'articolo relativo a [test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina **[impostazione offerta](#offer-setup)** e deselezionare **Abilita Test Drive**.

Sono disponibili i tipi di unità di test seguenti, ognuno con requisiti di configurazione tecnici specifici.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [App per la logica](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (configurazione tecnica non necessaria)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configurazione tecnica per Azure Resource Manager test drive

Un modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che soddisfano questo scenario usano solo le risorse di Azure. Altre informazioni sulla configurazione di un [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regions** (obbligatorio): attualmente sono presenti 26 aree supportate da Azure in cui è possibile rendere disponibili le test drive. In genere, è consigliabile rendere le test drive disponibili nelle aree in cui si prevede il maggior numero di clienti, in modo che possano selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che alla sottoscrizione sia consentita la distribuzione di tutte le risorse necessarie in ognuna delle aree selezionate.

- **Istanze**: selezionare il tipo (Hot o Cold) e il numero di istanze disponibili, che verranno moltiplicate per il numero di aree in cui è disponibile l'offerta.

**Hot**: questo tipo di istanza viene distribuito e in attesa di accesso per area selezionata. I clienti possono accedere immediatamente alle istanze a *caldo* di una test drive, anziché dover attendere una distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. Si consiglia di disporre di almeno un'istanza di accesso frequente, in quanto la maggior parte dei clienti non desidera attendere le distribuzioni complete, causando un calo nell'utilizzo del cliente se non è *disponibile un'istanza* a *caldo* .

**Cold**: questo tipo di istanza rappresenta il numero totale di istanze che possono essere distribuite per ogni area. Per le istanze a freddo è necessario che l'intero test drive Gestione risorse modello venga distribuito quando un cliente richiede l'test drive, in modo che le istanze a *freddo* risultino molto più lente del caricamento rispetto alle istanze a *caldo* . Il compromesso è dovuto al fatto che è necessario pagare solo per la durata del test drive, *non* è sempre in esecuzione nella sottoscrizione di Azure come con *un'istanza* attiva.

- **Test drive Azure Resource Manager modello**: caricare il file zip contenente il modello di Azure Resource Manager.  Per altre informazioni sulla creazione di un modello di Azure Resource Manager, vedere l'articolo introduttivo [creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Test drive duration** (required): Enter the length of time that the Test Drive will stay active, in # of hours. Il test drive termina automaticamente alla fine di questo periodo di tempo. Questa durata può essere impostata solo per un numero intero di ore (ad esempio, "2" ore; "1,5" non è valido).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configurazione tecnica per Dynamics 365 test drive

Microsoft può rimuovere la complessità della configurazione di un test drive ospitando e gestendo il provisioning e la distribuzione del servizio usando questo tipo di test drive. La configurazione di questo tipo di test drive ospitata è la stessa, indipendentemente dal fatto che la test drive sia destinata a un gruppo di lavoro centrale, Customer Engagement o Operations.

- **Max concurrent test drives** (required): Set the maximum number of customers that can use your test drive at one time. Ogni utente simultaneo utilizzerà una licenza Dynamics 365 mentre la test drive è attiva, pertanto sarà necessario assicurarsi che siano disponibili sufficienti licenze per supportare il limite massimo impostato. Valore consigliato da 3 a 5.

- **Durata test drive** (obbligatorio): immettere il periodo di tempo per cui il test drive resterà attivo definendo il numero di ore. Dopo questo numero di ore, la sessione terminerà e non utilizzerà più una delle licenze. È consigliabile un valore di 2-24 ore a seconda della complessità dell'offerta. Questa durata può essere impostata solo per un numero intero di ore (ad esempio, "2" ore; "1,5" non è valido).  L'utente può richiedere una nuova sessione se esaurisce il tempo e desidera accedere nuovamente al test drive.

- **URL istanza** (obbligatorio): URL da cui il cliente inizierà a test drive. In genere l'URL dell'istanza di Dynamics 365 che esegue l'app con i dati di esempio installati, ad esempio https://testdrive.crm.dynamics.com).

- **Instance Web API URL** (required): Retrieve the Web API URL for your Dynamics 365 instance by logging into your Microsoft 365 account and navigating to **Settings** \&gt; **Customization** \&gt; **Developer Resources** \&gt; **Instance Web API (Service Root URL)** , copy the URL found here (for example, https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Role name** (required): Provide the security role name you have defined in your custom Dynamics 365 test drive. This will be assigned to the user during their test drive (for example, test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technical configuration for Logic app test drive

Any custom products should use this type of test drive deployment template, which encompasses a variety of complex solution architectures. For more information about setting up Logic App test drives, visit [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) on GitHub.

- **Region** (required, single-selection dropdown list): Currently there are 26 Azure-supported regions where your test drive can be made available. The resources for your Logic app will be deployed in the region you select. If your Logic App has any custom resources stored in a specific region, make sure that region is selected here. The best way to ensure you have custom resources for your region available is to fully deploy your Logic App locally on your Azure subscription in the portal and verify that it functions correctly before making this selection.

- **Max concurrent test drives** (required): Set the maximum number of customers that can use your test drive at one time. These test drives are already deployed, enabling customers to instantly access them without waiting for a deployment.

- **Test drive duration** (required): Enter the length of time that the Test Drive will stay active, in # of hours. The test drive terminates automatically after this time period ends.

- **Azure resource group name** (required): Enter the [Azure resource group](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) name where your Logic App test drive is saved.

- **Azure logic app name** (required): Enter the name of the Logic app that assigns the test drive to the user. This Logic app must be saved in the Azure resources group above.

- **Deprovision logic app name** (required): Enter the name of the Logic app that deprovisions the test drive once the customer is finished. This Logic app must be saved in the Azure resources group above.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technical configuration not required for Power BI test drives

Products that want to demonstrate an interactive Power BI visual can use an embedded link to share a custom-built dashboard as their test drive, no further technical configuration required. Learn more about setting up[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) template apps.

### <a name="deployment-subscription-details"></a>Deployment subscription details

In order to deploy the Test Drive on your behalf, create and provide a separate, unique Azure Subscription. (Not required for Power BI test drives).

- **Azure subscription ID** (required for Azure Resource Manager and Logic apps): Enter the subscription ID to grant access to your Azure account services for resource usage reporting and billing. We recommend that you consider [creating a separate Azure subscription](https://docs.microsoft.com/azure/billing/billing-create-subscription) to use for test drives if you don't have one already. You can find your Azure subscription ID by logging in to the [Azure portal](https://portal.azure.com/) and navigating to the **Subscriptions** tab of the left-side menu. Selecting the tab will display your subscription ID (for example, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD tenant ID** (required): Enter your Azure Active Directory (AD) [tenant ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). To find this ID, sign in to the [Azure portal](https://portal.azure.com/), select the Active Directory tab in the left-menu, select **Properties**, then look for the **Directory ID** number listed (for example, 50c464d3-4930-494c-963c-1e951d15360e). You can also look up your organization's tenant ID using your domain name URL at:  [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Azure AD tenant name** (required for Dynamic 365): Enter your Azure Active Directory (AD) name. To find this name, sign in to the [Azure portal](https://portal.azure.com/), in the upper right corner your tenant name will be listed under your account name.

- **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e).

- **Azure ad segreto client App** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu a sinistra, selezionare **registrazioni app**, quindi selezionare l'app test drive. Selezionare quindi **certificati e segreti**, fare clic **su nuovo segreto client**, immettere una descrizione, selezionare **mai** sotto **scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. Non uscire dalla pagina prima di prendere nota del valore oppure non si avrà accesso al valore.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

### <a name="test-drive-marketplace-listings"></a>Test Drive Marketplace elenchi

L'opzione di **elenco del Marketplace** disponibile nella scheda **test drive** Visualizza le lingue in cui è disponibile il test drive. Attualmente, la **lingua inglese (Stati Uniti)** è l'unica località disponibile. Selezionare il nome della lingua per immettere le informazioni che descrivono l'esperienza test drive.

- **Descrizione** (obbligatoria): descrivere la test drive, gli elementi che verranno illustrati, gli obiettivi dell'utente per sperimentare, le funzionalità da esplorare e tutte le informazioni rilevanti per aiutare l'utente a determinare se acquisire l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo.

- **Informazioni di accesso** (obbligatorie per Azure Resource Manager e unità di test per la logica): spiegare cosa è necessario sapere al cliente per accedere a questo test drive e usarlo. Esaminare uno scenario per l'uso dell'offerta e esattamente ciò che il cliente deve conoscere per accedere alle funzionalità nell'test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale dell'utente** (obbligatorio): una procedura dettagliata approfondita dell'esperienza test drive. Il manuale dell'utente dovrebbe coprire esattamente ciò che si vuole che il cliente possa ottenere dall'esperienza del test drive e fungere da riferimento per eventuali domande. Il file deve essere in formato PDF ed essere denominato (255 caratteri al massimo) dopo il caricamento.

- **Video: aggiungere video** (facoltativo): è possibile caricare video in YouTube o Vimeo e farvi riferimento con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una panoramica delle informazioni che consentono di comprendere meglio le test drive, tra cui come usare correttamente le funzionalità dell'offerta e comprendere gli scenari in cui vengono evidenziati i vantaggi.
  - **Nome** (obbligatorio)
  - **URL (solo YouTube o Vimeo)** (obbligatorio)
  - **Anteprima (533 x 324px)** : il file di immagine deve essere in formato png.

## <a name="supplemental-content"></a>Contenuto supplementare

Questa pagina consente di fornire informazioni aggiuntive sull'offerta che consentono di convalidare l'offerta. Queste informazioni non vengono visualizzate ai clienti o pubblicate sul Marketplace.

### <a name="validation-assets"></a>Asset di convalida

Caricare un [report di analisi della personalizzazione (auto)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) in questa sezione. Questo report viene generato analizzando i modelli di personalizzazione e di estensione in base a un set predefinito di regole per le procedure consigliate.

Questo file deve essere in formato xls o xlsx. Se si dispone di più di un report, è possibile caricare un file con estensione zip contenente tutti i report.

### <a name="does-solution-include-localizations"></a>La soluzione include le localizzazioni?

Selezionare **Sì** se la soluzione consente l'uso di criteri e standard locali (ad esempio, se sono conformi alle diverse regole relative alle retribuzioni richieste da paesi/aree diverse). In caso contrario, selezionare **No**.

### <a name="does-solution-enable-translations"></a>La soluzione Abilita le traduzioni?

Risposta **affermativa** se il testo nella soluzione può essere convertito in altre lingue. In caso contrario, selezionare **No**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Una volta completate tutte le sezioni obbligatorie dell'offerta, selezionare **pubblica** nell'angolo superiore destro del portale. Si verrà reindirizzati alla pagina di **revisione e pubblicazione** .

Se è la prima volta che si pubblica questa offerta, è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta.
    - *Non avviato* : indica che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o che sono necessarie altre informazioni. Tornare alla sezione o aggiornarla.
    - *Complete* : indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** , fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note aggiuntive utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Verrà inviato un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta al pubblico (o se un'offerta privata, per i destinatari privati).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
