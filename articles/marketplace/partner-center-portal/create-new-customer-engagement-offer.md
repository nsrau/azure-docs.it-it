---
title: Creare un'offerta Dynamics 365 Customer Engagement e PowerApps nel marketplace commerciale Microsoft
description: Come creare una nuova offerta Dynamics 365 Customer Engagement e PowerApps per la presentazione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) nel Centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 159cdef6b2c831e8c20d7249334bebac4f2061c5
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606131"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Creare un'offerta Dynamics 365 for Customer Engagement e PowerApps

Questo argomento spiega come creare una nuova offerta Dynamics 365 Customer Engagement e PowerApps. Tutte le app per Dynamics 365 Customer Engagement (PowerApps, Sales, Service, Project Service e Field Service) devono essere sottoposte a un processo di certificazione e supportare un percorso di valutazione. Il processo di certificazione verifica che la soluzione soddisfi i requisiti standard, la compatibilità e l'efficienza nelle procedure consigliate. mentre il percorso di valutazione consente agli utenti di distribuire la soluzione in un ambiente Dynamics 365 reale.

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Dynamics 365 for Customer Engagement e PowerApps**.

    ![Immagine del menu di spostamento di sinistra.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner vengono visualizzate solo negli archivi online dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

Seguire questa procedura per configurare l'offerta.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Come si vuole che i potenziali clienti interagiscano con questa offerta?

Selezionare l'opzione che si vuole usare per l'offerta.

#### <a name="get-it-now-free"></a>Scarica ora (gratuito)

Consente di presentare gratuitamente l'offerta ai clienti fornendo un URL valido (che inizia con *http* o *https*) dal quale accedere all'app.  Ad esempio: `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Visualizzazione come "Versione di valutazione gratuita"

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (a partire da `http` o `https` ) dove è possibile ottenere una versione di valutazione.  Ad esempio: `https://contoso.com/trial/my-app`. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni degli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Consente di raccogliere informazioni di contatto del cliente tramite la connessione al sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividerne le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Lead clienti](#customer-leads).

Prima di continuare, selezionare **Salva bozza**.

### <a name="test-drive"></a>Test drive

Il test drive è un'ottima soluzione per presentare un'offerta a potenziali clienti, dando loro la possibilità di provarla prima dell'acquisto e ottenendo più conversioni e la generazione di lead altamente qualificati. Per altre informazioni, vedere la pagina relativa all' [test drive](../what-is-test-drive.md).

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

### <a name="applicable-dynamics-365-products"></a>Applicable Dynamics 365 products (Prodotti Dynamics 365 applicabili)

Selezionare tutti i prodotti Dynamics 365 per cui è valida l'offerta.

### <a name="app-version"></a>Versione dell'app

Immettere il numero di versione dell'offerta. I clienti visualizzeranno questa versione nella pagina dei dettagli dell'offerta. Se si aggiorna il numero di versione solo per via di modifiche di marketing/descrittive, selezionare la casella **Modifica solo marketing**. Questa opzione consente all'offerta di ignorare le fasi di certificazione e provisioning.

### <a name="terms-and-conditions"></a>Termini e condizioni

Specificare i termini e le condizioni legali qui. È anche possibile fornire l'indirizzo in cui sono disponibili i termini e le condizioni. Ai clienti verrà chiesto di accettare questi termini prima di poter provare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

Questa pagina visualizza le lingue in cui verrà presentata l'offerta. Attualmente, **Inglese (Stati Uniti)** è l'unica opzione disponibile.

Definire qui i dettagli del marketplace per ogni lingua/mercato, ad esempio il nome dell'offerta, la descrizione e le immagini. Selezionare il nome della lingua e del mercato in cui fornire queste informazioni.

> [!NOTE]
> Non è necessario usare l'inglese per i contenuti di presentazione dell'offerta (ad esempio descrizione, documenti, screenshot e condizioni per l'utilizzo), purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile specificare un *URL a collegamenti utili* per offrire contenuti in una lingua diversa da quella usata nei contenuti di presentazione dell'offerta.

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Microsoft AppSource (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo
2. Prodotti
3. Categorie
4. Indirizzo di supporto (collegamento)
5. Indirizzo Condizioni per l'utilizzo (collegamento)
6. Nome offerta
7. Descrizione
8. Screenshot/video

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

Immettere l'indirizzo in cui i clienti possono ottenere altre informazioni sull'offerta.

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'indirizzo dell'informativa sulla privacy dell'organizzazione. Si è tenuti a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida.

### <a name="contacts"></a>Contatti

Specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto supporto tecnico** e **Contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP.

Nella sezione **Contatto supporto tecnico** è necessario fornire anche un valore per **URL supporto** in cui i partner CSP possono trovare supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire qui almeno un documento di marketing correlato (e fino a un massimo di tre), ad esempio white paper, brochure, elenchi di controllo o presentazioni, in formato PDF.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire logo e immagini per l'offerta. Tutte le immagini devono essere in formato PNG.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo dello store

Fornire il logo dell'offerta in tre dimensioni di pixel:
- **Piccolo** (obbligatorio; 48 x 48)
- **Grande** (obbligatorio; 216 x 216)
- **Molto grande** (facoltativo; 255 x 115)

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere screenshot che mostrano il funzionamento dell'offerta. È necessario almeno uno screenshot ed è possibile aggiungerne fino a cinque. Tutti gli screenshot devono avere dimensioni di 1280 x 720 pixel.

#### <a name="videos"></a>Video

Se si vuole, è possibile aggiungere fino a quattro video che descrivono l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ogni video, immettere il nome, l'URL e un'immagine di anteprima (1280 x 720 pixel)

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

[Procedure consigliate per la presentazione di offerte nel marketplace](../gtm-offer-listing-best-practices.md)

Prima di continuare, selezionare **Salva bozza**.

## <a name="availability"></a>Disponibilità

Questa pagina consente di definire dove e come rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Specificare i mercati in cui l'offerta deve essere disponibile. A questo scopo, selezionare **Modifica mercati**. Verrà visualizzata la finestra popup **Selezione mercato**.

Per impostazione predefinita, non è selezionato alcun mercato, ma è necessario selezionarne almeno uno per pubblicare l'offerta. Scegliere **Seleziona tutto** per rendere l'offerta disponibile in ogni mercato possibile oppure selezionare i mercati specifici da aggiungere.

Le opzioni qui si applicano solo ai nuovi acquisti. Se un cliente possiede già l'app in un determinato mercato e successivamente si rimuove il mercato, il cliente può continuare a usarla, ma nessun nuovo cliente in tale mercato potrà ottenere l'offerta.

> [!IMPORTANT]
> Si è tenuti a soddisfare i requisiti legali locali, anche se non sono elencati qui o nel Centro per i partner. Tenere presente che se si selezionano tutti i mercati, le leggi locali, le restrizioni o altri fattori possono impedire la presentazione di alcune offerte in determinati paesi e regioni.

### <a name="preview-audience"></a>Gruppo di destinatari dell'anteprima

Prima di pubblicare l'offerta live all'interno della più ampia offerta del marketplace, è necessario renderla disponibile a un **gruppo di destinatari dell'anteprima** limitato. Immettere qui un valore in **Nascondi chiave** (qualsiasi stringa che usa solo lettere minuscole e/o numeri). I membri del gruppo di destinatari in anteprima possono usare questa chiave come token per visualizzare un'anteprima dell'offerta nel marketplace.

Quindi, quando si è pronti a rendere disponibile l'offerta e a rimuovere la restrizione, sarà necessario rimuovere la chiave specificata in **Nascondi chiave** e ripubblicare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

Questa pagina definisce i dettagli tecnici usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquistarla.

### <a name="base-license-model"></a>Base license model (Modello di licenza base)

Il modello di licenza base determina in che modo l'applicazione viene assegnata ai clienti nell'interfaccia di amministrazione di CRM. Selezionare **Risorsa** per le licenze basate su istanza o **Utente** se le licenze vengono assegnate una per ogni tenant.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Richiede l'accesso all'archiviazione sicura di CRM e l'accesso in uscita S2S

Selezionare questa casella per abilitare la configurazione dell'accesso all'archiviazione sicura di CRM e dell'accesso in uscita S2S (Server-to-Server). Questa funzionalità richiede una considerazione speciale da parte del team di Dynamics 365 durante la fase di certificazione. Microsoft contatterà l'utente per completare alcuni passaggi aggiuntivi per supportare questa funzionalità.

### <a name="application-configuration-url"></a>URL di configurazione dell'applicazione

Specificare l'URL per la pagina Web di configurazione che consente al cliente di configurare l'app.

### <a name="crm-package"></a>Pacchetto CRM

Nel campo **URL del percorso del pacchetto** immettere l'URL di un account di Archiviazione BLOB di Azure che contiene il file ZIP del pacchetto CRM caricato. Includere una chiave di firma di accesso condiviso di sola lettura per consentire a Microsoft di prelevare il pacchetto per la verifica.

> [!IMPORTANT]
> Per evitare un blocco di pubblicazione, assicurarsi che la data di scadenza nell'URL dell'archiviazione BLOB non sia scaduta. È possibile modificare la data accedendo al criterio. Si consiglia di impostare l'**ora di scadenza** su almeno un mese nel futuro.

Selezionare la casella con l'etichetta **Nel file del pacchetto è presente più di un pacchetto CRM**, se applicabile. In tal caso, assicurarsi di includere tutti i pacchetti nel file ZIP.

Per informazioni dettagliate su come creare il pacchetto e aggiornarne la struttura, vedere il [Passaggio 3: Creare un pacchetto AppSource per l'app](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource)

### <a name="crm-package-availability"></a>Disponibilità del pacchetto CRM

In questa sezione selezionare **+ Aggiungi area** per specificare le aree geografiche in cui il pacchetto CRM sarà disponibile per i clienti. La distribuzione nelle aree sovrane seguenti richiede un'autorizzazione speciale e la convalida durante il processo di certificazione: [Germania](../../germany/index.yml), [Cloud del US Gov](../../azure-government/documentation-government-welcome.md) e TIP.

Per impostazione predefinita, l'**URL di configurazione dell'applicazione** immesso in precedenza verrà usato per ogni area. Se si preferisce, è possibile immettere un URL di configurazione dell'applicazione distinto per una o più aree specifiche. 

Prima di continuare, selezionare **Salva bozza**.

## <a name="test-drive-technical-configuration"></a>Configurazione tecnica del test drive

In questa pagina è possibile configurare una dimostrazione ("test drive") che consente ai clienti di provare l'offerta prima di acquistarla. Per altre informazioni, vedere [che cos'è test drive](../what-is-test-drive.md).

Per abilitare un test drive, selezionare la casella di controllo **Abilita un test drive** nella scheda [Configurazione dell'offerta](#test-drive). Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Al termine della configurazione del test drive, selezionare **Salva bozza** prima di continuare.

## <a name="supplemental-content"></a>Contenuto supplementare

In questa pagina è possibile fornire informazioni aggiuntive sull'offerta per consentirne la convalida. Queste informazioni non vengono visualizzate ai clienti o pubblicate nel marketplace.

### <a name="key-usage-scenario"></a>Scenario di utilizzo principale

Caricare un file PDF in cui sono elencati gli scenari di utilizzo chiave dell'offerta. Tutti gli scenari elencati qui possono essere verificati dal team di convalida prima di approvare l'offerta per il marketplace.

Prima di continuare, selezionare **Salva bozza**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Inviare l'offerta per l'anteprima

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, selezionare **Rivedi e pubblica** nell'angolo superiore destro del portale.

Se è la prima volta che si pubblica questa offerta, è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta.
    - **Non avviata**: la sezione è stata lasciata inalterata e deve essere completata.
    - **Incompleta**: la sezione contiene errori che devono essere corretti o è necessario fornire altre informazioni. Tornare alla sezione e aggiornarla.
    - **Completa**: la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in stato completato prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** specificare le istruzioni per il test per il team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per conoscere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Si riceverà un messaggio di posta elettronica per far sapere all'utente che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Tornare al centro per i partner e selezionare **Go-Live** per pubblicare l'offerta sul pubblico.

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
