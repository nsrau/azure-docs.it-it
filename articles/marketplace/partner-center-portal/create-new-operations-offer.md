---
title: Creare un'offerta Dynamics 365 for Operations nel marketplace commerciale
description: Come creare una nuova offerta Dynamics 365 for Operations per la presentazione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) usando il portale del marketplace commerciale nel Centro per i partner.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 0a2696da70a785fc1da6bfd2c7b0cabae1a89197
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85980694"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Creare un'offerta Dynamics 365 for Operations

Questo argomento descrive come creare una nuova offerta Dynamics 365 for Operations. [Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) di Microsoft è un servizio ERP (Enterprise Resource Planning) che supporta finanza avanzata, operazioni, produzione e gestione della catena di approvvigionamento. Tutte le offerte per Dynamics 365 for Operations devono essere sottoposte al processo di certificazione.

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

>[!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner verranno aggiornate solo nel sistema e archiviate dopo la nuova pubblicazione. Assicurarsi di inviare l'offerta per la pubblicazione dopo aver apportato le modifiche.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Dynamics 365 for Operations**.

    ![Menu di spostamento di sinistra.](./media/new-offer-dynamics-365-operations.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo aver apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere l'ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Come si vuole che i potenziali clienti interagiscano con questa offerta?

Selezionare l'opzione che si vuole usare per l'offerta.

#### <a name="get-it-now-free"></a>Scarica ora (gratuito)

Consente di presentare gratuitamente l'offerta ai clienti fornendo un URL valido (che inizia con *http* o *https*) dal quale accedere all'app.  Ad esempio, usare `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Free trial (listing) (Versione di valutazione gratuita (presentazione))

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (a partire da `http` o `https` ) dove è possibile ottenere una versione di valutazione. Ad esempio: `https://contoso.com/trial/my-app`. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni degli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Consente di raccogliere informazioni di contatto del cliente tramite la connessione al sistema di gestione delle relazioni con i clienti (CRM, Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Clienti potenziali](#customer-leads).

### <a name="test-drive"></a>Test drive

Il test drive è un'ottima soluzione per presentare un'offerta a potenziali clienti, dando loro la possibilità di provarla prima dell'acquisto e ottenendo un incremento delle conversioni e la generazione di clienti potenziali altamente qualificati. Per altre informazioni, vedere la pagina relativa all' [test drive](../what-is-test-drive.md).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita un test drive**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

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

Questa pagina visualizza le lingue in cui verrà presentata l'offerta. Attualmente, **Inglese (Stati Uniti)** è l'unica opzione disponibile.

Sarà necessario definire i dettagli del marketplace (nome dell'offerta, descrizione, immagini e così via) per ogni lingua/mercato. Selezionare il nome della lingua e del mercato in cui fornire queste informazioni.

> [!NOTE]
> Non è necessario usare l'inglese per i contenuti di presentazione dell'offerta (ad esempio descrizione, documenti, screenshot, condizioni per l'utilizzo e così via), purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile fornire un *URL a collegamenti utili* per offrire contenuti in una lingua diversa da quella usata nei contenuti di presentazione dell'offerta.

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Microsoft AppSource:

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo
2. Prodotti
3. Categorie
4. Settori
5. Indirizzo di supporto (collegamento)
6. Condizioni per l'utilizzo
7. Informativa sulla privacy
8. Nome offerta
9. Screenshot/video
10. Description

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

Se si vogliono informare i clienti che l'app funziona con prodotti specifici, immettere qui fino a tre nomi di prodotto.

### <a name="support-urls"></a>URL del supporto

Questa sezione consente di fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="help-link"></a>Collegamento alla Guida

Immettere l'URL in cui i clienti possono ottenere altre informazioni sull'offerta.

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. Si è tenuti a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida.

### <a name="contacts"></a>Contatti

In questa sezione specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto di supporto** e **Contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP.

Nella sezione **Contatto di supporto** specificare un valore per **URL supporto** in cui i partner CSP possono trovare supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Specificare qui almeno un documento di marketing correlato (e fino a un massimo di tre), ad esempio white paper, brochure, elenchi di controllo o presentazioni. Questi documenti devono essere in formato pdf.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione è possibile specificare logo e immagini che verranno usati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato PNG.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo dello store

Fornire il logo dell'offerta in due dimensioni di pixel:

- **Piccola** (48 x 48)
- **Grande** (216 x 216)


#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere screenshot che mostrano il funzionamento dell'offerta. È necessario almeno uno screenshot ed è possibile aggiungerne fino a cinque. Tutti gli screenshot devono avere dimensioni di 1280 x 720 pixel.

#### <a name="videos"></a>Video

Se si vuole, è possibile aggiungere fino a quattro video che descrivono l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ogni video, immettere il nome, l'URL e un'immagine di anteprima (1280 x 720 pixel)

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

[Procedure consigliate per la presentazione di offerte nel marketplace](../gtm-offer-listing-best-practices.md)

Prima di continuare, selezionare **Salva bozza**.

## <a name="availability"></a>Disponibilità

Questa pagina descrive le opzioni relative a dove e come rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Questa sezione consente di specificare i mercati in cui l'offerta deve essere disponibile. A questo scopo, selezionare **Modifica mercati**. Verrà visualizzata la finestra popup **Selezione mercato**.

Per impostazione predefinita, non viene selezionato alcun mercato. Selezionare almeno un mercato in cui pubblicare l'offerta. Fare clic su **Seleziona tutto** per rendere l'offerta disponibile in ogni mercato possibile oppure selezionare i mercati specifici da aggiungere. Al termine, fare clic su **Salva**.

Le opzioni qui si applicano solo ai nuovi acquisti. Se un cliente possiede già l'app in un determinato mercato e successivamente si rimuove il mercato, il cliente può continuare a usarla, ma nessun nuovo cliente in tale mercato potrà ottenere l'offerta.

> [!IMPORTANT]
> Si è tenuti a soddisfare i requisiti legali locali, anche se non sono elencati qui o nel Centro per i partner.

Tenere presente che anche se si selezionano tutti i mercati, le leggi locali e le restrizioni o altri fattori possono impedire la presentazione di alcune offerte in determinati paesi e regioni.

### <a name="preview-audience"></a>Destinatari dell'anteprima

Prima di pubblicare l'offerta live all'interno della più ampia offerta del marketplace, è necessario renderla disponibile a un **gruppo di destinatari dell'anteprima** limitato. Immettere qui un valore in **Nascondi chiave** (qualsiasi stringa che usa solo lettere minuscole e/o numeri). I membri del gruppo di destinatari in anteprima possono usare questa chiave come token per visualizzare un'anteprima dell'offerta nel marketplace.

Quindi, quando si è pronti a rendere disponibile l'offerta e a rimuovere la restrizione, sarà necessario rimuovere la chiave specificata in **Nascondi chiave** e ripubblicare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

Questa pagina definisce i dettagli tecnici usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquistarla.

### <a name="solution-identifier"></a>Identificatore soluzione

Specificare l'identificatore della soluzione (GUID).

Per individuare l'identificatore della soluzione:

1. In Dynamics Lifecycle Services di Microsoft selezionare **Gestione soluzioni**.
2. Selezionare la soluzione e quindi cercare l'**identificatore della soluzione** in **Panoramica pacchetto**. Se l'identificatore è vuoto, scegliere **Modifica**, ripubblicare il pacchetto e quindi riprovare.

### <a name="release-version"></a>Versione di rilascio

Selezionare la versione di Dynamics 365 for Finance and Operations con cui funziona questa soluzione.

Prima di continuare, selezionare **Salva bozza**.

## <a name="test-drive-technical-configuration"></a>Configurazione tecnica del test drive

In questa pagina è possibile configurare una dimostrazione ("test drive") che consente ai clienti di provare l'offerta prima di acquistarla. Per altre informazioni, vedere [che cos'è test drive](../what-is-test-drive.md).

Per abilitare un test drive, selezionare la casella di controllo **Abilita un test drive** nella scheda [Configurazione dell'offerta](#test-drive). Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Al termine della configurazione del test drive, selezionare **Salva bozza** prima di continuare.

## <a name="supplemental-content"></a>Contenuto supplementare

In questa pagina è possibile fornire informazioni aggiuntive sull'offerta per consentirne la convalida. Queste informazioni non vengono visualizzate ai clienti o pubblicate nel marketplace.

### <a name="validation-assets"></a>Risorse per la convalida

Caricare in questa sezione un [report di Analisi personalizzazione](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report). Questo report viene generato analizzando i modelli di personalizzazione e di estensione, in base a un set predefinito di regole per le procedure consigliate.

Questo file deve essere in formato xls o xlsx. Se si dispone di più report, è possibile caricare un file con estensione zip contenente tutti i report.

### <a name="does-solution-include-localizations"></a>La soluzione prevede localizzazioni?

Se la soluzione abilita l'uso di criteri e standard locali (ad esempio, se sono conformi alle diverse regole relative alle retribuzioni richieste da paesi/aree diverse), selezionare **Sì**. In caso contrario, selezionare **No**.

### <a name="does-solution-enable-translations"></a>La soluzione consente le traduzioni?

Se il testo della soluzione può essere tradotto in altre lingue, rispondere **Sì**. In caso contrario, selezionare **No**.

Prima di continuare, selezionare **Salva bozza**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Inviare l'offerta per l'anteprima

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, selezionare **Rivedi e pubblica** nell'angolo superiore destro del portale.

Se è la prima volta che si pubblica questa offerta, è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta.
    - **Non avviata**: la sezione è stata lasciata inalterata e deve essere completata.
    - **Incompleta**: la sezione contiene errori che devono essere corretti o è necessario fornire altre informazioni. Tornare alla sezione o aggiornarla.
    - **Completa**: la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in stato completo prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** specificare le istruzioni per il test per il team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per conoscere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Si riceverà un messaggio di posta elettronica per far sapere all'utente che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta sul pubblico.

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
