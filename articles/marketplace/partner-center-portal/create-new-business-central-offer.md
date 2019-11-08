---
title: Crea una nuova offerta di Dynamics 365 business Central nel Marketplace commerciale
description: Come creare una nuova offerta di Dynamics 365 business Central per l'inserzione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) usando il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: b3d66cab8cf43d862bbf17d5783f0469e3e1a5f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825645"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Crea una nuova offerta di Dynamics 365 business Central

In questo argomento viene illustrato come creare una nuova offerta di Dynamics 365 business Central. [Microsoft Dynamics 365 business Central](https://dynamics.microsoft.com/business-central) è un sistema ERP (Enterprise Resource Planning) che gestisce un'ampia gamma di processi aziendali, tra cui finanza, operazioni, supply chain, CRM e gestione dei progetti e del commercio elettronico. I pacchetti Premium supportano anche il modello di distribuzione classica e la produzione. Tutte le offerte per Dynamics 365 business Central devono passare attraverso il processo di certificazione.

Per iniziare a creare le offerte di Dynamics 365 business Central, prima di tutto è necessario [creare un account del centro](./create-account.md) per i partner e aprire il [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), con la pagina **Panoramica** selezionata.

![Dashboard del Marketplace commerciale nel centro per i partner](./media/new-offer-overview.png)

>[!Note]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel centro per i partner verranno aggiornate solo nel sistema e archiviano i front-end dopo la nuova pubblicazione. Assicurarsi di inviare l'offerta per la pubblicazione dopo avere apportato le modifiche.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

Selezionare il pulsante **+ nuova offerta** , quindi selezionare la voce di menu **Dynamics 365 business Central** . Verrà visualizzata la finestra di dialogo **nuova offerta** .

### <a name="offer-id-and-alias"></a>ID offerta e alias

- **ID offerta**: identificatore univoco per ogni offerta nell'account. Questo ID sarà visibile ai clienti nell'indirizzo URL per l'offerta del Marketplace e i modelli Azure Resource Manager (se applicabile). L'ID offerta deve essere costituito da caratteri alfanumerici minuscoli (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti), limitati a 50 caratteri e non può essere modificato dopo aver selezionato **Crea**.  Se ad esempio si immette *test-offer-1* qui, l'URL dell'offerta verrà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

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

#### <a name="contact-me"></a>Contact me (Contattami)

Raccogliere le informazioni di contatto del cliente connettendosi al sistema CRM (Customer Relationship Management). Al cliente verrà richiesta l'autorizzazione a condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e all'origine del Marketplace in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Per ulteriori informazioni sulla configurazione del CRM, vedere [Connect Lead Management](#connect-lead-management). 

### <a name="test-drive"></a>Test drive

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di provare prima di acquistare, ottenendo una maggiore conversione e la generazione di lead altamente qualificati. [Altre informazioni sulle unità di test.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Per abilitare una test drive, selezionare la casella **Abilita una test drive** . Sarà quindi necessario configurare un ambiente dimostrativo nella [configurazione tecnica di test drive](#test-drive-technical-configuration) per consentire ai clienti di provare l'offerta per un periodo di tempo fisso. 

#### <a name="type-of-test-drive"></a>Tipo di test drive

Selezionare una delle opzioni seguenti:

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

Selezionare un minimo di una e un massimo di tre categorie, che verranno usate per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di richiamare il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta. 

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

### <a name="name"></a>Name

Il nome immesso qui verrà visualizzato ai clienti come titolo dell'elenco di offerte. Questo campo viene prepopolato con il testo immesso per l' **alias offerta** al momento della creazione dell'offerta, ma è possibile modificare questo valore. Questo nome può essere registrato (e possono essere inclusi marchi o simboli di copyright). Il nome non può contenere più di 50 caratteri e non può includere emoji.

### <a name="short-description"></a>Breve descrizione

Fornire una breve descrizione dell'offerta (fino a 100 caratteri), che può essere usata nei risultati della ricerca nel Marketplace.

### <a name="description"></a>Descrizione

Fornire una descrizione più lunga dell'offerta (fino a 3.000 caratteri). Questa descrizione verrà visualizzata ai clienti nella panoramica dell'inserzione sul Marketplace. Includere la proposta di valore dell'offerta, i vantaggi principali, le associazioni di categoria e/o di settore, le opportunità di acquisto in-app ed eventuali divulgazioni necessarie. 

Alcuni suggerimenti per scrivere la descrizione:  

- Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includere gli elementi seguenti nella proposta di valore:
  - Descrizione del prodotto
  - Il tipo di utente che trae vantaggio dal prodotto
  - Esigenze del cliente o dolore che il prodotto indirizzi
- Tenere presente che le prime frasi possono essere visualizzate nei risultati dei motori di ricerca.  
- Per vendere un prodotto, non basarsi sulle funzionalità, ma concentrarsi sul valore che può offrire.  
- Usare il più possibile la terminologia specifica del settore o formule che mettano in evidenza i vantaggi offerti. 
- Prendere in considerazione l'uso di tag HTML per formattare la descrizione e renderla più accattivante.

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Facoltativamente, è possibile immettere fino a tre parole chiave di ricerca per aiutare i clienti a trovare l'offerta nel Marketplace. Per ottenere risultati ottimali, provare a usare queste parole chiave anche nella descrizione.

### <a name="products-your-app-works-with"></a>Prodotti interrelati con l'app

Se si vuole informare i clienti che l'app funziona con prodotti specifici, immettere qui fino a tre nomi di prodotti.

### <a name="support-urls"></a>URL di supporto

Questa sezione consente di fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="help-link"></a>Collegamento alla guida

Immettere l'URL in cui i clienti possono ottenere ulteriori informazioni sull'offerta.

#### <a name="privacy-policy-url"></a>URL informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida.

### <a name="contacts"></a>Contatti

In questa sezione è necessario fornire il nome, l'indirizzo di posta elettronica e il numero di telefono per un **contatto di supporto** e un **contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e potranno essere fornite ai partner CSP.

Nella sezione **Contact Support** è necessario fornire anche l'URL di **supporto** in cui i partner CSP possono trovare il supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire qui almeno uno (e tre) documenti di marketing correlati, ad esempio white paper, brochure, elenchi di controllo o presentazioni. Questi documenti devono essere in formato PDF.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione è possibile fornire logo e immagini che verranno usati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato png.

#### <a name="store-logos"></a>Logo dello Store

Fornire il logo dell'offerta in due dimensioni: **Small (48 x 48)** e **Large (216 x 216)** .

#### <a name="hero"></a>Banner

L'immagine Hero è facoltativa. Se ne viene fornito uno, deve misurare 815 x 290 pixel.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere schermate che mostrano il funzionamento dell'offerta. Sono necessarie almeno tre schermate ed è possibile aggiungerne fino a cinque. Tutte le schermate devono avere 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, è possibile aggiungere fino a cinque video che illustrano l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ciascuna di esse, immettere il nome del video, il relativo URL e un'immagine di anteprima del video (1280 x 720 pixel)

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

- [Procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Disponibilità

Nella pagina di **disponibilità** sono disponibili le opzioni per la posizione e il modo in cui rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Questa sezione consente di specificare i mercati in cui l'offerta deve essere disponibile. A tale scopo, selezionare **modifica mercati**. viene visualizzata la finestra popup **selezione mercato** .

Per impostazione predefinita, non è selezionato alcun mercato, ma è necessario selezionare almeno un mercato per pubblicare l'offerta. Fare clic su **Seleziona tutto** per rendere disponibile l'offerta in ogni possibile mercato oppure selezionare i mercati specifici che si desidera aggiungere. Al termine, selezionare **Salva**.

Le selezioni in questo articolo si applicano solo alle nuove acquisizioni; Se un utente dispone già di un'app in un determinato mercato e successivamente si rimuove il mercato, le persone che dispongono già dell'offerta in quel mercato possono continuare a utilizzarlo, ma nessun nuovo cliente in quel mercato potrà ottenere l'offerta.

> [!IMPORTANT]
> È responsabilità dell'utente soddisfare eventuali requisiti legali locali, anche se tali requisiti non sono elencati qui o nel centro per i partner.

Tenere presente che, anche se si selezionano tutti i mercati, le leggi locali e le restrizioni o altri fattori, può impedire che alcune offerte siano elencate in alcuni paesi e regioni.

### <a name="preview-audience"></a>Anteprima destinatari

Prima di pubblicare l'offerta in tempo reale nell'offerta del Marketplace più ampia, sarà prima di tutto necessario renderla disponibile per un gruppo di **destinatari di anteprima**limitato. Immettere un **tasto Nascondi** (qualsiasi stringa che usa solo lettere minuscole e/o numeri) qui. I membri dei destinatari di anteprima possono usare questa chiave Hide come token per visualizzare un'anteprima dell'offerta nel Marketplace.

Quindi, quando si è pronti per rendere disponibile l'offerta e rimuovere la restrizione di anteprima, è necessario rimuovere la **chiave Hide** e pubblicare di nuovo.

## <a name="technical-configuration"></a>Configurazione tecnica

La pagina **configurazione tecnica** definisce i dettagli tecnici usati per la connessione all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquisirla.

### <a name="package-type"></a>Tipo di pacchetto

Selezionare l'opzione che si applica all'offerta:

- **Aggiunta**: un'app componente aggiuntivo estende l'esperienza e le funzionalità esistenti di Dynamics 365 business Central. Per altre informazioni, vedere [app aggiuntive](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Connetti**: è possibile usare un'app Connect nello scenario in cui deve essere stabilita una connessione Point-to-Point tra Dynamics 365 business Central e una soluzione o un servizio di terze parti. Per altre informazioni, vedere [Connect](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Caricamento di file

Se è stata selezionata l'opzione **Aggiungi in** precedenza, in cui verrà caricato il file del pacchetto dell'offerta, insieme ai file del pacchetto per qualsiasi estensione in cui sono presenti dipendenze.

#### <a name="extensions-package-file"></a>File del pacchetto di estensioni

Caricare il file del pacchetto di estensione (app) per l'offerta.

#### <a name="library-package-file"></a>File del pacchetto di libreria

Obbligatorio se l'offerta deve essere installata insieme a un'altra estensione che non verrà pubblicata nel Marketplace. In tal caso, caricare il file con estensione app qui.

#### <a name="dependency-package-file"></a>File del pacchetto di dipendenze

Obbligatorio se l'offerta deve essere installata insieme a un'altra estensione che è già stata pubblicata nel Marketplace. In tal caso, caricare qui il file `.app` o `.zip`.

### <a name="url-to-app-installation"></a>URL per l'installazione dell'app

Se è stata selezionata l'opzione **Connetti** sopra, fornire l'URL per l'installazione dell'app qui.

## <a name="test-drive-technical-configuration"></a>Configurazione tecnica test drive

Se è stata selezionata l'opzione **Abilita un test drive** nella pagina [installazione offerta](#offer-setup) , è necessario fornire i dettagli per consentire ai clienti di sperimentare una test drive dell'offerta.

La pagina **test drive** consente di configurare una dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di eseguire il commit per acquistarla. Per altre informazioni, vedere l'articolo relativo a [test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina **[impostazione offerta](#offer-setup)** e deselezionare **Abilita Test Drive**.

Sono disponibili i tipi di unità di test seguenti, ognuno con requisiti di configurazione tecnici specifici.

- [Gestione risorse di Azure](#technical-configuration-for-azure-resource-manager-test-drive)
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

- **Durata test drive** (obbligatorio): immettere il periodo di tempo in cui il test drive resterà attivo, in ore. Il test drive termina automaticamente alla fine di questo periodo di tempo. Questa durata può essere impostata solo per un numero intero di ore (ad esempio, "2" ore, "1,5" non è valido).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configurazione tecnica per Dynamics 365 test drive

Microsoft può rimuovere la complessità della configurazione di un test drive ospitando e gestendo il provisioning e la distribuzione del servizio usando questo tipo di test drive. La configurazione di questo tipo di test drive ospitata è la stessa, indipendentemente dal fatto che la test drive sia destinata a un gruppo di lavoro centrale, Customer Engagement o Operations.

- Numero massimo di **unità di test simultanee** (obbligatorio): impostare il numero massimo di clienti che possono usare la test drive alla volta. Ogni utente simultaneo utilizzerà una licenza Dynamics 365 mentre la test drive è attiva, pertanto sarà necessario assicurarsi che siano disponibili sufficienti licenze per supportare il limite massimo impostato. Valore consigliato da 3 a 5.

- **Durata test drive** (obbligatorio): immettere il periodo di tempo per cui il test drive resterà attivo definendo il numero di ore. Dopo questo numero di ore, la sessione terminerà e non utilizzerà più una delle licenze. È consigliabile un valore di 2-24 ore a seconda della complessità dell'offerta. Questa durata può essere impostata solo per un numero intero di ore (ad esempio, "2" ore, "1,5" non è valido).  L'utente può richiedere una nuova sessione se esaurisce il tempo e desidera accedere nuovamente al test drive.

- **URL istanza** (obbligatorio): URL da cui il cliente inizierà a test drive. In genere l'URL dell'istanza di Dynamics 365 che esegue l'app con i dati di esempio installati, ad esempio https://testdrive.crm.dynamics.com).

- **URL dell'API Web dell'istanza** (obbligatorio): recuperare l'URL dell'API Web per l'istanza di Dynamics 365 accedendo all'account Microsoft 365 e passando alle **Impostazioni** \&gt; **Personalizzazione** \&gt; **Risorse per sviluppatori** \&gt; **API Web dell'istanza (URL radice del servizio)** , copiare l'URL trovato qui, ad esempio https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Nome ruolo** (obbligatorio): specificare il nome del ruolo di sicurezza definito nell'test drive Dynamics 365 personalizzato, che verrà assegnato all'utente durante la relativa Test Drive, ad esempio test-drive-Role.

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configurazione tecnica per test drive app per la logica

Tutti i prodotti personalizzati devono usare questo tipo di modello di distribuzione test drive che comprende un'ampia gamma di architetture di soluzioni complesse. Per altre informazioni sulla configurazione di unità di test per l'app per la logica, vedere [operazioni](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [coinvolgimento dei clienti](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) su GitHub.

- **Region** (obbligatorio, elenco a discesa a selezione singola): attualmente sono presenti 26 aree supportate da Azure in cui è possibile rendere disponibili le test drive. Le risorse per l'app per la logica verranno distribuite nell'area selezionata. Se l'app per la logica contiene risorse personalizzate archiviate in un'area specifica, assicurarsi che l'area sia selezionata qui. Il modo migliore consiste nel distribuire completamente l'app per la logica in locale nella sottoscrizione di Azure nel portale e verificare che funzioni correttamente prima di effettuare questa selezione.

- Numero massimo di **unità di test simultanee** (obbligatorio): impostare il numero massimo di clienti che possono usare la test drive alla volta. Queste unità di test sono già distribuite, consentendo ai clienti di accedervi immediatamente senza attendere una distribuzione.

- **Durata test drive** (obbligatorio): immettere il periodo di tempo in cui il test drive resterà attivo, in ore. Il test drive termina automaticamente al termine di questo periodo di tempo.

- **Nome del gruppo di risorse di Azure** (obbligatorio): immettere il nome del [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in cui viene salvata l'App per la logica test drive.

- **Nome dell'app** per la logica di Azure (obbligatorio): immettere il nome dell'app per la logica che assegna la test drive all'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- **Deprovisioning app per la logica nome** (obbligatorio): immettere il nome dell'app per la logica che annulla il provisioning del test drive al termine del cliente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configurazione tecnica non necessaria per Power BI test drive

I prodotti che vogliono dimostrare un oggetto visivo Power BI interattivo possono usare un collegamento incorporato per condividere un dashboard personalizzato come test drive, non sono necessarie altre configurazioni tecniche. Altre informazioni sulla configurazione di[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) app modello.

### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione della distribuzione

Per distribuire il test drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca. (Non necessario per Power BI test drive).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile [creare una sottoscrizione di Azure separata](https://docs.microsoft.com/azure/billing/billing-create-subscription) da usare per le unità di test se non ne è già presente uno. È possibile trovare l'ID sottoscrizione di Azure accedendo al [portale di Azure](https://portal.azure.com/) e passando alla scheda **sottoscrizioni** del menu a sinistra. Se si seleziona la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5AB6-6789-1h234g764ghty".

- **Azure ad ID tenant** (obbligatorio): immettere l' [id tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare * * proprietà, quindi cercare il numero **ID directory** elencato (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile cercare l'ID tenant dell'organizzazione usando l'URL del nome di dominio all'indirizzo: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Azure ad nome del tenant** (obbligatorio per la 365 dinamica): immettere il nome del Azure Active Directory (ad). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/), nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.

- **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e).

- **Azure ad segreto client App** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu a sinistra, selezionare **registrazioni app**, quindi selezionare l'app test drive. Selezionare quindi **certificati e segreti**, fare clic **su nuovo segreto client**, immettere una descrizione, selezionare **mai** sotto **scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. Non uscire dalla pagina prima di eseguire questa operazione, altrimenti non sarà possibile accedere al valore.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

### <a name="test-drive-marketplace-listings"></a>Test Drive Marketplace elenchi

L'opzione di **elenco del Marketplace** disponibile nella scheda **test drive** Visualizza le lingue in cui è disponibile il test drive. Attualmente la **lingua inglese (Stati Uniti)** è l'unica località disponibile. Selezionare il nome della lingua per immettere le informazioni che descrivono l'esperienza test drive.

- **Descrizione** (obbligatoria): descrivere la test drive, gli elementi che verranno illustrati, gli obiettivi dell'utente per sperimentare, le funzionalità da esplorare e tutte le informazioni rilevanti per aiutare l'utente a determinare se acquisire l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

- **Informazioni di accesso** (obbligatorie per Azure Resource Manager e unità di test per la logica): spiegare cosa è necessario sapere al cliente per accedere a questo test drive e usarlo. Esaminare uno scenario per l'uso dell'offerta e esattamente ciò che il cliente deve conoscere per accedere alle funzionalità nell'test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale dell'utente** (obbligatorio): una procedura dettagliata approfondita dell'esperienza test drive. Il manuale dell'utente dovrebbe coprire esattamente ciò che si vuole che il cliente possa ottenere dall'esperienza del test drive e fungere da riferimento per eventuali domande. Il file deve essere in formato PDF ed essere denominato (255 caratteri al massimo) dopo il caricamento.

- **Video: aggiungere video** (facoltativo): è possibile caricare video in YouTube o Vimeo e farvi riferimento con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una procedura dettagliata per comprendere meglio le test drive, tra cui come usare correttamente le funzionalità dell'offerta e comprendere gli scenari in cui vengono evidenziati i vantaggi.
  - **Nome** (obbligatorio)
  - **URL (solo YouTube o Vimeo)** (obbligatorio)
  - **Anteprima (533 x 324 px)** : il file di immagine deve essere in formato png.

## <a name="supplemental-content"></a>Contenuto supplementare

Questa pagina consente di fornire informazioni aggiuntive sull'offerta che consentono di convalidare l'offerta. Queste informazioni non vengono visualizzate ai clienti o pubblicate sul Marketplace.

### <a name="target-release"></a>Versione di destinazione

Indicare la versione di Microsoft Dynamics Business Central a cui è destinata la soluzione: **corrente**, **successiva principale**o **secondaria successiva**. Queste informazioni consentono di testare la soluzione in modo appropriato.

### <a name="supported-editions"></a>Edizioni supportate

Se l'offerta richiede l'edizione Premium di Microsoft Dynamics 365 business Central, selezionare solo **Premium** . In caso contrario, selezionare sia **Essentials** che **Premium**.

### <a name="key-usage-scenario"></a>Scenario di utilizzo chiave

È necessario pload un file `.pdf` in cui sono elencati gli scenari di utilizzo chiave dell'offerta elencati in un documento (formato PDF). Tutti gli scenari elencati di seguito possono essere verificati dal team di convalida prima di approvare l'offerta per il Marketplace.

### <a name="app-tests-automation"></a>Automazione di test app

Facoltativamente, è possibile caricare un file di **automazione di test di app** qui (. app).

### <a name="test-accounts"></a>Account di test

Se è necessario un account di test per consentire al team di certificazione di rivedere correttamente l'offerta, caricare un file PDF, doc o docx con le informazioni sugli **account di test** .

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Una volta completate tutte le sezioni obbligatorie dell'offerta, selezionare **pubblica** nell'angolo superiore destro del portale. Si verrà reindirizzati alla pagina di **revisione e pubblicazione** . 

Se è la prima volta che si pubblica questa offerta, è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta.
    - *Non avviato* : indica che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o che sono necessarie altre informazioni. Tornare alla sezione o aggiornarla.
    - *Complete* : indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** , fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note aggiuntive utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Ti invieremo un messaggio di posta elettronica quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta al pubblico (o se un'offerta privata, per i destinatari privati).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel Marketplace commerciale](./update-existing-offer.md)
