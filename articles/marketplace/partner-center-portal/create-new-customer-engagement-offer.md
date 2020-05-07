---
title: Creare un'offerta di Dynamics 365 per Customer Engagement & PowerApps nel Marketplace commerciale Microsoft
description: Come creare un nuovo Dynamics 365 per Customer Engagement & offerta PowerApps per l'inserzione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) nel centro per i partner.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: cfec905d61daf961d9ca71026e9b7ec82f852bd5
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734774"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Creare un'offerta Dynamics 365 for Customer Engagement e PowerApps

Questo argomento illustra come creare una nuova offerta di Dynamics 365 per Customer Engagement & PowerApps. Tutte le app per Dynamics 365 per l'engagement dei clienti (PowerApps, Sales, Service, Project Service e Field Service) devono seguire il processo di certificazione e supportare un'esperienza di valutazione. Il processo di certificazione verifica che la soluzione soddisfi i requisiti standard, la compatibilità e l'efficienza nelle procedure consigliate. mentre il percorso di valutazione consente agli utenti di distribuire la soluzione in un ambiente Dynamics 365 reale.

Prima di iniziare, [creare un account Marketplace commerciale nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma Commercial Marketplace.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di navigazione a sinistra selezionare **Commercial Marketplace** > **Overview**.
3. Nella pagina Panoramica selezionare **+ nuova offerta** > **Dynamics 365 per Customer Engagement & PowerApps**.

    ![Viene illustrato il menu di spostamento a sinistra.](./media/new-offer-dynamics-365-cepa.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner vengono visualizzate solo nelle vetrine dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre dopo avere apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta Marketplace e Azure Resource Manager modelli, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un **alias dell'offerta**. Questo è il nome usato per l'offerta nel centro per i partner.

- Questo nome non viene usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati per i clienti.
- Questa operazione non può essere modificata dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Installazione dell'offerta

Per configurare l'offerta, seguire questa procedura.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>In che modo si desidera che i clienti possano interagire con questa offerta di inserzione?

Selezionare l'opzione che si vuole usare per questa offerta.

#### <a name="get-it-now-free"></a>Ottieni ora (gratuito)

Elenca gratuitamente l'offerta ai clienti fornendo un URL valido (a partire da *http* o *https*) dove possono accedere all'app.  Ad esempio, `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Versione di valutazione gratuita (elenco)

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (a partire da *http* o *https*) dove è possibile ottenere una versione di valutazione.  Ad esempio, `https://contoso.com/trial/my-app` Le versioni di valutazione gratuite dell'elenco di offerte vengono create, gestite e configurate dal servizio e non hanno sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione può essere usato solo per ottenere informazioni sugli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Raccogliere le informazioni di contatto del cliente connettendosi al sistema CRM (Customer Relationship Management). Al cliente verrà richiesta l'autorizzazione a condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e all'origine del Marketplace in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Per ulteriori informazioni sulla configurazione del CRM, vedere [Connect Lead Management](#connect-lead-management). 

Selezionare **Salva bozza** prima di continuare.

### <a name="test-drive"></a>Test drive

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di "provare prima di acquistare", ottenendo una maggiore conversione e la generazione di lead altamente qualificati. [Altre informazioni sulle unità di test](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita una test drive** . Per rimuovere test drive dall'offerta, deselezionare questa casella di controllo. Configurare l'ambiente di test drive nella sezione [configurazione tecnica test drive](#test-drive-technical-configuration) più avanti in questo argomento.

Per ulteriori informazioni, vedere [test drive dell'offerta nel Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Tipo di test drive

Selezionare una delle opzioni seguenti:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : un modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che soddisfano questo scenario usano solo le risorse di Azure.
- **[Dynamics 365 for business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft ospita e gestisce il servizio test drive (inclusi il provisioning e la distribuzione) per un sistema di pianificazione delle risorse aziendali centrale (finanza, operazioni, supply chain, CRM e così via).  
- **[Dynamics 365 per il coinvolgimento dei clienti](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft ospita e gestisce il servizio test drive (inclusi il provisioning e la distribuzione) per un sistema di engagement dei clienti (vendite, servizio, servizio del progetto, servizio campo e così via).  
- **[Dynamics 365 per le operazioni](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft ospita e gestisce il servizio test drive (inclusi il provisioning e la distribuzione) per un sistema di pianificazione delle risorse aziendali finanziarie e operative (finanza, operazioni, produzione, supply chain e così via). 
- **[App](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** per la logica: modello di distribuzione che comprende tutte le architetture di soluzioni complesse. Tutti i prodotti personalizzati devono usare questo tipo di test drive.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : un collegamento incorporato a un dashboard personalizzato. I prodotti che vogliono dimostrare un oggetto visivo Power BI interattivo devono usare questo tipo di test drive. È sufficiente caricare l'URL di Power BI incorporato qui.

#### <a name="additional-test-drive-resources"></a>Risorse test drive aggiuntive

- [Procedure consigliate tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Procedure consigliate per il marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Panoramica di test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (assicurarsi che il blocco popup sia disattivato)

## <a name="connect-lead-management"></a>Gestione dei lead di connessione

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per altre informazioni, vedere [Cenni preliminari sulla gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Selezionare **Salva bozza** prima di continuare.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel Marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="category"></a>Categoria

Selezionare almeno una e fino a tre categorie. Questi vengono usati per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta.

### <a name="industry"></a>Settore

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Applicable Dynamics 365 products (Prodotti Dynamics 365 applicabili)

Selezionare tutti i prodotti Dynamics 365 a cui si applica questa offerta.

### <a name="app-version"></a>Versione dell'app

Immettere il numero di versione dell'offerta. I clienti visualizzeranno questa versione nella pagina dei dettagli dell'offerta. Se si aggiorna il numero di versione solo a causa di modifiche di marketing/descrittive, selezionare la casella di **modifica solo marketing** . Questa opzione consente all'offerta di ignorare le fasi di certificazione e provisioning.

### <a name="terms-and-conditions"></a>Termini e condizioni

Fornire qui i termini e le condizioni legali. È anche possibile specificare l'indirizzo in cui è possibile trovare i termini e le condizioni. Ai clienti verrà richiesto di accettare le presenti condizioni per poter provare l'offerta.

Selezionare **Salva bozza** prima di continuare.

## <a name="offer-listing"></a>Elenco offerte

In questa pagina vengono visualizzate le lingue in cui verrà elencata l'offerta. Attualmente, l' **inglese (Stati Uniti)** è l'unica opzione disponibile.

Definire qui i dettagli del Marketplace per ogni lingua/mercato, ad esempio il nome dell'offerta, la descrizione e le immagini. Selezionare il nome del mercato/lingua per fornire queste informazioni.

> [!NOTE]
> Non è necessario che il contenuto dell'offerta (ad esempio la descrizione, i documenti, le schermate e le condizioni per l'utilizzo) sia in inglese, purché la descrizione dell'offerta inizi con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche accettabile fornire un URL di *collegamento utile* per offrire contenuto in una lingua diversa da quella usata nell'offerta di visualizzazione del contenuto.

### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato ai clienti come titolo dell'elenco di offerte. Questo campo viene prepopolato con il testo immesso per l' **alias offerta** al momento della creazione dell'offerta, ma è possibile modificare questo valore. Questo nome può essere registrato (e possono essere inclusi marchi o simboli di copyright). Il nome non può contenere più di 50 caratteri e non può includere emoji.

### <a name="short-description"></a>Breve descrizione

Fornire una breve descrizione dell'offerta, fino a 100 caratteri. Questa descrizione può essere usata nei risultati della ricerca nel Marketplace.

### <a name="description"></a>Descrizione

Fornire una descrizione più lunga dell'offerta, fino a 3.000 caratteri. Questa descrizione verrà visualizzata ai clienti nella panoramica dell'inserzione sul Marketplace. Includere la proposta di valore dell'offerta, i vantaggi principali, le associazioni di categoria e/o di settore, le opportunità di acquisto in-app ed eventuali divulgazioni necessarie.

Alcuni suggerimenti per scrivere la descrizione:

- Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includere le seguenti informazioni nella proposta di valore:
  - Descrizione del prodotto
  - Il tipo di utente che trae vantaggio dal prodotto
  - Esigenze del cliente o dolore che il prodotto indirizzi
- Tenere presente che le prime frasi possono essere visualizzate nei risultati dei motori di ricerca.  
- Per vendere un prodotto, non basarsi sulle funzionalità, ma concentrarsi sul valore che può offrire.  
- Usare il più possibile la terminologia specifica del settore o formule che mettano in evidenza i vantaggi offerti.

Per rendere più accattivante la descrizione dell'offerta, usare l'editor di testo RTF per applicare la formattazione.

![Uso dell'editor di testo RTF](./media/rich-text-editor.png)

| <center>Modificare il formato del testo | <center>Aggiungi elenchi puntati o numerati | <center>Aggiungere o rimuovere il rientro del testo |
| --- | --- | --- |
| <center>![Utilizzo dell'editor di testo RTF per modificare il formato del testo](./media/text-editor3.png) |  <center>![Uso dell'editor di testo RTF per aggiungere elenchi](./media/text-editor4.png) |  <center>![Utilizzo dell'editor di testo RTF per rientrare](./media/text-editor5.png) |


### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Facoltativamente, è possibile immettere fino a tre parole chiave di ricerca per aiutare i clienti a trovare l'offerta nel Marketplace. Per ottenere risultati ottimali, usare anche queste parole chiave nella descrizione.

### <a name="products-your-app-works-with"></a>Prodotti interrelati con l'app

Se si vuole informare i clienti che l'app funziona con prodotti specifici, immettere qui fino a tre nomi di prodotti.

### <a name="support-urls"></a>URL di supporto

Questa sezione consente di fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="help-link"></a>Collegamento alla guida

Immettere l'indirizzo in cui i clienti possono ottenere ulteriori informazioni sull'offerta.

#### <a name="privacy-policy-url"></a>URL informativa sulla privacy

Immettere l'indirizzo dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida.

### <a name="contacts"></a>Contatti

Specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per un **contatto di supporto** e un **contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e potranno essere fornite ai partner CSP.

Nella sezione **Contact Support** è necessario fornire anche l'URL di **supporto** in cui i partner CSP possono trovare il supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire qui almeno uno (e tre) documenti di marketing correlati, ad esempio white paper, brochure, elenchi di controllo o presentazioni, in formato PDF.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire logo e immagini per l'offerta. Tutte le immagini devono essere in formato PNG.

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

#### <a name="store-logos"></a>Logo dello Store

Fornire il logo dell'offerta in tre dimensioni in pixel:
- **Small** (obbligatorio; 48 x 48)
- **Grande** (obbligatorio; 216 x 216)
- **Wide** (facoltativo; 255 x 115)

#### <a name="hero"></a>Banner

L'immagine Hero è facoltativa. Se ne viene fornito uno, deve misurare 815 x 290 pixel.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere schermate che mostrano il funzionamento dell'offerta. È necessario almeno una schermata ed è possibile aggiungerne fino a cinque. Tutte le schermate devono avere 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, è possibile aggiungere fino a quattro video che illustrano l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ciascuna di esse, immettere il nome del video, il relativo URL e un'immagine di anteprima del video (1280 x 720 pixel)

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

[Procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Selezionare **Salva bozza** prima di continuare.

## <a name="availability"></a>Disponibilità

Questa pagina consente di definire dove e come rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Specificare i mercati in cui l'offerta deve essere disponibile. A tale scopo, selezionare **modifica mercati**. viene visualizzata la finestra popup **selezione mercato** .

Per impostazione predefinita, non è selezionato alcun mercato, ma è necessario selezionare almeno un mercato per pubblicare l'offerta. Scegliere **Seleziona tutto** per rendere disponibile l'offerta in ogni possibile mercato oppure selezionare i mercati specifici che si desidera aggiungere.

Le selezioni in questo articolo si applicano solo alle nuove acquisizioni; Se un utente dispone già di un'app in un determinato mercato e successivamente si rimuove tale mercato, le persone che dispongono già dell'offerta in quel mercato possono continuare a utilizzarlo, ma nessun nuovo cliente del mercato potrà usufruire dell'offerta.

> [!IMPORTANT]
> È responsabilità dell'utente soddisfare eventuali requisiti legali locali, anche se tali requisiti non sono elencati qui o nel centro per i partner. Anche se si selezionano tutti i mercati, le leggi locali e le restrizioni o altri fattori possono impedire che alcune offerte siano elencate in alcuni paesi e regioni.

### <a name="preview-audience"></a>Anteprima destinatari

Prima di pubblicare l'offerta in tempo reale nell'offerta del Marketplace più ampia, sarà prima di tutto necessario renderla disponibile per un gruppo di **destinatari di anteprima**limitato. Immettere un **tasto Nascondi** (qualsiasi stringa che usa solo lettere minuscole e/o numeri) qui. I membri dei destinatari di anteprima possono usare questa chiave Hide come token per visualizzare un'anteprima dell'offerta nel Marketplace.

Quindi, quando si è pronti per rendere disponibile l'offerta e rimuovere la restrizione di anteprima, è necessario rimuovere la **chiave Hide** e pubblicare di nuovo.

Selezionare **Salva bozza** prima di continuare.

## <a name="technical-configuration"></a>Configurazione tecnica

Questa pagina definisce i dettagli tecnici usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquisirla.

### <a name="base-license-model"></a>Base license model (Modello di licenza base)

Il modello di licenza di base determina il modo in cui i clienti vengono assegnati all'applicazione nell'interfaccia di amministrazione di CRM. Selezionare la **risorsa** per le licenze basate su istanze o l' **utente** se le licenze vengono assegnate una per ogni tenant.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Richiede l'accesso S2S in uscita e in archiviazione sicura CRM

Selezionare questa casella per abilitare la configurazione dell'accesso in uscita da server a server (S2S) o da un archivio sicuro CRM. Questa funzionalità richiede una considerazione speciale da parte del Team di Dynamics 365 durante la fase di certificazione. Microsoft contatterà l'utente per completare alcuni passaggi aggiuntivi per supportare questa funzionalità.

### <a name="application-configuration-url"></a>URL di configurazione dell'applicazione

Specificare l'URL per la pagina Web di configurazione che consente al cliente di configurare l'app.

### <a name="crm-package"></a>Pacchetto CRM

Nel campo **URL del percorso del pacchetto** immettere l'URL di un account di archiviazione di Azure che contiene il file zip del pacchetto CRM caricato. Questo URL deve includere una chiave di firma di accesso condiviso di sola lettura per consentire a Microsoft di selezionare il pacchetto per la verifica.

Selezionare la casella in cui **è presente più di un pacchetto CRM nel file del pacchetto**, se applicabile. In tal caso, assicurarsi di includere tutti i pacchetti nel file zip.

### <a name="crm-package-availability"></a>Disponibilità pacchetto CRM

In questa sezione selezionare **+ Aggiungi area** per specificare le aree geografiche in cui il pacchetto CRM sarà disponibile per i clienti. La distribuzione nelle seguenti aree sovrane richiede l'autorizzazione e la convalida speciali durante il processo di certificazione: [Germania](https://docs.microsoft.com/azure/germany/), [cloud degli Stati Uniti](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)e suggerimento.

Per impostazione predefinita, l' **URL di configurazione dell'applicazione** immesso in precedenza verrà usato per ogni area. Se si preferisce, è possibile immettere un URL di configurazione dell'applicazione distinto per una o più aree specifiche. 

Selezionare **Salva bozza** prima di continuare.

## <a name="test-drive-technical-configuration"></a>Configurazione tecnica test drive

Questa pagina consente di configurare una dimostrazione ("test drive") che consente ai clienti di provare l'offerta prima di acquistarla. Per altre informazioni, vedere l'articolo relativo a [test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Per abilitare una test drive, selezionare la casella di controllo Abilita un test drive nella scheda [installazione offerta](#test-drive) . Per rimuovere test drive dall'offerta, deselezionare questa casella di controllo.

Per altre informazioni, vedere l'articolo relativo a [test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Sono disponibili i tipi di unità di test seguenti, ognuno con requisiti di configurazione tecnici specifici:

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [App per la logica](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (configurazione tecnica non necessaria)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configurazione tecnica per Azure Resource Manager test drive

Un modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che soddisfano questo scenario usano solo le risorse di Azure. Altre informazioni sulla configurazione di un [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regions** (obbligatorio): attualmente sono presenti 26 aree supportate da Azure in cui è possibile rendere disponibili le test drive. In genere, è consigliabile rendere le test drive disponibili nelle aree in cui si prevede il maggior numero di clienti, in modo che possano selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che alla sottoscrizione sia consentita la distribuzione di tutte le risorse necessarie in ognuna delle aree selezionate.
- **Istanze** : selezionare il tipo (Hot o Cold) e il numero di istanze disponibili, che verranno moltiplicate per il numero di aree in cui è disponibile l'offerta.

    Accesso **frequente: questo** tipo di istanza viene distribuito e in attesa di accesso per area selezionata. I clienti possono accedere immediatamente alle istanze a *caldo* di una test drive, anziché dover attendere una distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. Si consiglia di disporre di almeno un'istanza di accesso frequente, in quanto la maggior parte dei clienti non desidera attendere le distribuzioni complete, causando un calo nell'utilizzo del cliente se non è *disponibile un'istanza* a *caldo* .

    **Cold** : questo tipo di istanza rappresenta il numero totale di istanze che possono essere distribuite per ogni area. Per le istanze a freddo è necessario che l'intero test drive Gestione risorse modello venga distribuito quando un cliente richiede l'test drive, in modo che le istanze a *freddo* risultino molto più lente del caricamento rispetto alle istanze a *caldo* . Il compromesso è dovuto al fatto che è necessario pagare solo per la durata del test drive, *non* è sempre in esecuzione nella sottoscrizione di Azure come con *un'istanza* attiva.

- **Test drive Azure Resource Manager modello** : caricare il file zip contenente il modello di Azure Resource Manager. Per altre informazioni sulla creazione di un modello di Azure Resource Manager, vedere l'articolo introduttivo [creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durata test drive** (obbligatorio): immettere il numero di ore per cui il test drive resterà attivo. Il testdDrive termina automaticamente al termine di questo periodo di tempo. Questa durata può essere impostata solo in ore intere (ad esempio, "2" ore è valida; "1,5" non è).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configurazione tecnica per Dynamics 365 test drive

Microsoft può rimuovere la complessità della configurazione di un test drive ospitando e gestendo il provisioning e la distribuzione del servizio usando questo tipo di test drive. La configurazione di questo tipo di test drive ospitata è la stessa, indipendentemente dal fatto che la test drive sia destinata a un gruppo di lavoro centrale, Customer Engagement o Operations.

- Numero massimo di **unità di test simultanee** (obbligatorio): impostare il numero massimo di clienti che possono usare la test drive alla volta. Ogni utente simultaneo utilizzerà una licenza Dynamics 365 mentre la test drive è attiva, pertanto sarà necessario assicurarsi che siano disponibili sufficienti licenze per supportare il limite massimo impostato. Il valore consigliato è 3-5.

- **Durata test drive** (obbligatorio): immettere il numero di ore per cui il test drive resterà attivo. Dopo questo periodo di tempo, la sessione terminerà e non utilizzerà più una delle licenze. È consigliabile un valore di 2-24 ore a seconda della complessità dell'offerta. Questa durata può essere impostata solo in ore intere (ad esempio, "2" ore è valida; "1,5" non è). L'utente può richiedere una nuova sessione se esaurisce il tempo e desidera accedere nuovamente al test drive.

- **URL istanza** (obbligatorio): URL da cui il cliente inizierà a test drive. In genere l'URL dell'istanza di Dynamics 365 che esegue l'app con i dati di esempio installati `https://testdrive.crm.dynamics.com`(ad esempio,).

- **URL dell'API Web dell'istanza** (obbligatorio): recuperare l'URL dell'API Web per l'istanza di Dynamics 365 accedendo all'account Microsoft 365 e passando alle **Impostazioni** \&gt; **Personalizzazione** \&gt; **Risorse** \&per sviluppatori gt; **API Web dell'istanza (URL radice del servizio)**, copiare l'URL trovato qui (ad `https://testdrive.crm.dynamics.com/api/data/v9.0`esempio,).

- **Nome ruolo** (obbligatorio): specificare il nome del ruolo di sicurezza definito nell'test drive Dynamics 365 personalizzato. Il nome del ruolo di sicurezza verrà assegnato all'utente durante la relativa test drive (ad esempio, test-drive-Role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configurazione tecnica per test drive app per la logica

Tutti i prodotti personalizzati devono usare questo tipo di modello di distribuzione test drive, che include un'ampia gamma di architetture di soluzioni complesse. Per altre informazioni sulla configurazione di unità di test per l'app per la logica, vedere [operazioni](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [coinvolgimento dei clienti](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) su GitHub.

- **Region** (obbligatorio, elenco a discesa a selezione singola): attualmente sono presenti 26 aree supportate da Azure in cui è possibile rendere disponibili le test drive. Le risorse per l'app per la logica verranno distribuite nell'area selezionata. Se l'app per la logica contiene risorse personalizzate archiviate in un'area specifica, assicurarsi che l'area sia selezionata qui. Il modo migliore per eseguire questa operazione consiste nel distribuire completamente l'app per la logica localmente nella sottoscrizione di Azure nel portale e verificare che funzioni correttamente prima di effettuare questa selezione.

- Numero massimo di **unità di test simultanee** (obbligatorio): impostare il numero massimo di clienti che possono usare la test drive alla volta. Queste unità di test sono già distribuite, consentendo ai clienti di accedervi immediatamente senza attendere una distribuzione.

- **Durata test drive** (obbligatorio): immettere il periodo di tempo in cui il test drive resterà attivo, in ore. Il test drive termina automaticamente al termine di questo periodo di tempo.

- **Nome del gruppo di risorse di Azure** (obbligatorio): immettere il nome del [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in cui viene salvata l'App per la logica test drive.

- **Nome dell'app** per la logica di Azure (obbligatorio): immettere il nome dell'app per la logica che assegna la test drive all'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- **Deprovisioning app per la logica nome** (obbligatorio): immettere il nome dell'app per la logica che esegue il deprovisioning del test drive al termine del cliente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configurazione tecnica non necessaria per Power BI test drive

I prodotti che vogliono dimostrare un oggetto visivo Power BI interattivo possono usare un collegamento incorporato per condividere un dashboard personalizzato come test drive, non sono necessarie altre configurazioni tecniche. Altre informazioni sulla configurazione di[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) app modello.

### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione della distribuzione

Per consentire a Microsoft di distribuire il test drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca (non necessaria per Power BI test drive).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile [creare una sottoscrizione di Azure separata](https://docs.microsoft.com/azure/billing/billing-create-subscription) da usare per le unità di test se non ne è già presente uno. È possibile trovare l'ID sottoscrizione di Azure accedendo al [portale di Azure](https://portal.azure.com/) e passando alla scheda **sottoscrizioni** del menu a sinistra. Se si seleziona la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5AB6-6789-1h234g764ghty".

- **Azure ad ID tenant** (obbligatorio): immettere l' [id tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile cercare l'ID tenant dell'organizzazione usando l'URL del nome di dominio in [https://www.whatismytenantid.com](https://www.whatismytenantid.com)-.

- **Azure ad nome del tenant** (obbligatorio per la 365 dinamica): immettere il nome del Azure Active Directory (ad). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/), nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.

- **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e).

- **Azure ad segreto client App** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu a sinistra, selezionare **registrazioni app**, quindi selezionare l'app test drive. Selezionare quindi **certificati e segreti**, fare clic **su nuovo segreto client**, immettere una descrizione, selezionare **mai** sotto **scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. Non uscire dalla pagina prima di copiare il valore o non si avrà accesso al valore.

### <a name="test-drive-marketplace-listings"></a>Test Drive Marketplace elenchi

L'opzione di **elenco del Marketplace** disponibile nella scheda **test drive** Visualizza le lingue in cui è disponibile il test drive. Attualmente, la **lingua inglese (Stati Uniti)** è l'unica località disponibile. Selezionare il nome della lingua per immettere le informazioni che descrivono l'esperienza test drive.

- **Descrizione** (obbligatoria): descrivere la test drive, gli elementi che verranno illustrati, gli obiettivi dell'utente per sperimentare, le funzionalità da esplorare e tutte le informazioni rilevanti per aiutare l'utente a determinare se acquisire l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

- **Informazioni di accesso** (obbligatorie per Azure Resource Manager e unità di test per la logica): spiega cosa è necessario sapere al cliente per accedere a questo test drive e usarlo. Esaminare uno scenario per l'uso dell'offerta e esattamente ciò che il cliente deve conoscere per accedere alle funzionalità nell'test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale dell'utente** (obbligatorio): una procedura dettagliata dell'esperienza test drive. Il manuale dell'utente dovrebbe coprire esattamente ciò che si vuole che il cliente possa ottenere dall'esperienza del test drive e fungere da riferimento per eventuali domande. Il file deve essere in formato PDF ed essere denominato (255 caratteri al massimo) dopo il caricamento.

- **Video** (facoltativo): i video caricati in un sito di hosting esterno fanno riferimento al collegamento e all'immagine di anteprima (533 x 324 pixel) qui. Questo consente ai clienti di visualizzare una panoramica delle informazioni che consentono di comprendere meglio le test drive, tra cui come usare le funzionalità dell'offerta e comprendere gli scenari in cui vengono evidenziati i vantaggi.
  - **Nome** (obbligatorio)
  - **Indirizzo** (obbligatorio; Solo YouTube o Vimeo)
  - Immagine di **Anteprima** (il file di immagine deve essere in formato PNG e 533 x 324 pixel)

Selezionare **Salva bozza** prima di continuare.

## <a name="supplemental-content"></a>Contenuto supplementare

Questa pagina consente di fornire informazioni aggiuntive sull'offerta che consentono di convalidare l'offerta. Queste informazioni non vengono visualizzate ai clienti o pubblicate sul Marketplace.

### <a name="key-usage-scenario"></a>Scenario di utilizzo chiave

Caricare un file PDF in cui sono elencati gli scenari di utilizzo chiave dell'offerta. Tutti gli scenari possono essere verificati dal team di convalida prima di approvare l'offerta per il Marketplace.

Selezionare **Salva bozza** prima di continuare.

## <a name="publish"></a>Pubblicazione

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, selezionare **revisione e pubblicazione** nell'angolo superiore destro del portale.

Se è la prima volta che si pubblica questa offerta, è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta.
    - **Non avviato** : la sezione non è stata toccata e deve essere completata.
    - **Incompleto** : la sezione contiene errori che devono essere corretti o sono necessarie altre informazioni. Tornare alla sezione o aggiornarla.
    - **Completato** : la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** , fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note aggiuntive utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Verrà inviato un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Tornare al centro per i partner e selezionare **Go-Live** per l'offerta per la pubblicazione nel pubblico (o se un'offerta privata, per i destinatari privati).

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
