---
title: Creare un'offerta di applicazione Azure-Marketplace commerciale Microsoft
description: Informazioni sui passaggi e le considerazioni per la creazione di una nuova offerta di applicazione Azure nel portale del Marketplace commerciale nel centro per i partner. È possibile elencare o vendere l'offerta di applicazione Azure in Azure Marketplace o tramite il programma Cloud Solution Provider (CSP).
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 22d6c37b59488633394d7f3ed5ca5b0c78371e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790688"
---
# <a name="create-an-azure-application-offer"></a>Creare un'offerta per un'applicazione di Azure

Questo articolo illustra i passaggi e le considerazioni per la creazione di una nuova offerta di applicazione Azure nel Marketplace commerciale. È necessario avere familiarità con questi concetti prima di creare una nuova offerta di applicazione Azure. 

Prima di poter pubblicare una nuova offerta di applicazione Azure, [creare un account Marketplace commerciale nel centro](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) per i partner e assicurarsi che l'account sia registrato nel programma commerciale Marketplace.

## <a name="before-you-begin"></a>Prima di iniziare

Per la progettazione, la compilazione e il test delle offerte di applicazioni Azure è necessario conoscere la piattaforma Azure e le tecnologie utilizzate per creare l'offerta. Il team tecnico deve avere il livello di conoscenza delle tecnologie Microsoft seguenti:

* Conoscenza di base dei [servizi di Azure](https://azure.microsoft.com/services/).
* Come [progettare e architettare le applicazioni Azure](https://azure.microsoft.com/solutions/architecture/).
* Conoscenza del funzionamento di [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage#storage)e rete di [Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Conoscenza operativa dei [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Conoscenza dell'utilizzo di [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Documentazione tecnica e risorse

Esaminare le risorse seguenti durante la preparazione dell'offerta di applicazione Azure per il Marketplace commerciale.

* [Comprendere modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

* Argomenti di avvio rapido:

    * [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/)
    * [Modelli di avvio rapido di Azure in GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Pubblicare una definizione di applicazione](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Distribuire un'app del catalogo di servizi](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Esercitazioni:

    * [Creare file di definizioni](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Pubblicare un'applicazione del Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Esempi:

    * [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Soluzioni di applicazioni gestite](https://docs.microsoft.com/azure/managed-applications/sample-projects)

Il video sulla [creazione di modelli di soluzioni e applicazioni gestite per Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) offre un'introduzione completa al tipo di offerta di applicazione Azure:

* Quali tipi di offerte sono disponibili;
* Quali asset tecnici sono necessari;
* Come creare un modello di Azure Resource Manager;
* Sviluppo e test dell'interfaccia utente dell'app;
* Come pubblicare l'offerta di app;
* Processo di revisione dell'applicazione.

### <a name="suggested-tools"></a>Strumenti suggeriti

Per la gestione dell'applicazione di Azure scegliere uno o entrambi gli ambienti di scripting seguenti:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

È consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) con le estensioni seguenti:
    * Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

È possibile esaminare gli strumenti disponibili nella pagina [strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/) . Inoltre, se si usa Visual Studio, il [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="types-of-azure-application-plans"></a>Tipi di piani per le applicazioni di Azure

Esistono due tipi di piani applicativi di Azure: modelli di soluzione e applicazioni gestite.

* Il **modello di soluzione** è uno dei modi principali per pubblicare una soluzione nel Marketplace. Usare questo tipo di piano quando la soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale (VM). Con un modello di soluzione è possibile automatizzare la fornitura di più risorse, tra cui macchine virtuali, rete e risorse di archiviazione, per offrire soluzioni IaaS complesse.  Per ulteriori informazioni sulla compilazione di modelli di soluzioni, vedere [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

* L' **applicazione gestita** è simile ai modelli di soluzione, con una differenza fondamentale. In un'applicazione gestita le risorse vengono distribuite in un gruppo di risorse gestito dall'entità di pubblicazione dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. In qualità di entità di pubblicazione dell'app, è possibile specificare il costo del supporto continuativo per la soluzione. Usa le applicazioni gestite per creare e distribuire con facilità applicazioni chiavi in mano completamente gestite ai tuoi clienti.  Per altre informazioni sui vantaggi e sui tipi di applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="technical-requirements"></a>Requisiti tecnici

Tutte le applicazioni Azure includono almeno due file nella cartella radice di un `.zip` archivio:

* Un file di modello Gestione risorse denominato [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Questo modello definisce le risorse da distribuire nella sottoscrizione di Azure del cliente.  Per esempi di modelli di Gestione risorse, vedere la [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) o il repository di modelli di [avvio rapido GitHub: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) corrispondente.

* Definizione dell'interfaccia utente per l'esperienza di creazione di applicazioni Azure denominata [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Nell'interfaccia utente specificare gli elementi che consentono ai consumer di fornire i valori dei parametri.

Tutte le nuove offerte per le applicazioni Azure devono includere un [GUID di attribuzione dell'utilizzo dei clienti partner di Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution). 

Per informazioni sui requisiti di pubblicazione per ogni piano dell'applicazione, vedere la pagina relativa ai requisiti di [pubblicazione](../marketplace-solution-templates.md) per i requisiti di pubblicazione e l' [offerta di applicazione gestita](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Creare una nuova offerta

>[!NOTE]
>Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner non verranno visualizzate nelle vetrine fino a quando non si ripubblica l'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/home).

1. Nel menu a sinistra selezionare **Commercial Marketplace** > **Overview**.

1. Nella pagina Panoramica selezionare **+ nuova offerta** > **applicazione Azure**.

    ![Viene illustrato il menu di spostamento a sinistra.](./media/new-offer-azure-app.png)

1. Nella pagina **nuova offerta** immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

     * Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta Marketplace e Azure Resource Manager modelli, se applicabile.
     * Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
     * Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

1. Immettere un **alias dell'offerta**. Questo è il nome usato per l'offerta nel centro per i partner.

     * Questo nome non viene usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati per i clienti.
     * Non è possibile modificare l'alias dell'offerta dopo aver selezionato **Crea**.

1. Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Installazione dell'offerta

Nella pagina **installazione offerta** è possibile configurare un test drive e la gestione dei lead per l'offerta. 

### <a name="test-drive"></a>Test drive

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di "provare prima di acquistare", ottenendo una maggiore conversione e la generazione di lead altamente qualificati. [Altre informazioni sulle unità di test](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita una test drive** . Per rimuovere test drive dall'offerta, deselezionare questa casella di controllo. Configurare l'ambiente di test drive nella sezione [configurazione tecnica test drive](#test-drive-technical-configuration) più avanti in questo argomento.

Per ulteriori informazioni, vedere [test drive dell'offerta nel Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive). È anche possibile leggere informazioni sulle [procedure consigliate di test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) e scaricare il [file PDF di test drives Overview](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (assicurarsi che il blocco popup sia disattivato)

>[!Note]
>Poiché tutte le applicazioni Azure vengono implementate usando un modello di Azure Resource Manager, l'unico tipo di test drive disponibili per un applicazione Azure è un [test drive basato su Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

## <a name="lead-management"></a>Lead management (Gestione di clienti potenziali)

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per altre informazioni, vedere [Cenni preliminari sulla gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Selezionare **Salva bozza** prima di continuare.

## <a name="properties"></a>Proprietà

La pagina delle **Proprietà** consente di definire le categorie e i settori usati per raggruppare l'offerta nel Marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

Selezionare almeno una e fino a tre categorie per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta.

### <a name="legal"></a>Note legali

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Elenco offerte

Questa pagina consente di gestire la copia e le immagini per l'offerta del Marketplace commerciale. 

### <a name="marketplace-details"></a>Dettagli del Marketplace

> [!NOTE]
> Non è necessario che il contenuto dell'offerta (ad esempio la descrizione, i documenti, le schermate e le condizioni per l'utilizzo) sia in inglese, purché la descrizione dell'offerta inizi con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche accettabile fornire un URL di *collegamento utile* per offrire contenuto in una lingua diversa da quella usata nell'offerta di visualizzazione del contenuto.

#### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato ai clienti come titolo dell'elenco di offerte. Questo campo viene prepopolato con il testo immesso per l' **alias offerta** al momento della creazione dell'offerta, ma è possibile modificare questo valore. Questo nome può essere registrato (e possono essere inclusi marchi o simboli di copyright). Il nome non può contenere più di 50 caratteri e non può includere emoji.

#### <a name="search-results-summary"></a>Riepilogo risultati ricerca

Fornire una breve descrizione dell'offerta (fino a 100 caratteri), che può essere usata nei risultati della ricerca.

#### <a name="long-summary"></a>Riepilogo lungo

Fornire una descrizione più lunga dell'offerta (fino a 256 caratteri). Questa descrizione può essere usata nei risultati della ricerca.

#### <a name="description"></a>Descrizione

Fornire una descrizione più lunga dell'offerta (fino a 3.000 caratteri). Questa descrizione verrà visualizzata ai clienti nella panoramica dell'elenco. Includere la proposta di valore dell'offerta, i vantaggi principali, le associazioni di categoria e/o di settore, le opportunità di acquisto in-app ed eventuali divulgazioni necessarie.

SHere sono alcuni suggerimenti per scrivere la descrizione:

* Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includere gli elementi seguenti:
  * Descrizione dell'offerta.
  * Il tipo di utente che trae vantaggio dall'offerta.
  * Esigenze dei clienti o problemi che l'offerta indirizzi.
* Tenere presente che le prime frasi potrebbero essere visualizzate nei risultati del motore di ricerca.
* Non fare affidamento sulle funzionalità e sulle funzionalità per vendere l'offerta. Concentrarsi invece sul valore fornito dall'offerta.
* Usare parole specifiche del settore o basate sui vantaggi.
* Prendere in considerazione l'uso di tag HTML per formattare la descrizione in modo da renderla più accattivante.

#### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Facoltativamente, è possibile immettere fino a tre parole chiave di ricerca per aiutare i clienti a trovare l'offerta nel Marketplace. Per ottenere risultati ottimali, usare anche queste parole chiave nella descrizione.

#### <a name="privacy-policy-link"></a>Collegamento all'informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida.

### <a name="useful-links"></a>Collegamenti utili

Aggiungere collegamenti ai documenti online supplementari facoltativi sulla soluzione selezionando **+ Aggiungi un collegamento**.

### <a name="contact-information"></a>Informazioni contatto

Specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per un contatto di **supporto**tecnico, un **contatto tecnico**e un **contatto del programma CSP**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e potranno essere fornite ai partner CSP. Alcuni contatti possono richiedere informazioni aggiuntive.

### <a name="marketplace-media"></a>Supporti Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate provocheranno il rifiuto dell'invio.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi https://upload.xboxlive.com il servizio usato dal centro per i partner.

#### <a name="store-logos"></a>Logo dello Store

Fornire i file PNG del logo dell'offerta nelle tre dimensioni di pixel seguenti:

- **Piccolo** (48 x 48)
- **Media** (90 x 90)
- **Grande** (216 x 216)
- **Wide** (255 x 115)

Tutti e tre i logo sono obbligatori e vengono usati in posizioni diverse nell'elenco.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere fino a cinque schermate che mostrano il funzionamento dell'offerta. Ogni screenshot deve avere una dimensione di 1280 x 720 pixel e in formato PNG. Ogni screenshot deve includere una didascalia.

#### <a name="videos"></a>Video

È possibile aggiungere fino a cinque video che illustrano l'offerta. Questi devono essere ospitati in un servizio video esterno. Immettere il nome di ogni video, l'indirizzo Web e un'immagine PNG di anteprima del video a 1280 x 720 pixel.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

- [Procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Selezionare **Salva bozza** prima di continuare.

## <a name="preview-audience"></a>Anteprima destinatari

Nella scheda Anteprima scegliere un **pubblico di anteprima** limitato per convalidare l'offerta prima di pubblicarla in un gruppo più ampio di destinatari del Marketplace.

> [!IMPORTANT]
> Dopo aver verificato l'offerta in anteprima, selezionare **Go Live** per pubblicare l'offerta nel pubblico del Marketplace commerciale.

I destinatari dell'anteprima sono identificati dai GUID di ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ogni utente. Nessuno di questi campi può essere visualizzato dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina **sottoscrizioni** in portale di Azure.

Aggiungere almeno un ID sottoscrizione di Azure, singolarmente (fino a 10) o caricando un file CSV (fino a 100). Aggiungendo questi ID sottoscrizione, si definiscono gli utenti che possono visualizzare l'anteprima dell'offerta prima della pubblicazione in tempo reale. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per il test delle modifiche dell'offerta o degli aggiornamenti all'offerta.

> [!NOTE]
> Un pubblico di anteprima è diverso da un pubblico privato. Un pubblico di anteprima può accedere all'offerta _prima_ che venga pubblicata nel Marketplace. Possono visualizzare e convalidare tutti i piani, inclusi quelli che saranno disponibili solo per un pubblico privato dopo che l'offerta è stata completamente pubblicata nel Marketplace. Un pubblico privato (definito nella scheda piano **prezzi e disponibilità** ) dispone di accesso esclusivo a un piano specifico.

Selezionare **Salva bozza** prima di continuare.

## <a name="technical-configuration"></a>Configurazione tecnica

Completare questa sezione solo se l'offerta include un'applicazione gestita che emetterà eventi di misurazione usando l'API del servizio di controllo del Marketplace. Immettere l' **ID tenant Azure Active Directory** e il **Azure Active Directory ID applicazione** che il servizio utilizzerà per la creazione di eventi di misurazione.

Selezionare **Salva bozza** prima di continuare.

## <a name="technical-configuration-offer-level"></a>Configurazione tecnica (livello offerta)

>[!Note]
>I dettagli tecnici a livello di offerta sono facoltativi.  È necessario configurare questi dettagli solo se si pubblica un'applicazione gestita con fatturazione a consumo e si dispone di un servizio che verrà autenticato con un token di sicurezza Azure AD. Per altre informazioni, vedere [strategie di autenticazione](./marketplace-metering-service-authentication.md) per le diverse opzioni di autenticazione.

La configurazione tecnica definisce i dettagli (ID tenant e ID app) usati per identificare il servizio, che genera eventi di misurazione per un'applicazione gestita usando le API del [servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md).  Immettere l'identità che il servizio utilizzerà per la creazione di eventi di misurazione.

* **Azure ad ID tenant** (obbligatorio): all'interno portale di Azure, è necessario [creare un'app Azure Active Directory (ad)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) in modo che sia possibile convalidare la connessione tra i due servizi sia dietro una comunicazione autenticata. Per trovare l' [ID tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), passare alla Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).
* **Azure ad ID app** (obbligatorio): sono necessari anche l' [ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e una chiave di autenticazione. Per ottenere tali valori, passare alla Azure Active Directory e selezionare **registrazioni app**, quindi cercare il numero di **ID dell'applicazione** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e). Per trovare la chiave di autenticazione, passare a **Impostazioni** e selezionare **chiavi**. Sarà necessario fornire una descrizione e una durata, quindi verrà fornito un valore numerico.

>[!Note]
>L'ID applicazione di Azure verrà associato all'ID editore e potrà essere riutilizzato solo all'interno di questo account del server di pubblicazione.

>[!Note]
>Questa configurazione è obbligatoria se si vuole usare l' [evento di utilizzo batch](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#batch-usage-event).  Se si vuole inviare un [evento di utilizzo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#usage-event), è anche possibile usare il [servizio metadati dell'istanza](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per ottenere il [token web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app).

## <a name="plan-overview"></a>Panoramica del piano

Questa scheda consente di fornire diverse opzioni di piano all'interno della stessa offerta. Questi piani (definiti SKU nel portale Cloud Partner) possono differire in termini di tipo di piano (modello di soluzione e applicazione gestita), monetizzazione o destinatari.  Configurare almeno un piano per elencare l'offerta nel Marketplace.

Una volta creati, vengono visualizzati i nomi, gli ID, il tipo di piano, la disponibilità (pubblico o privato), lo stato di pubblicazione corrente e le eventuali azioni disponibili in questa scheda.

Le **azioni** disponibili nella **Panoramica del piano** variano a seconda dello stato corrente del piano e possono includere:

* Se lo stato del piano è **bozza** -eliminazione bozza.
* Se lo stato del piano è **Live** -stop sell plan o Sync private audience.

### <a name="create-new-plan"></a>Crea nuovo piano

***ID piano*** : creare un ID piano univoco per ogni piano nell'offerta. Questo ID sarà visibile ai clienti nell'URL del prodotto.  Usare solo lettere minuscole, caratteri alfanumerici, trattini o caratteri di sottolineatura. Per questo ID piano sono consentiti al massimo 50 caratteri. Questo ID non può essere modificato dopo aver selezionato Crea.

***Nome del piano*** : i clienti visualizzeranno questo nome per decidere quale piano selezionare nell'offerta. Creare un nome di offerta univoco per ogni piano nell'offerta. Il nome del piano viene usato per distinguere i piani software che possono far parte della stessa offerta (ad esempio, il nome dell'offerta: Windows Server; piani: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Pianificare l'installazione

Questa scheda consente di impostare la configurazione di alto livello per il tipo di piano, il fatto che riutilizzi i pacchetti di un altro piano e i cloud in cui il piano dovrebbe essere disponibile. Le risposte in questa scheda influiscono sui campi che vengono visualizzati in altre schede per lo stesso piano.

#### <a name="plan-type"></a>Tipo di piano
Selezionare il tipo di piano per l'offerta. Il piano di un **modello di soluzione** è gestito interamente dal cliente. Un piano di **applicazione gestita** consente agli editori di gestire l'applicazione per conto del cliente. Per informazioni dettagliate, vedere [tipi di piani per le applicazioni di Azure](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Riutilizzare la configurazione tecnica

Se si dispone di più di un piano dello stesso tipo e i pacchetti sono identici, è possibile selezionare **questo piano riutilizza i pacchetti da un altro piano**.  Quando si seleziona questa opzione, sarà possibile selezionare uno degli altri piani dello stesso tipo per questa offerta per riutilizzare i pacchetti. 

>[!Note]
>Quando si riutilizzano i pacchetti di un altro piano, l'intera scheda configurazione tecnica scomparirà da questo piano. I dettagli relativi alla configurazione tecnica dell'altro piano, inclusi gli eventuali aggiornamenti apportati in futuro, verranno utilizzati anche per questo piano.<br><br>Questa impostazione non può essere modificata dopo la pubblicazione del piano.

#### <a name="azure-regions-cloud"></a>Aree di Azure (cloud)

Il piano deve essere reso disponibile in almeno un'area di Azure.

Selezionare l'opzione **globale di Azure** per rendere il piano disponibile per i clienti in tutte le aree globali di Azure con integrazione del Marketplace commerciale. Per informazioni dettagliate, vedere [disponibilità geografica e supporto della valuta](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selezionare l'opzione **Azure per enti pubblici** per rendere il piano disponibile nell'area di [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) . Questa area garantisce l'accesso controllato per i clienti da entità federali, statali, locali o tribali degli Stati Uniti, oltre ai partner idonei per la loro gestione. L'autore è responsabile di tutti i controlli di conformità, le misure di sicurezza e le procedure consigliate. Azure per enti pubblici USA Data Center e reti fisicamente isolate (situate solo negli Stati Uniti).

Prima di eseguire la pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), testare e convalidare il piano nell'ambiente in quanto determinati endpoint potrebbero essere diversi. Per configurare e testare il piano, richiedere un account di valutazione da [Microsoft Azure per enti pubblici versione di valutazione](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Dopo che il piano è stato pubblicato e disponibile in un'area di Azure specifica, non è possibile rimuovere tale area.

#### <a name="azure-government-certifications"></a>Certificazioni di Azure per enti pubblici

Questa opzione è visibile solo se è **stato selezionato Azure per enti pubblici**.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e normative governative. Ad esempio, FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per sensibilizzare le certificazioni per questi programmi, è possibile fornire fino a 100 collegamenti che li descrivono. Questi possono essere collegamenti all'inserzione diretta del programma o collegamenti a descrizioni della conformità con essi nei propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

Selezionare **Salva bozza** prima di continuare.

### <a name="plan-listing"></a>Elenco di piani

Qui è possibile configurare i dettagli dell'elenco del piano. In questa scheda vengono visualizzate informazioni specifiche che possono variare tra i piani nella stessa offerta.

#### <a name="plan-name"></a>Nome piano

Questa operazione è già compilata con il nome assegnato al piano al momento della creazione. Questo nome viene visualizzato nel Marketplace come titolo del piano ed è limitato a 100 caratteri.

#### <a name="plan-summary"></a>Riepilogo del piano

Fornire un breve riepilogo del piano (non l'offerta). Questo riepilogo è limitato a 100 caratteri.

#### <a name="plan-description"></a>Descrizione del piano

Viene descritto cosa rende univoco questo piano software, nonché le differenze tra i piani all'interno dell'offerta. Non descrivere l'offerta, ma solo il piano. La descrizione del piano può contenere un massimo di 2.000 caratteri.

Selezionare **Salva bozza** prima di continuare.

### <a name="availability-solution-template-plans-only"></a>Disponibilità (solo piani di modelli di soluzione)

Il piano può essere reso visibile a tutti, solo a specifici clienti (destinatari privati) e se rendere il piano nascosto per l'uso da parte di un altro modello di soluzione o di applicazioni gestite.

#### <a name="plan-visibility"></a>Visibilità del piano

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando gli ID sottoscrizione di Azure.

Selezionare **questo è un piano privato** per rendere il piano privato e visibile solo ai destinatari di propria scelta. Una volta pubblicato come piano privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Una volta che un piano è stato pubblicato come visibile a tutti, deve rimanere visibile a tutti gli utenti. non può essere riconfigurato come piano privato.

Se si rende privata la pianta, immettere un **ID sottoscrizione di Azure** e la relativa descrizione. Ognuno è un pubblico che avrà accesso a questo piano privato. L'accesso viene assegnato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione di Azure assegnato. Aggiungere un massimo di 10 ID sottoscrizione di clienti singolarmente oppure 20.000 importando un file CSV. Gli ID sottoscrizione di Azure sono rappresentati come GUID e le lettere devono essere minuscole.

>[!Note]
>Un pubblico privato o con restrizioni è diverso dai destinatari di anteprima definiti nella scheda **Anteprima** . Un pubblico di anteprima può accedere all'offerta _prima_ della sua pubblicazione nel Marketplace. Sebbene la scelta del pubblico privato venga applicata solo a un piano specifico, i destinatari di anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

#### <a name="hide-plan"></a>Nascondi piano

Se il modello di soluzione deve essere distribuito solo indirettamente quando si fa riferimento a un altro modello di soluzione o a un'applicazione gestita, selezionare questa casella per pubblicare il modello di soluzione, ma nasconderlo dai clienti che eseguono ricerche e ricerche direttamente.

Selezionare **Salva bozza** prima di continuare.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Prezzi e disponibilità (solo piani di applicazioni gestite)

Usarlo per configurare i **mercati** in cui sarà disponibile questo piano, i **prezzi** al mese della gestione della soluzione e la **visibilità del piano** se solo i clienti specifici dovrebbero visualizzarlo (un pubblico privato).

Selezionare **Salva bozza** prima di continuare.

#### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per qualsiasi località di mercato in cui si desidera rendere disponibile questo piano. Una casella di ricerca e un pulsante per la selezione dei paesi in cui vengono riportate le imposte, in cui Microsoft si impegnano le vendite e le imposte per conto dell'utente, vengono fornite informazioni utili.

Se sono già stati impostati i prezzi per il piano in Stati Uniti dollari (USD) e si aggiunge un altro percorso di mercato, il prezzo del nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Rivedere sempre il prezzo di ogni mercato prima della pubblicazione. I prezzi possono essere esaminati tramite il collegamento "Esporta prezzi (xlsx)" dopo aver salvato le modifiche.

#### <a name="pricing"></a>Prezzi

Fornire il prezzo per mese per questo piano.  Questo prezzo è in aggiunta a qualsiasi infrastruttura di Azure o a costi software con pagamento in base al consumo sostenuti dalle risorse distribuite da questa soluzione.

Oltre al prezzo mensile, è anche possibile impostare i prezzi per l'utilizzo di unità non standard usando la [fatturazione](./azure-app-metered-billing.md)a consumo.  Se lo si desidera, è possibile impostare il prezzo per mese su zero e addebitare esclusivamente la fatturazione a consumo. 

I prezzi impostati in USD (USD = Stati Uniti dollaro) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti quando vengono salvati. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e esaminando il prezzo in ogni mercato. Se si desidera impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi. 

>[!Note]
>È innanzitutto necessario salvare le modifiche ai prezzi per consentire l'esportazione dei dati dei prezzi.

Esaminare attentamente i prezzi prima della pubblicazione, in quanto esistono alcune limitazioni relative a ciò che può essere modificato dopo la pubblicazione di un piano.  

>[!Note]
>Una volta pubblicato il prezzo per un mercato nel piano, non è possibile modificarlo in un secondo momento.

#### <a name="plan-visibility"></a>Visibilità del piano

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando gli ID sottoscrizione di Azure.

Selezionare **questo è un piano privato** per rendere il piano privato e visibile solo ai destinatari di propria scelta. Una volta pubblicato come piano privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Una volta che un piano è stato pubblicato come visibile a tutti, deve rimanere visibile a tutti gli utenti. non può essere riconfigurato come piano privato.

Se si rende privata la pianta, immettere un **ID sottoscrizione di Azure** e la relativa descrizione. Ognuno è un pubblico che avrà accesso a questo piano privato. L'accesso viene assegnato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione di Azure assegnato. Aggiungere un massimo di 10 ID sottoscrizione di clienti singolarmente oppure 20.000 importando un file CSV. Gli ID sottoscrizione di Azure sono rappresentati come GUID e le lettere devono essere minuscole.

>[!Note]
>Un pubblico privato o con restrizioni è diverso dai destinatari di anteprima definiti nella scheda **Anteprima** . Un pubblico di anteprima può accedere all'offerta _prima_ della sua pubblicazione nel Marketplace. Sebbene la scelta del pubblico privato venga applicata solo a un piano specifico, i destinatari di anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

### <a name="technical-configuration"></a>Configurazione tecnica 

Questa scheda consente di caricare il pacchetto di distribuzione che consentirà ai clienti di distribuire il piano.

>[!Note]
>Questa scheda non sarà visibile se il piano è stato configurato per riutilizzare i pacchetti di un altro piano nella scheda **Imposta piano** .

#### <a name="package-details"></a>Dettagli del pacchetto

Questa scheda consente di modificare la versione bozza della configurazione tecnica.

**Versione** : assegnare la versione corrente della configurazione tecnica.  Incrementare questa versione ogni volta che si pubblica una modifica in questa pagina. Il formato `{integer}.{integer}.{integer}`della versione deve essere.

**File del pacchetto** (con estensione zip): questo pacchetto contiene tutti i file di modello necessari per il piano, nonché eventuali risorse aggiuntive, incluse in un `.zip` file.

Tutti i pacchetti del piano dell'applicazione Azure devono includere questi due file nella cartella radice `.zip` di un archivio:

* Un file di modello Gestione risorse denominato [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Questo modello automatizza la distribuzione delle risorse nella sottoscrizione di Azure Customers.  Per esempi di modelli di Gestione risorse, vedere la [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) o il repository di modelli di [avvio rapido GitHub: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) corrispondente.
* Definizione dell'interfaccia utente per l'esperienza di creazione di applicazioni Azure denominata [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Le dimensioni massime dei file supportate sono:

* Fino a 1 GB di dimensioni totali `.zip` dell'archivio compresso
* Fino a 1 GB per ogni singolo file non compresso nell' `.zip` archivio  

Tutte le nuove offerte di applicazioni Azure devono includere anche un GUID di [attribuzione dell'utilizzo dei clienti partner di Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) .

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

I piani di applicazioni gestite richiedono informazioni aggiuntive su questa scheda.

#### <a name="previously-published-packages"></a>Pacchetti pubblicati in precedenza

La sottoscheda **pacchetti pubblicati in precedenza** consente di visualizzare tutte le versioni pubblicate della configurazione tecnica.

#### <a name="enable-just-in-time-jit-access"></a>Abilitare l'accesso just-in-time (JIT)

Selezionare questa opzione per abilitare l'accesso JIT (just-in-Time) per questo piano.  L'accesso JIT consente di richiedere l'accesso con privilegi elevati alle risorse di un'applicazione gestita per la risoluzione dei problemi o la manutenzione. Si ha sempre accesso in sola lettura alle risorse, ma per un periodo di tempo specifico è possibile avere un accesso maggiore.  Per altre informazioni, vedere [abilitare e richiedere l'accesso JIT (just-in-Time) per le applicazioni gestite di Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Per richiedere agli utenti dell'applicazione gestita di concedere l'accesso permanente all'account, lasciare deselezionata questa opzione.

>[!Note]
>Assicurarsi di aggiornare il `createUiDefinition.json` file per supportare questa funzionalità.  

#### <a name="deployment-mode"></a>Modalità di distribuzione

Consente di scegliere se configurare la modalità di distribuzione **completa** o **incrementale** durante la distribuzione del piano: 

* In **modalità completa**, una ridistribuzione dell'applicazione da parte del cliente comporterà la rimozione delle risorse nel gruppo di risorse gestite se le risorse non sono definite in `mainTemplate.json`. 
* In **modalità incrementale**, una ridistribuzione dell'applicazione lascia invariate le risorse esistenti.

Per altre informazioni sulle modalità di distribuzione, vedere [Azure Resource Manager modalità di distribuzione](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

#### <a name="notification-endpoint-url"></a>URL dell'endpoint di notifica

Specificare un endpoint del webhook HTTPS per ricevere le notifiche relative a tutte le operazioni CRUD nelle istanze di applicazioni gestite di questa versione del piano.

#### <a name="customize-allowed-customer-actions"></a>Personalizzare le azioni dei clienti consentite

Selezionare questa opzione per specificare le azioni che i clienti possono eseguire sulle risorse gestite, oltre alle azioni`*/read`"" disponibili per impostazione predefinita. 

Elencare le azioni aggiuntive che si desidera consentire al cliente di eseguire qui, separate da punti e virgola.  Per altre informazioni, vedere informazioni sulle [assegnazioni Deny per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Per informazioni sulle azioni disponibili, vedere [Operazioni di provider di risorse con Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Ad esempio, per consentire agli utenti di riavviare le macchine virtuali, aggiungere `Microsoft.Compute/virtualMachines/restart/action` alle azioni consentite.

#### <a name="global-azure--azure-government-cloud"></a>Azure globale/Cloud Azure per enti pubblici

Indicare chi deve avere l'accesso di gestione a questa applicazione gestita in ogni cloud supportato. Gli utenti, i gruppi o le applicazioni a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite vengono identificati usando le identità Azure Active Directory (AAD).

**Azure Active Directory ID tenant** : l'ID tenant di AAD (noto anche come ID directory) contenente le identità degli utenti, dei gruppi o delle applicazioni a cui si vogliono concedere le autorizzazioni. È possibile trovare l'ID tenant di AAD nella portale di Azure, in [proprietà per Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Autorizzazioni** : aggiungere il Azure Active Directory ID oggetto dell'utente, del gruppo o dell'applicazione a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite. Identificare l'utente in base al relativo ID principale, disponibile nel pannello [Azure Active Directory utenti del portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Per ogni entità, selezionare uno dei Azure AD ruoli predefiniti dall'elenco (proprietario o collaboratore). Il ruolo selezionato descrive le autorizzazioni che l'entità avrà sulle risorse nella sottoscrizione del cliente. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Per ulteriori informazioni sul controllo degli accessi in base al ruolo (RBAC), vedere [Introduzione a RBAC nel portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Sebbene sia possibile aggiungere fino a 100 autorizzazioni per cloud, è in genere più semplice creare un gruppo di utenti Active Directory e specificarne l'ID nell'"ID entità". In questo modo sarà possibile aggiungere altri utenti al gruppo di gestione dopo la distribuzione del piano e ridurre la necessità di aggiornare il piano solo per aggiungere altre autorizzazioni.

#### <a name="policy-settings"></a>Impostazioni dei criteri

Applicare i [criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview) all'applicazione gestita per specificare i requisiti di conformità per la soluzione distribuita. Per le definizioni dei criteri e il formato dei valori dei parametri, vedere [Esempi di criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/index). È possibile configurare un massimo di cinque criteri e una sola istanza di ogni opzione di criteri. Alcuni criteri richiedono parametri aggiuntivi. Lo SKU Standard è necessario per i criteri di controllo. Il nome del criterio è limitato a 50 caratteri.

Selezionare **Salva bozza** prima di continuare.

## <a name="co-sell-with-microsoft"></a>Co-selling con Microsoft

Fornire informazioni sulla scheda co-Sell è interamente facoltativo per la pubblicazione dell'offerta. È necessario per ottenere lo stato pronto per la co-selling e per il co-selling IP. Le informazioni fornite verranno utilizzate dai team di vendita Microsoft per ottenere ulteriori informazioni sulla soluzione durante la valutazione dell'idoneità alle esigenze dei clienti. Non è disponibile direttamente per i clienti.

Per informazioni dettagliate su questa scheda, vedere [opzione di co-selling nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="resell-through-csps"></a>Rivendi tramite CSP

Espandi la portata dell'offerta rendendola disponibile ai partner nel programma [Cloud Solution Provider](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Questo consente ai rivenditori di vendere l'offerta ai clienti e creare soluzioni in bundle.

Selezionare **Salva bozza** prima di continuare.

## <a name="test-drive"></a>Test drive

Configurare una dimostrazione (test drive) che consente ai clienti di provare l'offerta prima di acquistarla. Per creare un ambiente dimostrativo che consente ai clienti di provare l'offerta per un periodo di tempo fisso, vedere [test drive dell'offerta nel Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Per abilitare una test drive, selezionare la casella di controllo **Abilita un test drive** nella scheda [installazione offerta](#test-drive) . Per rimuovere test drive dall'offerta, deselezionare questa casella di controllo.

### <a name="test-drive-technical-configuration"></a>Configurazione tecnica test drive

- **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione della distribuzione

Per consentire la distribuzione del test drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca (non necessaria per Power BI unità di test).

* **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile [creare una sottoscrizione di Azure separata](https://docs.microsoft.com/azure/billing/billing-create-subscription) da usare per le unità di test se non ne è già presente uno. È possibile trovare l'ID sottoscrizione di Azure accedendo al [portale di Azure](https://portal.azure.com/) e passando alla scheda **sottoscrizioni** del menu a sinistra. Selezionando la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5AB6-6789-1h234g764ghty".
* **Azure ad ID tenant** (obbligatorio): immettere l' [id tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile cercare l'ID tenant dell'organizzazione usando l'URL del nome di dominio in [https://www.whatismytenantid.com](https://www.whatismytenantid.com):.
* **Azure ad nome del tenant** (obbligatorio per la 365 dinamica): immettere il nome del Azure Active Directory (ad). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/), nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.
* **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu di spostamento a sinistra, selezionare **registrazioni app**, quindi cercare il numero di **ID dell'applicazione** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).
* **Azure Active Directory segreto client applicazione** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu di navigazione a sinistra, selezionare **registrazioni app**, quindi selezionare l'app test drive. Selezionare quindi **certificati e segreti**, fare clic **su nuovo segreto client**, immettere una descrizione, selezionare **mai** sotto **scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore prima di uscire dalla pagina.

Selezionare **Salva bozza** prima di continuare.

### <a name="marketplace-listing-optional"></a>Inserzione nel Marketplace (facoltativo)

Descrivere l'esperienza test drive.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Descrizione** (obbligatoria): descrivere la test drive, gli elementi che verranno illustrati, gli obiettivi dell'utente per sperimentare, le funzionalità da esplorare e tutte le informazioni rilevanti per aiutare l'utente a determinare se acquisire l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 
* **Informazioni di accesso** (obbligatorie per Azure Resource Manager e unità di test per la logica): spiega cosa è necessario sapere al cliente per accedere a questo test drive e usarlo. Esaminare uno scenario per l'uso dell'offerta e esattamente ciò che il cliente deve conoscere per accedere alle funzionalità nell'test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.
* **Manuale dell'utente** (obbligatorio): una procedura dettagliata dell'esperienza test drive. Il manuale dell'utente dovrebbe coprire esattamente ciò che si vuole che il cliente possa ottenere dall'esperienza del test drive e fungere da riferimento per eventuali domande. Il file deve essere in formato PDF ed essere denominato (255 caratteri al massimo) dopo il caricamento.
* **Video: aggiungere video** (facoltativo): è possibile caricare video in YouTube o Vimeo e farvi riferimento con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una panoramica delle informazioni che consentono di comprendere meglio le test drive, tra cui come usare correttamente le funzionalità dell'offerta e comprendere gli scenari che ne evidenziano i vantaggi.
  * **Nome** (obbligatorio)
  * **Indirizzo** (solo YouTube o Vimeo; obbligatorio)
  * **Anteprima** (il file di immagine deve essere in formato PNG e 533 x 324 px).

Selezionare **Salva bozza** prima di continuare.

## <a name="publish"></a>Pubblicazione

Una volta completate tutte le sezioni obbligatorie dell'offerta, selezionare **revisione e pubblicazione** nell'angolo superiore destro del portale.

Esaminare lo stato di completamento di ogni sezione dell'offerta.
    - *Non avviato* : indica che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o che sono necessarie altre informazioni. Tornare alla sezione o aggiornarla.
    - *Complete* : indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.

Se questa è la prima volta che si pubblica questa offerta, è possibile fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per comprendere l'app.

Selezionare **Submit (Invia** ) per inviare l'offerta per la pubblicazione. Verrà inviato un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare.

Tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta al pubblico (o se un'offerta privata, per i destinatari privati).

### <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

Il passaggio di **convalida manuale** del processo di pubblicazione rappresenta un'ampia revisione dell'offerta e delle risorse tecniche associate (in particolare il modello di Azure Resource Manager), i problemi vengono in genere presentati come collegamenti richiesta pull (PR). Per una spiegazione su come visualizzare e rispondere a queste richieste pull, vedere [Gestione del feedback di revisione](./azure-apps-review-feedback.md).

Se si verificano errori in uno o più passaggi di pubblicazione, correggerli prima di ripubblicare l'offerta.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
