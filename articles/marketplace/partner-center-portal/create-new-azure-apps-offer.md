---
title: Creare un'offerta per un'applicazione Azure - Marketplace commerciale Microsoft
description: Informazioni sui passaggi e sulle considerazioni per la creazione di una nuova offerta per un'applicazione Azure nel portale del marketplace commerciale nel Centro per i partner. È possibile presentare o vendere l'offerta per un'applicazione Azure in Azure Marketplace o tramite il programma Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: AarathiN
ms.author: aarathin
ms.date: 07/14/2020
ms.openlocfilehash: 071d3fc972e99d61a226492350b7eee146584df6
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815667"
---
# <a name="create-an-azure-application-offer"></a>Creare un'offerta per un'applicazione di Azure

Questo articolo presenta le procedure e le considerazioni per la creazione di una nuova offerta per un'applicazione Azure nel marketplace commerciale. È necessario avere familiarità con questi concetti prima di creare una nuova offerta per un'applicazione Azure.

Prima di poter pubblicare una nuova offerta per un'applicazione Azure, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md) e assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

## <a name="before-you-begin"></a>Prima di iniziare

La progettazione, la creazione e i test delle offerte per applicazioni Azure richiedono esperienza tecnica nella piattaforma di Azure e nelle tecnologie usate per creare l'offerta. Il team tecnico deve avere il livello di conoscenza delle tecnologie Microsoft seguenti:

* Conoscenza di base dei [servizi di Azure](https://azure.microsoft.com/services/).
* Capacità di [progettare applicazioni Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/).
* Esperienza pratica in [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage#storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Esperienza pratica in [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Esperienza pratica in [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Documentazione e risorse tecniche

Esaminare le risorse seguenti durante la preparazione dell'offerta per un'applicazione Azure per il marketplace commerciale.

* [Comprendere modelli di Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)

* Argomenti di avvio rapido:

    * [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/)
    * [Guida alle procedure consigliate per i modelli di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [Pubblicare una definizione di applicazione](../../managed-applications/publish-service-catalog-app.md)
    * [Distribuire un'app del catalogo di servizi](../../managed-applications/deploy-service-catalog-quickstart.md)

* Esercitazioni:

    * [Creare file di definizioni](../../managed-applications/publish-service-catalog-app.md)
    * [Pubblicare un'applicazione del Marketplace](../../managed-applications/publish-marketplace-app.md)

* Esempi:

    * [Interfaccia della riga di comando di Azure](../../managed-applications/cli-samples.md)
    * [Azure PowerShell](../../managed-applications/powershell-samples.md)
    * [Soluzioni di applicazioni gestite](../../managed-applications/sample-projects.md)

Il video [Creazione di modelli di soluzione e applicazioni gestite per Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) offre un'introduzione completa al tipo di offerta applicazione Azure:

* Tipi di offerta disponibili
* Quali asset tecnici sono necessari
* Come creare un modello di Azure Resource Manager
* Sviluppo e test dell'interfaccia utente dell'app
* Come pubblicare l'offerta di app
* Processo di revisione dell'applicazione

### <a name="suggested-tools"></a>Strumenti suggeriti

Per la gestione dell'applicazione di Azure scegliere uno o entrambi gli ambienti di scripting seguenti:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

È consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo:

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/) con le estensioni seguenti:
    * Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

È possibile esaminare gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/). Inoltre, se si usa Visual Studio, vedere [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="types-of-azure-application-plans"></a>Tipi di piani per applicazioni Azure

Esistono due tipi di piani per applicazioni Azure: modelli di soluzione e applicazioni gestite.

* Un **modello di soluzione** è uno dei molti strumenti per pubblicare una soluzione nel marketplace. Usare questo tipo di offerta quando una soluzione richiede automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale. Con un modello di soluzione è possibile automatizzare la distribuzione di più di una risorsa, tra cui macchine virtuali, reti e risorse di archiviazione, per fornire soluzioni IaaS complesse.  Per altre informazioni sulla creazione di modelli di soluzione, vedere [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

* Le **applicazioni gestite** sono simili ai modelli di soluzione, con un'importante differenza. In un'applicazione gestita le risorse vengono distribuite in un gruppo di risorse gestito dall'entità di pubblicazione dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. In qualità di entità di pubblicazione dell'app, è possibile specificare il costo del supporto continuativo per la soluzione. Usare applicazioni gestite per creare e distribuire facilmente applicazioni chiavi in mano completamente gestite ai clienti.  Per altre informazioni sui vantaggi e sui tipi di applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](../../managed-applications/overview.md).

## <a name="technical-requirements"></a>Requisiti tecnici

Tutte le applicazioni Azure includono almeno due file nella cartella radice di un archivio `.zip`:

* Un file di modello di Resource Manager denominato [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md).  Questo modello definisce le risorse da distribuire nella sottoscrizione di Azure del cliente. Per esempi di modelli di Resource Manager, vedere la [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) o il repository [GitHub: modelli di avvio rapido di Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) corrispondente.

* Definizione dell'interfaccia utente per l'esperienza di creazione di applicazioni Azure denominata [createUiDefinition.json](../../managed-applications/create-uidefinition-overview.md).  Nell'interfaccia utente specificare gli elementi che consentono ai consumer di fornire i valori dei parametri.

Tutte le nuove offerte per applicazioni Azure devono includere un [GUID di attribuzione dell'uso da parte dei clienti e dei partner di Azure](../azure-partner-customer-usage-attribution.md). 

Per informazioni sui requisiti di pubblicazione per ogni piano dell'applicazione, vedere [Requisiti di pubblicazione di un'offerta di tipo modello di soluzione](../marketplace-solution-templates.md) e [Requisiti di pubblicazione di un'offerta di tipo applicazione gestita](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Creare una nuova offerta

>[!NOTE]
>Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner non verranno visualizzate negli archivi online fino a quando non si pubblica nuovamente l'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).

1. Dal menu a sinistra scegliere **Marketplace commerciale** > **Panoramica**.

1. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Applicazione Azure**.

    ![Immagine del menu di spostamento a sinistra.](./media/new-offer-azure-app.png)

1. Nella pagina **Nuova offerta** immettere un valore in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

     * Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta nel marketplace e nei modelli di Azure Resource Manager, se applicabile.
     * Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
     * Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

1. Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

     * Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
     * L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

1. Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

Nella pagina **Configurazione dell'offerta** è possibile configurare un test drive e la gestione dei lead per l'offerta.

### <a name="test-drive"></a>Test drive

Il test drive è un'ottima soluzione per presentare un'offerta a potenziali clienti, dando loro la possibilità di provarla prima dell'acquisto e ottenendo più conversioni e la generazione di lead altamente qualificati. [Altre informazioni sui test drive](../what-is-test-drive.md).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita un test drive**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo. Configurare l'ambiente di test drive nella sezione [Configurazione tecnica del test drive](#test-drive-technical-configuration) più avanti in questo argomento.

Per altre informazioni, vedere [Test drive dell'offerta nel marketplace commerciale](test-drive.md). È anche possibile leggere informazioni sulle [procedure consigliate per i test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) e scaricare il [PDF della panoramica dei test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (assicurarsi che il blocco popup sia disattivato).

>[!Note]
>Poiché tutte le applicazioni Azure vengono implementate usando un modello di Azure Resource Manager, l'unico tipo di test drive disponibile per un'applicazione Azure è un [test drive basato su Azure Resource Manager](../azure-resource-manager-test-drive.md).

### <a name="customer-leads"></a>Lead clienti

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per altre informazioni, vedere [Panoramica della gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

La pagina delle **Proprietà** consente di definire le categorie usate per raggruppare l'offerta nel Marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="category"></a>Category

Selezionare le categorie e le sottocategorie per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta. Selezionare:

- Almeno una delle due categorie, incluse una categoria primaria e una secondaria (facoltativo).
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**.

Vedere l'elenco completo di categorie e sottocategorie nell'elenco delle [procedure](../gtm-offer-listing-best-practices.md)consigliate.

### <a name="legal"></a>Note legali

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Presentazione offerta

In questa pagina è possibile gestire la copia e le immagini per l'offerta del marketplace commerciale.

### <a name="marketplace-details"></a>Dettagli del marketplace

> [!NOTE]
> Il contenuto per la presentazione dell'offerta (ad esempio la descrizione, i documenti, gli screenshot e le condizioni per l'utilizzo) non deve necessariamente essere in inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile specificare un *URL a collegamenti utili* per offrire contenuti in una lingua diversa da quella usata nei contenuti di presentazione dell'offerta.

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Azure Marketplace (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo
2. Categorie
3. Indirizzo di supporto (collegamento)
4. Condizioni per l'utilizzo
5. Indirizzo privacy policy (collegamento)
6. Nome offerta
7. Riepilogo
8. Descrizione
9. Screenshot/video

<br>Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta nell'portale di Azure:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Viene illustrato il modo in cui questa offerta viene visualizzata nel portale di Azure.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Titolo
2. Descrizione
3. Collegamenti utili
4. Schermate

#### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato ai clienti come titolo della presentazione dell'offerta. Questo campo viene precompilato con il testo immesso per **Alias offerta** quando è stata creata l'offerta, ma è possibile modificarne il valore. Questo nome può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright). Il nome non può essere costituito da più di 50 caratteri e non può includere emoji.

#### <a name="search-results-summary"></a>Riepilogo risultati di ricerca

Fornire una breve descrizione dell'offerta, fino a 100 caratteri. Questa descrizione può essere usata nei risultati della ricerca.

#### <a name="long-summary"></a>Riepilogo lungo

Fornire una descrizione più lunga dell'offerta, fino a 256 caratteri. Questa descrizione può essere usata nei risultati della ricerca.

#### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Se si vuole, è possibile immettere fino a tre parole chiave di ricerca per aiutare i clienti a trovare l'offerta nel marketplace. Per ottenere risultati ottimali, usare queste parole chiave anche nella descrizione.

#### <a name="privacy-policy-link"></a>Collegamento dell'informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. Si è tenuti a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida.

### <a name="useful-links"></a>Collegamenti utili

Aggiungere collegamenti a documenti online supplementari facoltativi sulla soluzione selezionando **+ Aggiungi collegamento**.

### <a name="contact-information"></a>Informazioni contatto

Fornire il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto di supporto**, **Contatto tecnico** e **Contatto programma CSP**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP. Alcuni contatti possono richiedere informazioni aggiuntive.

### <a name="marketplace-media"></a>File multimediali del marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate verranno rifiutate.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo per lo Store

Fornire un file PNG per il logo di **grandi** dimensioni. Il centro per i partner lo utilizzerà per creare un logo **piccolo** e **medio** . Facoltativamente, è possibile sostituirli con immagini diverse in un secondo momento.

- **Grande** (da 216 x 216 a 350 x 350 px, obbligatorio)
- **Media** (90 x 90 px, facoltativo)
- **Piccolo** (48 x 48 px, facoltativo)

Questi logo vengono usati in posizioni diverse nell'elenco:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere fino a cinque screenshot che mostrano il funzionamento dell'offerta. Ogni screenshot deve avere dimensioni pari a 1280 x 720 pixel ed essere in formato PNG. Ogni screenshot deve includere una didascalia.

#### <a name="videos"></a>Video

Aggiungere fino a cinque video che descrivono l'offerta. Questi devono essere ospitati in un servizio video esterno. Immettere il nome di ogni video, l'indirizzo Web e un'immagine PNG di anteprima del video a 1280 x 720 pixel.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

- [Procedure consigliate per la presentazione di offerte nel marketplace](../gtm-offer-listing-best-practices.md)

Prima di continuare, selezionare **Salva bozza**.

## <a name="preview-audience"></a>Gruppo di destinatari per l'anteprima

Nella scheda Anteprima scegliere un **gruppo di destinatari per l'anteprima** limitato per convalidare l'offerta prima di pubblicarla al più ampio pubblico del marketplace.

> [!IMPORTANT]
> Dopo aver controllato l'offerta in Anteprima, selezionare **Passa allo stato Live** per pubblicare l'offerta ai destinatari pubblici del marketplace commerciale.

Il gruppo di destinatari per l'anteprima è identificato da GUID di ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno. Nessuno di questi campi può essere visualizzato dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina **Sottoscrizioni** nel portale di Azure.

Aggiungere almeno un ID sottoscrizione di Azure, singolarmente (fino a 10) o caricando un file CSV (fino a 100). L'aggiunta di questi ID sottoscrizione consente di definire gli utenti che possono visualizzare in anteprima l'offerta prima che venga pubblicata. Se l'offerta è già stata pubblicata, è comunque possibile definire un gruppo di destinatari per l'anteprima che possa testare le modifiche o gli aggiornamenti all'offerta.

> [!NOTE]
> Un gruppo di destinatari per l'anteprima è diverso da un pubblico privato. Un gruppo di destinatari per l'anteprima può accedere all'offerta *prima* che l'offerta venga pubblicata nei marketplace. Può infatti visualizzare e convalidare tutti i piani, inclusi quelli che saranno disponibili solo per un gruppo di destinatari privato dopo che l'offerta viene pubblicata completamente nel marketplace. Un gruppo di destinatari privato (definito nella scheda **Prezzi e disponibilità** del piano) ha accesso esclusivo a un piano specifico.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

Completare questa sezione solo se l'offerta include un'applicazione gestita che genererà eventi di analisi usando l'API del servizio di analisi del marketplace. Completare i campi **ID tenant di Azure Active Directory** e **ID applicazione Azure Active Directory** usati dal servizio per la generazione di eventi di analisi.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration-offer-level"></a>Configurazione tecnica (a livello di offerta)

>[!Note]
>I dettagli tecnici a livello di offerta sono facoltativi.  È necessario configurare questi dettagli solo se si pubblica un'applicazione gestita con fatturazione a consumo ed è presente un servizio che verrà autenticato con un token di sicurezza di Azure AD. Per altre informazioni, vedere [Strategie di autenticazione](./marketplace-metering-service-authentication.md) per le diverse opzioni di autenticazione.

La configurazione tecnica definisce i dettagli (ID tenant e ID app) usati per identificare il servizio, in modo da generare eventi di analisi per un'applicazione gestita usando le [API del servizio di analisi del marketplace](./marketplace-metering-service-apis.md).  Immettere l'identità che verrà usata dal servizio per la creazione di eventi di analisi.

* **Azure ad ID tenant** (obbligatorio): all'interno del portale di Azure, è necessario [creare un'app Azure Active Directory (ad)](../../active-directory/develop/howto-create-service-principal-portal.md) in modo che sia possibile convalidare la connessione tra i due servizi sia dietro una comunicazione autenticata. Per trovare l' [ID tenant](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), passare alla Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).
* **Azure ad ID app** (obbligatorio): è necessario anche l' [ID applicazione](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)e una chiave di autenticazione. Per ottenere questi valori, passare ad Azure Active Directory e selezionare **Registrazioni app**, quindi cercare il numero **ID applicazione** elencato, ad esempio 50c464d3-4930-494c-963C-1e951d15360e. Per trovare la chiave di autenticazione, passare a **Impostazioni** e selezionare **Chiavi**. Sarà necessario specificare una descrizione e una durata, quindi verrà fornito un valore numerico.

>[!Note]
>L'ID applicazione Azure verrà associato all'ID editore e potrà essere riutilizzato solo all'interno dell'account di questo editore.

>[!Note]
>Questa configurazione è obbligatoria se si vuole usare l' [evento di utilizzo batch](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)).  Se si vuole inviare un [evento di utilizzo](marketplace-metering-service-apis.md#metered-billing-single-usage-event), è anche possibile usare il [servizio metadati dell'istanza](../../active-directory/managed-identities-azure-resources/overview.md) per ottenere il [token web JSON (JWT) Bearer token](pc-saas-registration.md#how-to-get-the-publishers-authorization-token)).

## <a name="plan-overview"></a>Panoramica del piano

Questa scheda consente di fornire diverse opzioni per il piano all'interno della stessa offerta. Questi piani, chiamati in precedenza SKU, possono differire in termini di tipo di piano (modello di soluzione e applicazione gestita), monetizzazione o destinatari. Configurare almeno un piano per presentare l'offerta nel marketplace.

È possibile creare fino a 100 piani per ogni offerta: fino a 45 di questi possono essere privati. Scopri di più sui piani privati in [offerte private in Microsoft Commercial Marketplace](../private-offers.md).

Una volta creati i piani, in questa scheda vengono visualizzati i nomi, gli ID, il tipo di piano, la disponibilità (pubblica o privata), lo stato di pubblicazione corrente e tutte le azioni disponibili.

Le **azioni** disponibili in **Panoramica del piano** variano a seconda dello stato corrente del piano e possono includere:

* Se lo stato del piano è **Bozza**, Elimina bozza.
* Se lo stato del piano è **Attiva**, Interrompi vendita o Sincronizza destinatari privati.

### <a name="create-new-plan"></a>Crea nuovo piano

***ID piano***: creare un ID piano univoco per ogni piano nell'offerta. Questo ID sarà visibile ai clienti nell'URL del prodotto.  Usare solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. Per questo ID piano sono consentiti al massimo 50 caratteri. Questo ID non può essere modificato dopo aver selezionato Crea.

***Nome del piano*** : i clienti visualizzeranno questo nome per decidere quale piano selezionare nell'offerta. Creare un nome di offerta univoco per ogni piano nell'offerta. Il nome del piano viene usato per distinguere i piani software che possono fare parte della stessa offerta (ad esempio, nome dell'offerta: Windows Server, piani: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configurazione del piano

Questa scheda consente di impostare la configurazione generale per il tipo di piano, specificando se riutilizzare i pacchetti di un altro piano e in quali cloud deve essere disponibile il piano. Le risposte in questa scheda influiscono sui campi visualizzati in altre schede per lo stesso piano.

#### <a name="plan-type"></a>Tipo di piano
Selezionare il tipo di piano per l'offerta. Un piano **Modello di soluzione** viene gestito interamente dal cliente. Un piano **Applicazione gestita** consente agli editori di gestire l'applicazione per conto del cliente. Per informazioni dettagliate, vedere [Tipi di piani per le applicazioni Azure](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Riutilizzare la configurazione tecnica

Se è stato creato più di un piano dello stesso tipo e i pacchetti sono identici tra piani, è possibile selezionare **This plan reuses packages from another plan** (Questo piano riutilizza pacchetti di un altro piano).  Quando si seleziona questa opzione, è possibile selezionare uno degli altri piani dello stesso tipo per questa offerta per riutilizzare i pacchetti.

>[!Note]
>Quando si riutilizzano pacchetti di un altro piano, l'intera scheda Configurazione tecnica scomparirà da questo piano. I dettagli relativi alla configurazione tecnica dell'altro piano, inclusi gli eventuali aggiornamenti apportati in futuro, verranno usati anche per questo piano.<br><br>Questa impostazione non può essere modificata dopo la pubblicazione del piano.

#### <a name="azure-regions-cloud"></a>Aree di Azure (cloud)

Il piano deve essere reso disponibile in almeno un'area di Azure.

Selezionare l'opzione **Azure globale** per rendere il piano disponibile ai clienti in tutte le aree globali di Azure in cui è prevista l'integrazione del marketplace commerciale. Per informazioni dettagliate, vedere [Disponibilità geografica e valute supportate](../marketplace-geo-availability-currencies.md).

Selezionare l'opzione **Azure per enti pubblici** per rendere disponibile il piano nell'area [Azure per enti pubblici](../../azure-government/documentation-government-welcome.md). Questa area garantisce l'accesso controllato per i clienti da entità federali, statali, locali o tribali degli Stati Uniti, oltre ai partner idonei alla gestione. L'editore è responsabile di tutti i controlli di conformità, le misure di sicurezza e le procedure consigliate. Azure per enti pubblici usa data center e reti fisicamente isolati (situati solo negli Stati Uniti).

Prima di eseguire la pubblicazione in [Azure per enti pubblici](../../azure-government/documentation-government-manage-marketplace-partners.md), testare e convalidare il piano nell'ambiente, in quanto determinati endpoint possono essere diversi. Per configurare e testare il piano, richiedere un account di valutazione per la [versione di valutazione di Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Dopo che il piano viene pubblicato ed è disponibile in un'area di Azure specifica, questa non può più essere rimossa.

#### <a name="azure-government-certifications"></a>Certificazioni del Cloud Azure per enti pubblici

Questa opzione è visibile solo se è stato selezionato **Azure per enti pubblici**.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e normative governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per aumentare la consapevolezza sulle certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti descrittivi. Questi possono essere collegamenti alla presentazione direttamente nel programma o a descrizioni della conformità a tali standard nei propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

Prima di continuare, selezionare **Salva bozza**.

### <a name="plan-listing"></a>Elenco piani

Qui è possibile configurare i dettagli della presentazione del piano. In questa scheda vengono visualizzate informazioni specifiche che possono variare tra i piani nella stessa offerta.

#### <a name="plan-name"></a>Nome piano

Questo campo è precompilato con il nome assegnato durante la creazione del piano. Il nome viene visualizzato nel marketplace come titolo del piano ed è limitato a 100 caratteri.

#### <a name="plan-summary"></a>Riepilogo piano

Fornire un breve riepilogo del piano (non dell'offerta). Questo riepilogo è limitato a 100 caratteri.

#### <a name="plan-description"></a>Descrizione piano

Descrivere che cosa rende unico questo piano software, insieme alle differenze tra i piani all'interno dell'offerta. Non descrivere l'offerta, ma solo il piano. La descrizione del piano può contenere fino a 2.000 caratteri.

Prima di continuare, selezionare **Salva bozza**.

### <a name="availability-solution-template-plans-only"></a>Disponibilità (solo piani di tipo modello di soluzione)

Il piano può essere reso visibile a tutti, solo a clienti specifici (destinatari privati) ed è possibile scegliere se rendere il piano nascosto per l'uso da parte di un altro modello di soluzione o di applicazioni gestite.

#### <a name="plan-visibility"></a>Plan Visibility (Visibilità del piano)

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando gli ID sottoscrizione di Azure.

Selezionare **Questo è un piano privato** per rendere il piano privato e visibile solo a un gruppo limitato di destinatari di propria scelta. Una volta pubblicato il piano come privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Dopo che un piano è stato pubblicato come visibile a tutti, deve restare visibile a tutti gli utenti e non può essere riconfigurato come piano privato.

Se si imposta il piano come privato, immettere un **ID sottoscrizione di Azure** e la relativa descrizione. Ogni ID corrisponde a un gruppo di destinatari che avrà accesso a questo piano privato. L'accesso viene assegnato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione di Azure assegnato. Aggiungere un massimo di 10 ID sottoscrizione dei clienti singolarmente oppure 20.000 ID importando un file CSV. Gli ID sottoscrizione di Azure vengono rappresentati come GUID e le lettere devono essere minuscole.

>[!Note]
>Un gruppo di destinatari privato o limitato è diverso dai destinatari per l'anteprima definiti nella scheda **Anteprima**. Un gruppo di destinatari per l'anteprima può accedere all'offerta _prima_ che l'offerta venga pubblicata nel marketplace. Mentre la scelta di un gruppo di destinatari privato viene applicata solo a un piano specifico, i destinatari per l'anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

#### <a name="hide-plan"></a>Nascondi piano

Se il modello di soluzione deve essere distribuito solo indirettamente quando si fa riferimento a un altro modello di soluzione o applicazione gestita, selezionare questa casella per pubblicare il modello di soluzione, ma nasconderlo ai clienti che eseguono ricerche e vogliono selezionarlo direttamente.

Prima di continuare, selezionare **Salva bozza**.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Prezzi e disponibilità (solo piani di tipo applicazione gestita)

Usare questa opzione per configurare i campi **Mercati** per i mercati in cui sarà disponibile il piano, **Prezzi** per i prezzi al mese della gestione della soluzione e **Plan Visibility** (Visibilità piano) solo se il piano deve essere visualizzato da clienti specifici (gruppo di destinatari privato).

Prima di continuare, selezionare **Salva bozza**.

#### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per la località di un mercato qualsiasi in cui si vuole rendere disponibile questo piano. Sono inclusi una casella di ricerca e un pulsante per la selezione di paesi o aree geografiche in cui Microsoft versa l'IVA e l'imposta sui beni durevoli per conto degli editori.

Se i prezzi per il piano sono già stati impostati in dollari statunitensi (USD) e si aggiunge la località di un altro mercato, il prezzo del nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Controllare sempre il prezzo di ogni mercato prima della pubblicazione. I prezzi possono essere esaminati tramite il collegamento "Export prices (xlsx)" (Esporta prezzi - XLSX) dopo aver salvato le modifiche.

#### <a name="pricing"></a>Prezzi

Specificare il prezzo al mese per questo piano.  Questo prezzo è in aggiunta a tutti gli altri costi dell'infrastruttura di Azure o i costi software con pagamento in base al consumo sostenuti dalle risorse distribuite da questa soluzione.

Oltre al prezzo mensile, è anche possibile impostare i prezzi per l'utilizzo di unità non standard tramite la [fatturazione a consumo](./azure-app-metered-billing.md).  È anche possibile impostare il prezzo al mese su zero e addebitare esclusivamente la fatturazione a consumo.

I prezzi impostati in USD (USD = dollaro statunitense) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti quando vengono salvati. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e controllando il prezzo in ogni mercato. Per impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi.

>[!Note]
>Salvare le modifiche apportate ai prezzi per consentire l'esportazione dei dati relativi ai prezzi.

Esaminare attentamente i prezzi prima della pubblicazione, in quanto esistono alcune limitazioni relative alle modifiche consentite dopo la pubblicazione di un piano.  

>[!Note]
>Una volta pubblicato il prezzo per un mercato nel piano, non è possibile modificarlo in un secondo momento.

#### <a name="plan-visibility"></a>Plan Visibility (Visibilità del piano)

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando gli ID sottoscrizione di Azure.

Selezionare **Questo è un piano privato** per rendere il piano privato e visibile solo a un gruppo limitato di destinatari di propria scelta. Una volta pubblicato il piano come privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Dopo che un piano è stato pubblicato come visibile a tutti, deve restare visibile a tutti gli utenti e non può essere riconfigurato come piano privato.

>[!Note]
>Un gruppo di destinatari privato o limitato è diverso dai destinatari per l'anteprima definiti nella scheda **Anteprima**. Un gruppo di destinatari per l'anteprima può accedere all'offerta _prima_ che l'offerta venga pubblicata nel marketplace. Mentre la scelta di un gruppo di destinatari privato viene applicata solo a un piano specifico, i destinatari per l'anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

Se si imposta il piano come privato, immettere un **ID sottoscrizione di Azure** e la relativa descrizione. Ogni ID corrisponde a un gruppo di destinatari che avrà accesso a questo piano privato. L'accesso viene assegnato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione di Azure assegnato. Aggiungere un massimo di 10 ID sottoscrizione dei clienti singolarmente oppure 20.000 ID importando un file CSV. Gli ID sottoscrizione di Azure vengono rappresentati come GUID e le lettere devono essere minuscole.

>[!Note]
>Le offerte private non sono supportate con le sottoscrizioni di Azure stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

### <a name="technical-configuration"></a>Configurazione tecnica

Questa scheda consente di caricare il pacchetto di distribuzione che consentirà ai clienti di distribuire il piano.

>[!Note]
>Questa scheda non sarà visibile se il piano è stato configurato in modo da riutilizzare i pacchetti di un altro piano nella scheda **Configurazione del piano**.

#### <a name="package-details"></a>Dettagli del pacchetto

Questa scheda consente di modificare la versione bozza della configurazione tecnica.

**Versione**: assegnare la versione corrente della configurazione tecnica.  Incrementare questa versione ogni volta che si pubblica una modifica in questa pagina. La versione deve usare il formato `{integer}.{integer}.{integer}`.

**File del pacchetto (.zip)** : questo pacchetto contiene tutti i file di modello necessari per il piano, nonché tutte le risorse aggiuntive, inclusi come pacchetto in un file `.zip`.

Tutti i pacchetti del piano dell'applicazione Azure devono includere questi due file nella cartella radice di un archivio `.zip`:

* Un file di modello di Resource Manager denominato [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md). Questo modello automatizza la distribuzione delle risorse nella sottoscrizione di Azure dei clienti.  Per esempi di modelli di Resource Manager, vedere la [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) o il repository [GitHub: modelli di avvio rapido di Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) corrispondente.
* Definizione dell'interfaccia utente per l'esperienza di creazione di applicazioni Azure denominata [createUiDefinition.json](../../azure-resource-manager/managed-application-createuidefinition-overview.md).

Le dimensioni massime dei file supportate sono:

* Fino a 1 GB di dimensioni totali compresse in un archivio `.zip`
* Fino a 1 GB per ogni singolo file non compresso incluso nell'archivio `.zip`  

Tutte le nuove offerte per applicazioni Azure devono includere anche un GUID di [attribuzione di utilizzo da parte dei clienti e dei partner di Azure](../azure-partner-customer-usage-attribution.md).

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

I piani di tipo applicazione gestita richiedono informazioni aggiuntive in questa scheda.

#### <a name="previously-published-packages"></a>Pacchetti pubblicati in precedenza

La sottoscheda **Pacchetti pubblicati in precedenza** consente di visualizzare tutte le versioni pubblicate della configurazione tecnica.

#### <a name="enable-just-in-time-jit-access"></a>Abilita accesso JIT

Selezionare questa opzione per abilitare l'accesso JIT per il piano.  L'accesso JIT consente di richiedere l'accesso con privilegi elevati alle risorse di un'applicazione gestita per la risoluzione dei problemi o la manutenzione. L'accesso alle risorse è sempre in sola lettura, ma per un periodo di tempo specifico è possibile richiedere un accesso di livello maggiore.  Per altre informazioni, vedere [Abilitare e richiedere l'accesso JIT per applicazioni gestite di Azure](../../managed-applications/request-just-in-time-access.md).  Perché i consumer dell'applicazione gestita concedano all'account l'accesso permanente, lasciare deselezionata questa opzione.

>[!Note]
>Assicurarsi di aggiornare il file `createUiDefinition.json` per supportare questa funzionalità.  

#### <a name="deployment-mode"></a>Modalità di distribuzione

Specificare se configurare la modalità di distribuzione **Completa** o **Incrementale** durante la distribuzione del piano: 

* In modalità **Completa** una ridistribuzione dell'applicazione da parte del cliente comporterà la rimozione delle risorse nel gruppo di risorse gestite, se le risorse non sono definite in `mainTemplate.json`. 
* In modalità **Incrementale** una ridistribuzione dell'applicazione lascia invariate le risorse esistenti.

Per altre informazioni sulle modalità di distribuzione, vedere [Modalità di distribuzione di Azure Resource Manager](../../azure-resource-manager/deployment-modes.md).

#### <a name="notification-endpoint-url"></a>URL endpoint di notifica

Specificare un endpoint webhook HTTPS facoltativo per ricevere notifiche su tutte le operazioni CRUD su istanze di applicazioni gestite di questa versione del piano.

#### <a name="customize-allowed-customer-actions"></a>Personalizza azioni cliente consentite

Selezionare questa opzione per specificare le azioni che i clienti possono eseguire sulle risorse gestite, oltre alle azioni "`*/read`" disponibili per impostazione predefinita.

Elencare le azioni aggiuntive di cui consentire l'esecuzione al cliente, separate da punti e virgola.  Per altre informazioni, vedere [Informazioni sulle assegnazioni di rifiuto per le risorse di Azure](../../role-based-access-control/deny-assignments.md). Per informazioni sulle azioni disponibili, vedere [Operazioni di provider di risorse con Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). Ad esempio, per consentire agli utenti di riavviare le macchine virtuali, aggiungere `Microsoft.Compute/virtualMachines/restart/action` alle azioni consentite.

#### <a name="global-azure--azure-government-cloud"></a>Azure globale/Cloud Azure per enti pubblici

Indicare chi deve avere accesso di gestione a questa applicazione gestita in ogni cloud supportato. Gli utenti, i gruppi o le applicazioni a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite vengono identificati usando le identità Azure Active Directory (AD).

**Azure Active Directory ID tenant** : l'id tenant Azure ad (noto anche come ID directory) contenente le identità degli utenti, dei gruppi o delle applicazioni a cui si vogliono concedere le autorizzazioni. È possibile trovare l'ID tenant Azure AD nel portale di Azure, in [proprietà per Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Autorizzazioni**: aggiungere l'ID oggetto di Azure Active Directory dell'utente, del gruppo o dell'applicazione cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite. Identificare l'utente tramite l'ID entità di sicurezza, disponibile nel [pannello degli utenti di Azure Active Directory del portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Per ogni entità di sicurezza, selezionare uno dei ruoli predefiniti di Azure AD nell'elenco (Proprietario o Collaboratore). Il ruolo selezionato descrive le autorizzazioni dell'entità di sicurezza per le risorse nella sottoscrizione del cliente. Per altre informazioni, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md). Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../../role-based-access-control/overview.md).

>[!Note]
>Benché sia possibile aggiungere fino a 100 autorizzazioni per cloud, è in genere più semplice creare un gruppo di utenti di Active Directory e specificarne l'ID in "ID entità di sicurezza". In questo modo, sarà possibile aggiungere altri utenti al gruppo di gestione dopo la distribuzione del piano e ridurre la necessità di aggiornare il piano solo per aggiungere altre autorizzazioni.

#### <a name="policy-settings"></a>Impostazioni dei criteri

Applicare [criteri di Azure](../../governance/policy/overview.md) all'applicazione gestita per specificare i requisiti di conformità per la soluzione distribuita. Per le definizioni dei criteri e il formato dei valori dei parametri, vedere [Esempi di criteri di Azure](../../governance/policy/samples/index.md). È possibile configurare un massimo di cinque criteri e una sola istanza di ogni opzione dei criteri. Alcuni criteri richiedono parametri aggiuntivi. Lo SKU Standard è necessario per i criteri di controllo. La lunghezza massima del nome dei criteri è 50 caratteri.

Prima di continuare, selezionare **Salva bozza**.

## <a name="co-sell-with-microsoft"></a>Co-selling con Microsoft

L'immissione di informazioni nella scheda Co-selling è del tutto facoltativa per la pubblicazione dell'offerta. È tuttavia obbligatoria per ottenere lo stato di idoneità per il co-selling o di soluzione pronta per il co-selling della proprietà intellettuale. Le informazioni fornite verranno usate dai team di vendita Microsoft per approfondire la soluzione durante la valutazione dell'idoneità alle esigenze dei clienti. Non saranno direttamente disponibile ai clienti.

Per informazioni dettagliate su questa scheda, vedere [Opzione di co-selling nel Centro per i partner](commercial-marketplace-co-sell.md).

## <a name="resell-through-csps"></a>Rivendita tramite CSP

È possibile espandere la copertura dell'offerta rendendola disponibile ai partner nel programma [Cloud Solution Provider](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). In questo modo, i rivenditori possono vendere l'offerta ai propri clienti e creare soluzioni in bundle.

Prima di continuare, selezionare **Salva bozza**.

## <a name="test-drive"></a>Test drive

Configurare una dimostrazione (test drive) che consente ai clienti di provare l'offerta prima di acquistarla. Per creare un ambiente demo in cui i clienti possano provare l'offerta per un periodo di tempo fisso, vedere [Test drive dell'offerta nel marketplace commerciale](test-drive.md).

Per abilitare una test drive, selezionare la casella di controllo **Abilita un test drive** nella scheda [Configurazione dell'offerta](#test-drive). Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

### <a name="test-drive-technical-configuration"></a>Configurazione tecnica del test drive

- **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Dettagli abbonamento distribuzione

Per consentire la distribuzione del test drive per proprio conto, creare e fornire una sottoscrizione di Azure separata e univoca (non necessaria per test drive di Power BI).

* **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e App per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile provare a [creare una sottoscrizione di Azure separata](../../billing/billing-create-subscription.md) da usare per i test drive, se non esiste già. Per trovare gli ID sottoscrizione di Azure, accedere al [portale di Azure](https://portal.azure.com/) e passare alla scheda **Sottoscrizioni** del menu a sinistra. Se si seleziona la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5AB6-6789-1h234g764ghty".
* **Azure ad ID tenant** (obbligatorio): immettere l' [id tenant](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato, ad esempio 50c464d3-4930-494c-963C-1e951d15360e. È anche possibile cercare l'ID tenant dell'organizzazione usando l'URL del nome di dominio all'indirizzo: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).
* **Nome tenant di Azure AD** (obbligatorio per Dynamics 365): immettere il nome di Azure Active Directory (AD). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/): nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.
* **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu di spostamento a sinistra, selezionare **Registrazioni app**, quindi cercare il numero **ID applicazione** elencato, ad esempio 50c464d3-4930-494c-963c-1e951d15360e.
* **Azure Active Directory segreto client applicazione** (obbligatorio): immettere il [segreto client](../../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu di spostamento a sinistra, selezionare **Registrazioni app**, quindi selezionare l'app test drive. Selezionare quindi **Certificati e segreti**, selezionare **Nuovo segreto client**, immettere una descrizione, selezionare **Mai** in **Scade**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore prima di uscire dalla pagina.

Prima di continuare, selezionare **Salva bozza**.

### <a name="marketplace-listing-optional"></a>Presentazione nel Marketplace (facoltativo)

Descrivere l'esperienza del test drive.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Descrizione** (obbligatorio): descrivere il test drive, che cosa si intende dimostrare, gli obiettivi che l'utente può sperimentare, le funzionalità da esplorare e tutte le informazioni pertinenti per aiutare l'utente a determinare se acquistare l'offerta. In questo campo è possibile immettere fino a 3000 caratteri di testo. 
* **Informazioni sull'accesso** (obbligatorio per Azure Resource Manager e per i test drive delle app per la logica): descrivere tutto quello che un cliente deve sapere per poter accedere al test drive e usarlo. Descrivere dettagliatamente uno scenario per l'uso dell'offerta e fornire esattamente tutte le informazioni necessarie al cliente per accedere alle funzionalità durante il test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.
* **Manuale dell'utente** (obbligatorio): descrizione dettagliata approfondita dell'esperienza del test drive. Il manuale dell'utente deve trattare tutti gli aspetti che si vogliono far sperimentare al cliente con il test drive e deve fungere da riferimento per qualsiasi domanda del cliente. Il file deve essere in formato PDF e il nome deve essere assegnato (255 caratteri al massimo) dopo il caricamento.
* **Video: Aggiungi video** (facoltativo): è possibile caricare video in YouTube o Vimeo e farvi riferimento con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una panoramica delle informazioni che consentono di comprendere meglio il test drive, tra cui come usare correttamente le funzionalità dell'offerta e comprendere gli scenari in cui vengono evidenziati i vantaggi.
  * **Nome** (obbligatorio)
  * **Indirizzo** (solo YouTube o Vimeo, obbligatorio)
  * **Anteprima** (il file di immagine deve essere in formato PNG e 533 x 324 pixel).

Prima di continuare, selezionare **Salva bozza**.

## <a name="publish"></a>Pubblica

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, selezionare **Rivedi e pubblica** nell'angolo superiore destro del portale.

Esaminare lo stato di completamento di ogni sezione dell'offerta.
    - *Non avviato* : indica che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o che sono necessarie altre informazioni. Tornare alla sezione e aggiornarla.
    - *Completato* : indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in stato completo prima di poter inviare l'offerta.

Se questa è la prima volta che si pubblica questa offerta, è possibile fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per la comprensione dell'app.

Per inviare l'offerta per la pubblicazione, selezionare **Invia**. Si riceverà un messaggio di posta elettronica che indica che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione.

Tornare al Centro per i partner e selezionare **Passa allo stato Live** per l'offerta per pubblicare l'offerta al pubblico (o, se è un'offerta privata, per destinatari privati).

### <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

Il passaggio **Convalida manuale** del processo di pubblicazione rappresenta una revisione estesa dell'offerta e dei relativi asset tecnici associati (in particolare il modello di Azure Resource Manager) e i problemi vengono in genere visualizzati come collegamenti di richiesta pull. Per una spiegazione su come visualizzare e rispondere a queste richieste pull, vedere [Gestione del feedback di revisione](./azure-apps-review-feedback.md).

Se si verificano errori in uno o più passaggi di pubblicazione, correggerli prima di ripubblicare l'offerta.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
