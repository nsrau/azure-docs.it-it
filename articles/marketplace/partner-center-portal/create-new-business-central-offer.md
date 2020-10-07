---
title: Creare un'offerta Dynamics 365 Business Central - Marketplace commerciale Microsoft
description: Informazioni sui passaggi e le considerazioni per la creazione di una nuova offerta di Dynamics 365 business Central in Microsoft Commercial Marketplace nel centro per i partner. È possibile presentare o vendere un'offerta in Azure Marketplace o tramite il programma Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: bc34d2044c3a91fe18e900b21d589dde855754d3
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91774579"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Creare un'offerta Dynamics 365 Business Central

Questo articolo descrive come creare una nuova offerta Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) è un sistema ERP (Enterprise Resource Planning) per la gestione di processi aziendali in un'ampia gamma di settori, tra cui finanza, operazioni, catena di approvvigionamento, CRM, gestione dei progetti e commercio elettronico. I pacchetti Premium supportano anche il modello di distribuzione classica e la produzione. Tutte le offerte per Dynamics 365 Business Central devono essere sottoposte al processo di certificazione.

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento a sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Dynamics 365 Business Central**.

    ![Menu di spostamento a sinistra.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner vengono visualizzate solo negli archivi online dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere l'ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- L'ID offerta combinato con l'ID editore deve avere una lunghezza compresa tra 40 caratteri.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi. Se, ad esempio, l'ID editore è testpublisherid e si immette **test-offer-1** qui, l'indirizzo Web dell'offerta sarà `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

Seguire questa procedura per configurare l'offerta.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Come si vuole che i potenziali clienti interagiscano con questa offerta?

Selezionare l'opzione che si vuole usare per l'offerta.

#### <a name="get-it-now-free"></a>Scarica ora (gratuito)

Elenca gratuitamente l'offerta ai clienti.

#### <a name="free-trial-listing"></a>Free trial (listing) (Versione di valutazione gratuita - Presentazione)

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni degli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Consente di raccogliere informazioni di contatto del cliente tramite la connessione al sistema di gestione delle relazioni con i clienti (CRM, Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Clienti potenziali](#customer-leads).

### <a name="test-drive"></a>Test drive

A questo punto le offerte di Dynamics 365 business Central non supportano test drive. Per rimuovere test drive dall'offerta, deselezionare la casella di controllo **Abilita una test drive** .

### <a name="customer-leads"></a>Clienti potenziali

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per altre informazioni, vedere [Panoramica della gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="category"></a>Category

Selezionare le categorie e le sottocategorie per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta. Selezionare:

- Almeno una delle due categorie, incluse una categoria primaria e una secondaria (facoltativo).
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**.

Vedere l'elenco completo di categorie e sottocategorie nell'elenco delle [procedure](../gtm-offer-listing-best-practices.md)consigliate.

### <a name="industry"></a>Settore

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versione dell'app

Immettere il numero di versione dell'offerta. I clienti visualizzeranno questa versione nella pagina dei dettagli dell'offerta.

### <a name="terms-and-conditions"></a>Termini e condizioni

Specificare i propri termini e condizioni legali nel campo **Termini e condizioni**. È anche possibile specificare l'URL in cui sono disponibili i termini e le condizioni. Ai clienti verrà chiesto di accettare questi termini e condizioni prima di poter provare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

Qui è possibile definire i dettagli per l'offerta, ad esempio il nome, la descrizione e le immagini.

> [!NOTE]
> È possibile fornire i dettagli per la presentazione dell'offerta solo in una lingua. Non è necessario usare l'inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È inoltre accettabile fornire un URL per *Collegamento alla Guida* per offrire contenuti in una lingua diversa da quella usata nei contenuti per la presentazione dell'offerta.

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Microsoft AppSource (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo
2. Prodotti
3. Categorie
4. Indirizzo di supporto (collegamento)
5. Condizioni per l'utilizzo
6. Informativa sulla privacy
7. Nome offerta
8. Riepilogo
9. Descrizione
10. Screenshot/video

### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato ai clienti come titolo della presentazione dell'offerta. Questo campo viene precompilato con il testo immesso per **Alias offerta** quando è stata creata l'offerta, ma è possibile modificarne il valore. Questo nome può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright). Il nome non può essere costituito da più di 50 caratteri e non può includere emoji.

### <a name="short-description"></a>Breve descrizione

Fornire una breve descrizione dell'offerta, fino a 100 caratteri. Questa descrizione può essere usata nei risultati della ricerca del marketplace.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Se si vuole, è possibile immettere fino a tre parole chiave di ricerca per aiutare i clienti a trovare l'offerta nel marketplace. Per ottenere risultati ottimali, provare a usare queste parole chiave anche nella descrizione.

### <a name="products-your-app-works-with"></a>Prodotti interrelati con l'app

Se si vuole informare i clienti che l'app funziona con prodotti specifici, immettere qui fino a tre nomi di prodotto.

### <a name="helpprivacy-urls"></a>URL della Guida/URL privacy

Questa sezione consente di fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="help-link"></a>Collegamento alla Guida

Immettere l'URL in cui i clienti possono ottenere altre informazioni sull'offerta. Il valore di **Collegamento alla Guida** non può essere uguale a quello di **URL supporto** (descritto di seguito).

#### <a name="privacy-policy-link"></a>Collegamento dell'Informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. Si è tenuti a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida.

### <a name="contact-information"></a>Informazioni sul contatto

In questa sezione è necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto di supporto** e **Contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP.

Nella sezione **Contatto di supporto** è necessario fornire anche un valore per **URL supporto** in cui i partner CSP possono trovare supporto per l'offerta. L'URL del supporto non può essere lo stesso di quello immesso in **Collegamento alla Guida**.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire qui almeno un documento di marketing correlato (e fino a un massimo di tre), ad esempio white paper, brochure, elenchi di controllo o presentazioni. Questi documenti devono essere in formato PDF.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire un file PNG per il logo di **grandi** dimensioni. Il centro per i partner utilizzerà questo per creare un logo di **piccole dimensioni** . Facoltativamente, è possibile sostituire questo oggetto con un'immagine diversa in un secondo momento.

- **Grande** (da 216 x 216 a 350 x 350 px, obbligatorio)
- **Piccolo** (48 x 48 px, facoltativo)

Questi logo vengono usati in posizioni diverse nell'elenco:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio `https://upload.xboxlive.com` usato dal Centro per i partner.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere screenshot che mostrano il funzionamento dell'offerta. Sono necessari almeno tre screenshot ed è possibile aggiungerne fino a cinque. Tutti gli screenshot devono avere dimensioni di 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, è possibile aggiungere fino a cinque video che descrivono l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ogni video, immettere il nome, l'URL e un'immagine di anteprima (1280 x 720 pixel).

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

[Procedure consigliate per la presentazione di offerte nel marketplace](../gtm-offer-listing-best-practices.md)

Prima di continuare, selezionare **Salva bozza**.

## <a name="availability"></a>Disponibilità

In questa pagina sono incluse le opzioni relative a dove e come rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Questa sezione consente di specificare i mercati in cui l'offerta deve essere disponibile. A questo scopo, selezionare **Modifica mercati**. Verrà visualizzata la finestra popup **Selezione mercato**.

Selezionare almeno un mercato in cui pubblicare l'offerta. Scegliere **Seleziona tutto** per rendere l'offerta disponibile in ogni mercato possibile oppure selezionare i mercati specifici da aggiungere.

Le opzioni qui si applicano solo ai nuovi acquisti. Se un cliente possiede già l'app in un determinato mercato e successivamente si rimuove il mercato, il cliente può continuare a usarla, ma nessun nuovo cliente in tale mercato potrà ottenere l'offerta.

> [!IMPORTANT]
> Si è tenuti a soddisfare i requisiti legali locali, anche se non sono elencati qui o nel Centro per i partner.

Tenere presente che anche se si selezionano tutti i mercati, le leggi locali, le restrizioni o altri fattori possono impedire la presentazione di alcune offerte in determinati paesi e regioni.

### <a name="preview-audience"></a>Visualizza in anteprima gruppo di destinatari

Prima di pubblicare l'offerta live all'interno della più ampia offerta del marketplace, è necessario renderla disponibile a un **gruppo di destinatari dell'anteprima** limitato. Immettere qui un valore in **Nascondi chiave** (qualsiasi stringa che usa solo lettere minuscole e/o numeri). I membri del gruppo di destinatari in anteprima possono usare questa chiave come token per visualizzare un'anteprima dell'offerta nel marketplace.

Quindi, quando si è pronti a rendere disponibile l'offerta e a rimuovere la restrizione, sarà necessario rimuovere la chiave specificata in **Nascondi chiave** e ripubblicare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

Questa pagina definisce i dettagli tecnici usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquistarla.

### <a name="package-type"></a>Tipo di pacchetto

Selezionare l'opzione che si applica all'offerta:

* **Componente aggiuntivo**: app che funge da componente aggiuntivo per estendere l'esperienza e le funzionalità esistenti di Dynamics 365 Business Central. Per informazioni dettagliate, vedere [App componente aggiuntivo](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Connessione**: è possibile usare un'app di connessione nello scenario in cui deve essere stabilita una connessione punto a punto tra Dynamics 365 Business Central e una soluzione o un servizio di terze parti. Per informazioni dettagliate, vedere [App di connessione](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Caricamento di file

Se sopra è stata selezionata l'opzione **Componente aggiuntivo**, questa opzione consente di caricare il file del pacchetto dell'offerta, insieme ai file di pacchetto per qualsiasi estensione da cui ha dipendenze.

#### <a name="extensions-package-file"></a>File del pacchetto dell'estensione

Caricare il file del pacchetto dell'estensione (con estensione app) per l'offerta.

#### <a name="library-package-file"></a>File del pacchetto dell'estensione della libreria

Obbligatorio se l'offerta deve essere installata insieme a un'altra estensione che non verrà pubblicata nel marketplace. In questo caso, caricare qui il file con estensione app.

>[!NOTE]
>Il file del pacchetto di dipendenze non viene più utilizzato. Caricare invece un file del pacchetto di libreria.

### <a name="url-to-app-installation"></a>URL per l'installazione dell'app

Se in precedenza è stata selezionata l'opzione **Connessione**, specificare qui l'indirizzo per l'installazione dell'app. Per i servizi connessi che non richiedono l'installazione, specificare l'indirizzo della pagina di destinazione o la pagina di iscrizione del servizio.

Prima di continuare, selezionare **Salva bozza**.

## <a name="test-drive-technical-configuration"></a>Configurazione tecnica del test drive

In questa pagina è possibile configurare una dimostrazione ("test drive") che consente ai clienti di provare l'offerta prima di acquistarla. Per altre informazioni, vedere [che cos'è test drive](../what-is-test-drive.md).

Per abilitare un test drive, selezionare la casella di controllo **Abilita un test drive** nella scheda [Configurazione dell'offerta](#test-drive). Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Al termine della configurazione del test drive, selezionare **Salva bozza** prima di continuare.

## <a name="supplemental-content"></a>Contenuto supplementare

In questa pagina è possibile fornire informazioni aggiuntive sull'offerta per consentirne la convalida. Queste informazioni non vengono visualizzate ai clienti o pubblicate nel marketplace.

### <a name="target-release"></a>Versione di destinazione

Indicare la versione di Microsoft Dynamics Business Central cui è destinata la soluzione: **Corrente**, **Versione principale successiva** o **Versione secondaria successiva**. Queste informazioni consentono di testare la soluzione in modo appropriato.

### <a name="supported-editions"></a>Edizioni supportate

Se l'offerta richiede l'edizione Premium di Microsoft Dynamics 365 Business Central, selezionare solo **Premium**. In caso contrario, selezionare sia **Essentials** sia **Premium**.

### <a name="key-usage-scenario"></a>Scenario di utilizzo principale

È necessario caricare un file PDF in cui sono elencati gli scenari di utilizzo principali dell'offerta elencati in un documento (formato PDF). Tutti gli scenari elencati qui possono essere verificati dal team di convalida prima di approvare l'offerta per il marketplace.

### <a name="app-tests-automation"></a>Automazione di test delle app

Se l'offerta è un'app che funge da componente aggiuntivo, è necessario caricare un file in **Automazione di test delle app** (con estensione app). Questo file non è applicabile alle app di connessione.

### <a name="test-accounts"></a>Account di test

Se è necessario un account di test per consentire al team di certificazione di controllare correttamente l'offerta, caricare un file PDF, DOC o DOCX con le informazioni specificate in **Account di test**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Inviare l'offerta per l'anteprima

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, selezionare **Pubblica** nell'angolo superiore destro del portale. Verrà visualizzata la pagina **Rivedi e pubblica**. 

Se è la prima volta che si pubblica questa offerta, è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta.
    - *Non avviata*: indica che la sezione è stata lasciata inalterata e deve essere completata.
    - *Incompleta*: indica che la sezione contiene errori che devono essere corretti o è necessario fornire altre informazioni. Tornare alla sezione o aggiornarla.
    - *Completa*: indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in stato completo prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** specificare le istruzioni per il test per il team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per conoscere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Si riceverà un messaggio di posta elettronica quando una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta sul pubblico.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
