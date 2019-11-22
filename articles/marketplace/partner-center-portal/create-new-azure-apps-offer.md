---
title: Creare una nuova offerta di app di Azure nel Marketplace commerciale
description: Come creare una nuova offerta di app di Azure per l'inserzione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) usando il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: d7e05f12c04136c8394dbcb27b7a950fc5ce85d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281272"
---
# <a name="create-an-azure-application-offer"></a>Creare un'offerta per un'applicazione di Azure

I passaggi per la pubblicazione di un'offerta di applicazione Azure nel Marketplace commerciale sono descritti qui.

## <a name="azure-application-offer-type"></a>Tipo di offerta applicazione Azure

Questo argomento descrive le nozioni di base sulle offerte per le applicazioni Azure.  È necessario avere familiarità con questi concetti prima di avviare il processo di pubblicazione di una nuova offerta di applicazione Azure nel Marketplace.

### <a name="publishing-overview"></a>Panoramica della pubblicazione

Il video sulla [creazione di modelli di soluzioni e le applicazioni gestite per Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) sono un'introduzione al tipo di offerta di applicazione Azure:

* Quali tipi di offerte sono disponibili;
* Quali asset tecnici sono necessari;
* Come creare un modello di Azure Resource Manager;
* Sviluppo e test dell'interfaccia utente dell'app;
* Come pubblicare l'offerta di app;
* Processo di revisione dell'applicazione.

### <a name="types-of-azure-application-plans"></a>Tipi di piani per le applicazioni di Azure

Esistono due tipi di piani applicativi di Azure, applicazioni gestite e modelli di soluzioni.

* Il **modello di soluzione** è uno dei modi principali per pubblicare una soluzione nel Marketplace. Questo tipo di piano viene usato quando la soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale (VM).  Con un modello di soluzione è possibile automatizzare la fornitura di più risorse, tra cui macchine virtuali, rete e risorse di archiviazione, per offrire soluzioni IaaS complesse.  Per ulteriori informazioni sulla creazione di modelli di soluzione, vedere la documentazione [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) .

* L' **applicazione gestita** è simile ai modelli di soluzione, con una differenza fondamentale. In un'applicazione gestita le risorse vengono distribuite in un gruppo di risorse gestito dall'entità di pubblicazione dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. In qualità di entità di pubblicazione dell'app, è possibile specificare il costo del supporto continuativo per la soluzione. Usa le applicazioni gestite per creare e distribuire con facilità applicazioni chiavi in mano completamente gestite ai tuoi clienti.  Per altre informazioni sui vantaggi e sui tipi di applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](https://docs.microsoft.com/azure/managed-applications/overview).

Tutte le applicazioni Azure includono almeno due file nella cartella radice di un archivio `.zip`:

* Un file di modello Gestione risorse denominato [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Questo è il modello che definisce le risorse da distribuire nella sottoscrizione di Azure del cliente.  Per esempi di modelli di Gestione risorse, vedere la [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) o il repository di modelli di [avvio rapido GitHub: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) corrispondente.

* Definizione dell'interfaccia utente per l'esperienza di creazione di applicazioni Azure denominata [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Nell'interfaccia utente specificare gli elementi che consentono ai consumer di fornire i valori dei parametri.

Tutte le nuove offerte per le applicazioni Azure devono includere un [GUID di attribuzione dell'utilizzo dei clienti partner di Azure](??).

### <a name="before-you-begin"></a>Prima di iniziare

Rivedere la documentazione seguente relativa all'applicazione di Azure, che include argomenti di avvio rapido, esercitazioni ed esempi.

* [Comprendere modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Argomenti di avvio rapido:

    * [Modelli di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/)
    * [Modelli di avvio rapido di Azure in GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Pubblicare una definizione di applicazione](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Distribuire un'app del catalogo di servizi](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Esercitazioni:

    * [Creare file di definizioni](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Pubblicare un'applicazione del Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Esempi:

    * [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Soluzioni di applicazioni gestite](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Nozioni fondamentali sulla conoscenza tecnica

La progettazione, la compilazione e il test di queste risorse richiedono tempo e conoscenze tecniche sia della piattaforma di Azure che delle tecnologie usate per creare l'offerta.

Il team tecnico deve avere il livello di conoscenza delle tecnologie Microsoft seguenti:

* Conoscenza di base dei [servizi di Azure](https://azure.microsoft.com/services/).
* Come [progettare e architettare le applicazioni Azure](https://azure.microsoft.com/solutions/architecture/).
* Conoscenza del funzionamento di [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage#storage)e rete di [Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Conoscenza operativa dei [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Conoscenza dell'utilizzo di [JSON](https://www.json.org/).

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

È possibile esaminare gli strumenti disponibili nella pagina [strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/) .  Inoltre, se si usa Visual Studio, il [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Creare un'offerta per un'applicazione di Azure

Prima di poter creare un'offerta di applicazione Azure, è prima necessario [creare un account del centro](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) per i partner e aprire il [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), con la scheda **Panoramica** selezionata.

>[!Note]
>Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel centro per i partner verranno aggiornate solo nel sistema e nelle vetrine solo dopo la nuova pubblicazione.  Assicurarsi di inviare l'offerta per la pubblicazione dopo aver apportato le modifiche.

### <a name="create-a-new-offer"></a>Creare una nuova offerta

Selezionare il pulsante **+ nuova offerta** , quindi selezionare la voce di menu **applicazione Azure** . Verrà visualizzata la finestra di dialogo **nuova offerta** .

### <a name="offer-id-and-alias"></a>ID offerta e alias

* **ID offerta**: identificatore univoco per ogni offerta nell'account. Questo ID sarà visibile ai clienti nell'indirizzo URL per l'offerta del Marketplace e i modelli Azure Resource Manager (se applicabile). <br> <br> L'ID offerta deve essere costituito da caratteri alfanumerici minuscoli (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti). È limitato a 50 caratteri e non può essere modificato dopo aver selezionato Crea. <br> <br> Se ad esempio si immette `test-offer-1` qui, l'URL dell'offerta verrà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`. 

* **Alias offerta**: nome usato per fare riferimento all'offerta all'interno del centro per i partner. Questo nome non verrà usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori che verranno visualizzati ai clienti. Questo valore non può essere modificato dopo aver selezionato **Crea**.

Dopo aver immesso l' **ID offerta** e l' **alias offerta**, selezionare **Crea**. Sarà quindi possibile lavorare su tutte le altre parti dell'offerta.

## <a name="offer-setup"></a>Installazione dell'offerta

La pagina **installazione offerta** richiede le informazioni seguenti. Assicurarsi di selezionare **Salva** dopo aver completato questi campi.

### <a name="test-drive"></a>Test drive

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di provare prima di acquistare, ottenendo una maggiore conversione e la generazione di lead altamente qualificati. [Altre informazioni sulle unità di test.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Per abilitare una test drive, selezionare la casella **Abilita una test drive** . Sarà quindi necessario configurare un ambiente dimostrativo nella [configurazione tecnica di test drive](#types-of-azure-application-plans) per consentire ai clienti di provare l'offerta per un periodo di tempo fisso. 

>[!Note]
>Poiché tutte le applicazioni Azure vengono implementate usando un modello di Azure Resource Manager, l'unico tipo di test drive che può essere configurato per un applicazione Azure è un [test drive basato su Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

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

### <a name="standard-marketplace-terms-and-conditions"></a>Termini e condizioni del Marketplace standard

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

### <a name="summary"></a>summary

Fornire una breve descrizione dell'offerta (fino a 100 caratteri), che può essere usata nei risultati della ricerca nel Marketplace.

### <a name="long-summary"></a>Riepilogo lungo

Fornire una descrizione più lunga dell'offerta (fino a 256 caratteri). La descrizione può essere usata nei risultati della ricerca nel Marketplace.

### <a name="description"></a>DESCRIZIONE

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

### <a name="support-urls"></a>URL di supporto

Questa sezione consente di fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="privacy-policy-url"></a>URL informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida.

#### <a name="useful-links"></a>Collegamenti utili

Fornire documenti online supplementari facoltativi sulla soluzione.  Aggiungere altri collegamenti utili facendo clic su **+ Aggiungi un collegamento**.

### <a name="contacts"></a>Contatti

In questa sezione è necessario fornire il nome, l'indirizzo di posta elettronica e il numero di telefono per un **contatto di supporto** e un **contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e potranno essere fornite ai partner CSP.

Nella sezione **Contact Support** è necessario fornire anche l'URL di **supporto** in cui i partner CSP possono trovare il supporto per l'offerta.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione è possibile fornire logo e immagini che verranno usati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato png.

#### <a name="store-logos"></a>Logo dello Store

Fornire il logo dell'offerta in tre dimensioni: **Small (48 x 48)** , **medium (90 x 90)** e **Large (216 x 216)** .

#### <a name="hero"></a>Banner

L'immagine Hero è facoltativa. Se ne viene fornito uno, deve misurare 815 x 290 pixel.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere schermate che mostrano il funzionamento dell'offerta. È possibile aggiungere fino a cinque schermate. Tutte le schermate devono avere 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, è possibile aggiungere fino a cinque video che illustrano l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ciascuna di esse, immettere il nome del video, il relativo URL e un'immagine di anteprima del video (1280 x 720 pixel).

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

- [Procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Anteprima

La scheda **Anteprima** consente di definire un gruppo di **destinatari di anteprima** limitato per la convalida dell'offerta prima di pubblicare l'offerta in tempo reale per i destinatari più ampi del Marketplace.

> [!IMPORTANT]
> È necessario selezionare **Go Live** prima che l'offerta venga pubblicata in tempo reale per il pubblico del Marketplace dopo aver verificato l'offerta in anteprima.

I destinatari dell'anteprima sono identificati dai GUID di ID sottoscrizione di Azure, abbinati a una descrizione facoltativa per ognuno di essi.  Nessuno di questi campi è visibile ai clienti.

Aggiungere fino a 10 ID sottoscrizione di Azure manualmente o fino a 100 se si carica un file CSV.  Con l'aggiunta di queste sottoscrizioni, si definisce un gruppo di destinatari a cui verrà consentito l'accesso in anteprima all'offerta prima che sia completamente pubblicata.  Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti all'offerta.

>[!Note]
>Il pubblico di anteprima è diverso da un pubblico privato. Un pubblico di anteprima è autorizzato ad accedere all'offerta *prima* di essere pubblicata Live nei Marketplace. È anche possibile scegliere di creare un piano e renderlo disponibile solo per un pubblico privato (usando la scheda disponibilità piano).  I destinatari dell'anteprima potranno visualizzare e convalidare tutti i piani, inclusi i piani che saranno disponibili solo per un pubblico privato dopo che l'offerta è stata completamente pubblicata nel Marketplace.

## <a name="plan-overview"></a>Panoramica del piano

La scheda **panoramica piano** consente di fornire opzioni di piano diverse all'interno della stessa offerta. Questi piani (definiti SKU nel portale Cloud Partner) possono differire in termini di tipo di piano (modello di soluzione e applicazione gestita), monetizzazione o destinatari.  Configurare almeno un piano per elencare l'offerta nel Marketplace.

Una volta creati, vengono visualizzati i nomi, gli ID, il tipo di piano, la disponibilità (pubblico o privato), lo stato di pubblicazione corrente e le eventuali azioni disponibili in questa scheda.

Le **azioni** disponibili nella **Panoramica del piano** variano a seconda dello stato corrente del piano e possono includere:

* Se lo stato del piano è **bozza** -eliminazione bozza.
* Se lo stato del piano è **Live** -stop sell plan o Sync private audience.

### <a name="create-new-plan"></a>Crea nuovo piano

***ID piano*** : creare un ID piano univoco per ogni piano nell'offerta. Questo ID sarà visibile ai clienti nell'URL del prodotto.  Usare solo lettere minuscole, caratteri alfanumerici, trattini o caratteri di sottolineatura. Per questo ID piano sono consentiti al massimo 50 caratteri. Questo ID non può essere modificato dopo aver selezionato Crea.

***Nome del piano*** : i clienti visualizzeranno questo nome per decidere quale piano selezionare nell'offerta. Creare un nome di offerta univoco per ogni piano nell'offerta. Il nome del piano viene usato per distinguere i piani software che possono far parte della stessa offerta, ad esempio Nome offerta: Windows Server; piani: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Pianificare l'installazione

La scheda **programma di installazione** consente di impostare la configurazione di alto livello per il tipo di piano, se riutilizza i pacchetti da un altro piano e quali cloud deve essere disponibile nel piano.  Le risposte in questa scheda influiscono sui campi che vengono visualizzati in altre schede per lo stesso piano.

#### <a name="plan-type"></a>Tipo di piano

Come descritto nei [tipi di piani applicativi di Azure](#types-of-azure-application-plans), selezionare se il piano conterrà un modello di soluzione o un'applicazione gestita.

#### <a name="this-plan-reuses-packages"></a>Questo piano riutilizza i pacchetti

Se si dispone di più di un piano dello stesso tipo e i pacchetti sono identici, è possibile selezionare **questo piano riutilizza i pacchetti da un altro piano**.  Quando si seleziona questa opzione, sarà possibile selezionare uno degli altri piani dello stesso tipo per questa offerta per riutilizzare i pacchetti. 

>[!Note]
>Quando si riutilizzano i pacchetti di un altro piano, l'intera scheda configurazione tecnica scomparirà da questo piano.  I dettagli relativi alla configurazione tecnica dell'altro piano, inclusi gli eventuali aggiornamenti apportati in futuro, verranno utilizzati anche per questo piano. <br> <br> Inoltre, questa impostazione non può essere modificata dopo la pubblicazione del piano.

#### <a name="cloud-availability"></a>Disponibilità cloud

Questo piano deve essere reso disponibile in almeno un cloud. 

Selezionare l'opzione **pubblica di Azure** per rendere la soluzione distribuibile ai clienti in tutte le aree di Azure pubbliche con integrazione del Marketplace.  Altre informazioni sulla [disponibilità geografica](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Seleziona l'opzione **Azure Government cloud** per rendere la tua soluzione distribuibile nel [cloud di Azure per enti](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)pubblici, un cloud della community per enti pubblici con accesso controllato per i clienti degli Stati Uniti federali, statali, locali o tribali e partner idonei a soddisfare tali entità.  L'autore è responsabile di tutti i controlli di conformità, le misure di sicurezza e le procedure consigliate per gestire questa community Cloud.  Azure per enti pubblici USA Data Center e reti fisicamente isolate (situate solo negli Stati Uniti).  Prima di eseguire la pubblicazione in [Azure per enti pubblici](https://aka.ms/azuregovpublish), Microsoft consiglia di testare e convalidare la soluzione nell'ambiente in quanto determinati endpoint potrebbero essere diversi. Per organizzare e testare la soluzione, richiedere un account di valutazione da questo [collegamento](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Una volta pubblicato un piano come disponibile in un cloud specifico, il cloud non può essere rimosso.

**Certificazioni cloud di Azure per enti pubblici**

Questa opzione è visibile solo se il **cloud di Azure per enti pubblici** è selezionato in **disponibilità cloud**.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e normative governative, ad esempio FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.  Per sensibilizzare le certificazioni per questi programmi, è possibile fornire fino a 100 collegamenti che descrivono le certificazioni.  Questi collegamenti possono essere collegamenti all'inserzione diretta del programma o a collegamenti a descrizioni della conformità con tali collegamenti nei propri siti Web.  Questi collegamenti saranno visibili solo ai clienti del cloud di Azure per enti pubblici.

## <a name="plan-listing"></a>Elenco di piani

La scheda **elenco piani** Visualizza le informazioni di visualizzazione specifiche del piano che possono essere diverse tra i diversi piani per la stessa offerta.

### <a name="name"></a>Nome

Pre-popolato in base al nome assegnato al piano al momento della creazione.  Questo nome verrà visualizzato come titolo di questo "piano software" visualizzato nel Marketplace.  Può contenere un massimo di 100 caratteri.

### <a name="summary"></a>summary

Fornire un breve riepilogo del piano software.  Può contenere un massimo di 100 caratteri.

### <a name="description"></a>DESCRIZIONE

Questa descrizione è un'opportunità per spiegare cosa rende univoco questo piano software e qualsiasi differenza rispetto ad altri piani software all'interno dell'offerta. Può contenere un massimo di 2.000 caratteri.

Selezionare **Salva** dopo aver completato questi campi.

## <a name="availability"></a>Availability

La scheda **disponibilità** è visibile solo ai piani di modelli di soluzione.  Il piano può essere reso visibile a tutti, solo a specifici clienti (destinatari privati) e se rendere il piano nascosto per l'uso da parte di un altro modello di soluzione o di applicazioni gestite.

### <a name="plan-audience"></a>Pianificare i destinatari

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando gli ID sottoscrizione di Azure.

**Privacy/questo è un piano privato** (casella di controllo facoltativo): selezionare questa casella per rendere il piano privato e visibile solo per i destinatari con restrizioni a scelta. Una volta pubblicato come piano privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Una volta che un piano è stato pubblicato come visibile a tutti, deve rimanere visibile a tutti gli utenti. Il piano non può essere configurato nuovamente come piano privato.

**Destinatari con restrizioni (ID sottoscrizione di Azure)** : assegnare i destinatari che avranno accesso a questo piano privato. L'accesso viene assegnato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione di Azure assegnato. È possibile aggiungere un massimo di 10 ID sottoscrizione o 20.000 ID sottoscrizione per i clienti se si importa un file di foglio di calcolo CSV.  Gli ID sottoscrizione di Azure sono rappresentati come GUID e le lettere devono essere in lettere minuscole.

>[!Note]
>I destinatari privati (o destinatari limitati) variano a seconda del gruppo di destinatari di anteprima definito nella scheda [**Anteprima**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Un pubblico di anteprima è autorizzato ad accedere all'offerta *prima* che l'offerta venga pubblicata dal Marketplace. Sebbene la designazione dei destinatari privati venga applicata solo a un piano specifico, i destinatari di anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

### <a name="hide-plan"></a>Nascondi piano

Se il modello di soluzione deve essere distribuito solo indirettamente quando si fa riferimento a un altro modello di soluzione o a un'applicazione gestita, selezionare questa casella per pubblicare il modello di soluzione, ma nasconderlo dai clienti che eseguono ricerche e ricerche direttamente.

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

La scheda **prezzi e disponibilità** è visibile solo ai piani di applicazioni gestite.  È possibile configurare i mercati in cui sarà disponibile questo piano, il prezzo al mese della gestione della soluzione e se rendere il piano visibile a tutti o solo a specifici clienti (destinatari privati).

### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per qualsiasi località di mercato in cui si desidera rendere disponibile questo piano. Una casella di ricerca e un pulsante per la selezione dei paesi in cui vengono riportate le imposte, in cui Microsoft si impegnano le vendite e le imposte per conto dell'utente, vengono fornite informazioni utili.

Se sono già stati impostati i prezzi per il piano in Stati Uniti dollari (USD) e si aggiunge un altro percorso di mercato, il prezzo del nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Rivedere sempre il prezzo di ogni mercato prima della pubblicazione. I prezzi possono essere esaminati tramite il collegamento "Esporta prezzi (xlsx)" dopo aver salvato le modifiche.

### <a name="pricing"></a>Prezzi

Fornire il prezzo per mese per questo piano.  Questo prezzo è in aggiunta a qualsiasi infrastruttura di Azure o a costi software con pagamento in base al consumo sostenuti dalle risorse distribuite da questa soluzione.

I prezzi impostati nella valuta locale (USD = Stati Uniti dollaro) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti disponibili durante l'installazione. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e esaminando il prezzo in ogni mercato. Se si desidera impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi. 

>[!Note]
>È innanzitutto necessario salvare le modifiche ai prezzi per consentire l'esportazione dei dati dei prezzi.

Esaminare attentamente i prezzi prima della pubblicazione, in quanto esistono alcune limitazioni relative a ciò che può essere modificato dopo la pubblicazione di un piano.  

>[!Note]
>Una volta pubblicato il prezzo per un mercato nel piano, non è possibile modificarlo in un secondo momento.

### <a name="plan-audience"></a>Pianificare i destinatari

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando gli ID sottoscrizione di Azure.

**Privacy/questo è un piano privato** (casella di controllo facoltativo): selezionare questa casella per rendere il piano privato e visibile solo per i destinatari con restrizioni a scelta. Una volta pubblicato come piano privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Una volta che un piano è stato pubblicato come visibile a tutti, deve rimanere visibile a tutti gli utenti. Il piano non può essere configurato nuovamente come piano privato.

**Destinatari con restrizioni (ID sottoscrizione di Azure)** : assegnare i destinatari che avranno accesso a questo piano privato. L'accesso viene assegnato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione di Azure assegnato. È possibile aggiungere un massimo di 10 ID sottoscrizione o 20.000 ID sottoscrizione per i clienti se si importa un file di foglio di calcolo CSV.  Gli ID sottoscrizione di Azure sono rappresentati come GUID e le lettere devono essere in lettere minuscole.

>[!Note]
>I destinatari privati (o destinatari limitati) variano a seconda del gruppo di destinatari di anteprima definito nella scheda [**Anteprima**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Un pubblico di anteprima è autorizzato ad accedere all'offerta *prima* che l'offerta venga pubblicata dal Marketplace. Sebbene la designazione dei destinatari privati venga applicata solo a un piano specifico, i destinatari di anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

## <a name="technical-configuration"></a>Configurazione tecnica 

La scheda **configurazione tecnica** consente di caricare il pacchetto di distribuzione che consentirà ai clienti di distribuire il piano.

>[!Note]
>Questa scheda non sarà visibile se il piano è stato configurato per riutilizzare i pacchetti di un altro piano nella scheda **Imposta piano** .

### <a name="package-details"></a>Dettagli del pacchetto

I **Dettagli del pacchetto** sottoscheda consentono di modificare la versione bozza della configurazione tecnica.

***Versione*** : assegnare la versione corrente della configurazione tecnica.  Incrementare questa versione ogni volta che si pubblica una modifica in questa pagina. Il formato della versione deve essere `{integer}.{integer}.{integer}`.

***File del pacchetto*** (`.zip`): questo pacchetto contiene tutti i file di modello necessari per questo piano, nonché eventuali risorse aggiuntive, incluse in un file di `.zip`.

Tutti i pacchetti del piano dell'applicazione Azure devono includere questi due file nella cartella radice di un archivio `.zip`:

* Un file di modello Gestione risorse denominato [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Questo modello automatizza la distribuzione delle risorse nella sottoscrizione di Azure Customers.  Per esempi di modelli di Gestione risorse, vedere la [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) o il repository di modelli di [avvio rapido GitHub: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) corrispondente.

* Definizione dell'interfaccia utente per l'esperienza di creazione di applicazioni Azure denominata [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Tutte le nuove offerte di applicazioni Azure devono includere anche un GUID di [attribuzione dell'utilizzo dei clienti partner di Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) .

### <a name="previously-published-packages"></a>Pacchetti pubblicati in precedenza 

I **pacchetti pubblicati in precedenza** sottoscheda consentono di visualizzare tutte le versioni pubblicate della configurazione tecnica.

## <a name="technical-configuration-managed-application-plans-only"></a>Configurazione tecnica (solo piani di applicazioni gestite)

I piani di applicazioni gestite presentano maggiore complessità nella scheda **configurazione tecnica** oltre ai campi **versione** e **file di pacchetto** descritti in precedenza. 

### <a name="enable-just-in-time-jit-access"></a>Abilitare l'accesso just-in-time (JIT)

Selezionare questa opzione per abilitare l'accesso JIT (just-in-Time) per questo piano.  L'accesso JIT consente di richiedere l'accesso con privilegi elevati alle risorse di un'applicazione gestita per la risoluzione dei problemi o la manutenzione. Si ha sempre accesso in sola lettura alle risorse, ma per un periodo di tempo specifico è possibile avere un accesso maggiore.  Per altre informazioni, vedere [abilitare e richiedere l'accesso JIT (just-in-Time) per le applicazioni gestite di Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Per richiedere agli utenti dell'applicazione gestita di concedere l'accesso permanente all'account, lasciare deselezionata questa opzione.

>[!Note]
>Assicurarsi di aggiornare il file di `createUiDefinition.json` per supportare questa funzionalità.  

### <a name="deployment-mode"></a>Modalità di distribuzione

Consente di scegliere se configurare la modalità di distribuzione **completa** o **incrementale** durante la distribuzione del piano: 

* In **modalità completa**, una ridistribuzione dell'applicazione da parte del cliente comporterà la rimozione delle risorse nel gruppo di risorse gestite se le risorse non sono definite nel `mainTemplate.json`. 
* In **modalità incrementale**, una ridistribuzione dell'applicazione lascia invariate le risorse esistenti.

Per altre informazioni sulle modalità di distribuzione, vedere [Azure Resource Manager modalità di distribuzione](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>URL dell'endpoint di notifica

Specificare un endpoint del webhook HTTPS per ricevere le notifiche relative a tutte le operazioni CRUD nelle istanze di applicazioni gestite di questa versione del piano.

### <a name="customize-allowed-customer-actions"></a>Personalizzare le azioni dei clienti consentite

Selezionare questa opzione per specificare le azioni che i clienti possono eseguire sulle risorse gestite, oltre alle azioni "`*/read`" disponibili per impostazione predefinita. 

Elencare le azioni aggiuntive che si desidera consentire al cliente di eseguire qui, separate da punti e virgola.  Per altre informazioni, vedere informazioni sulle [assegnazioni Deny per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Per informazioni sulle azioni disponibili, vedere [Operazioni di provider di risorse con Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Ad esempio, per consentire agli utenti di riavviare le macchine virtuali, aggiungere `Microsoft.Compute/virtualMachines/restart/action` alle azioni consentite.

### <a name="global-azure--azure-government-cloud"></a>Azure globale/Cloud Azure per enti pubblici

Indicare chi deve avere l'accesso di gestione a questa applicazione gestita in ogni cloud supportato.  Gli utenti, i gruppi o le applicazioni a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite vengono identificati usando le identità Azure Active Directory (AAD).

***Azure Active Directory ID tenant*** : l'ID tenant di AAD (noto anche come ID directory) contenente le identità degli utenti, dei gruppi o delle applicazioni a cui si vogliono concedere le autorizzazioni.  È possibile trovare l'ID tenant di AAD nella portale di Azure, in [proprietà per Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autorizzazioni*** : aggiungere il Azure Active Directory ID oggetto dell'utente, del gruppo o dell'applicazione a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite. Identificare l'utente in base al relativo ID principale, disponibile nel pannello [Azure Active Directory utenti del portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Per ogni entità, selezionare uno dei Azure AD ruoli predefiniti dall'elenco (proprietario o collaboratore). Il ruolo selezionato descrive le autorizzazioni che l'entità avrà sulle risorse nella sottoscrizione del cliente. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Per ulteriori informazioni sul controllo degli accessi in base al ruolo (RBAC), vedere [Introduzione a RBAC nel portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Sebbene sia possibile aggiungere fino a 100 autorizzazioni per cloud, è in genere più semplice creare un gruppo di utenti Active Directory e specificarne l'ID nell'"ID entità".  In questo modo sarà possibile aggiungere altri utenti al gruppo di gestione dopo la distribuzione del piano e ridurre la necessità di aggiornare il piano solo per aggiungere altre autorizzazioni.

### <a name="policy-settings"></a>Impostazioni dei criteri

Applicare i [criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview) all'applicazione gestita per specificare i requisiti di conformità per la soluzione distribuita.  Per le definizioni dei criteri e il formato dei valori dei parametri, vedere [Esempi di criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).  È possibile configurare un massimo di cinque criteri e una sola istanza di ogni opzione di criteri.  Alcuni criteri richiedono parametri aggiuntivi.  Lo SKU Standard è necessario per i criteri di controllo.  Il nome del criterio è limitato a 50 caratteri.

## <a name="co-sell"></a>Co-selling

Fornire informazioni sulla scheda Cosell è interamente facoltativo per la pubblicazione dell'offerta. È necessario per ottenere lo stato pronto per la co-selling e per il co-selling IP. Le informazioni fornite verranno utilizzate dai team di vendita Microsoft per ottenere ulteriori informazioni sulla soluzione durante la valutazione dell'idoneità alle esigenze dei clienti. Non è disponibile direttamente per i clienti.

Per altre informazioni sul completamento di questa scheda, vedere [opzione di co-selling nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Test drive

La scheda **test drive** consente di configurare una dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di eseguire il commit per acquistarla. Per altre informazioni, vedere l'articolo [che cos'è test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina **impostazione offerta** e deselezionare **Abilita Test Drive**.

### <a name="technical-configuration"></a>Configurazione tecnica

Le applicazioni Azure usano intrinsecamente il tipo di test drive Azure Resource Manager.  Per ulteriori informazioni, vedere [la pagina relativa alla configurazione tecnica per Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) .

### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione della distribuzione

Per distribuire il test drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca. (Non necessario per Power BI test drive).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile [creare una sottoscrizione di Azure separata](https://docs.microsoft.com/azure/billing/billing-create-subscription) da usare per le unità di test se non ne è già presente uno. È possibile trovare l'ID sottoscrizione di Azure accedendo al [portale di Azure](https://portal.azure.com/) e passando alla scheda **sottoscrizioni** del menu a sinistra. Selezionando la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5AB6-6789-1h234g764ghty".

- **Azure ad ID tenant** (obbligatorio): immettere l' [id tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare * * proprietà, quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile cercare l'ID tenant dell'organizzazione usando l'URL del nome di dominio all'indirizzo: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Azure ad nome del tenant** (obbligatorio per la 365 dinamica): immettere il nome del Azure Active Directory (ad). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/), nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.

- **Azure ad ID app** (obbligatorio): immettere l' [id applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).

- **Azure ad segreto client App** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu a sinistra, selezionare **registrazioni app**, quindi selezionare l'app test drive. Selezionare quindi **certificati e segreti**, fare clic **su nuovo segreto client**, immettere una descrizione, selezionare **mai** sotto **scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. Non uscire dalla pagina prima di copiare il valore, altrimenti non sarà possibile accedere al valore.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

### <a name="test-drive-listings-optional"></a>Elenchi di test drive (facoltativo)

L'opzione per gli **elenchi di test drive** disponibile nella scheda **test drive** Visualizza le lingue (e i mercati) in cui è disponibile la test drive, attualmente in inglese (Stati Uniti) è l'unica posizione disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e della data/ora in cui è stato aggiunto. È necessario definire i dettagli del test drive (descrizione, manuale dell'utente, video e così via) per ogni lingua/mercato.

- **Descrizione** (obbligatoria): descrivere la test drive, gli elementi che verranno illustrati, gli obiettivi dell'utente per sperimentare, le funzionalità da esplorare e tutte le informazioni rilevanti per aiutare l'utente a determinare se acquisire l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

- **Informazioni di accesso** (obbligatorie per Azure Resource Manager e unità di test per la logica): spiegare cosa è necessario sapere al cliente per accedere a questo test drive e usarlo. Esaminare uno scenario per l'uso dell'offerta e esattamente ciò che il cliente deve conoscere per accedere alle funzionalità nell'test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale dell'utente** (obbligatorio): una procedura dettagliata approfondita dell'esperienza test drive. Il manuale dell'utente dovrebbe coprire esattamente ciò che si vuole che il cliente possa ottenere dall'esperienza del test drive e fungere da riferimento per eventuali domande. Il file deve essere in formato PDF ed essere denominato (255 caratteri al massimo) dopo il caricamento.

- **Video: aggiungere video** (facoltativo): è possibile caricare video in YouTube o Vimeo e farvi riferimento con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una procedura dettagliata per comprendere meglio le test drive, tra cui come usare correttamente le funzionalità dell'offerta e comprendere gli scenari in cui vengono evidenziati i vantaggi.
  - **Nome** (obbligatorio)
  - **URL (solo YouTube o Vimeo)** (obbligatorio)
  - **Anteprima (533 x 324 px)** : il file di immagine deve essere in formato png.

Selezionare **Salva** dopo aver completato questi campi.

## <a name="publish"></a>Publish

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Una volta completate tutte le sezioni obbligatorie dell'offerta, selezionare **pubblica** nell'angolo superiore destro del portale. Si verrà reindirizzati alla pagina di **revisione e pubblicazione** . 

Se è la prima volta che si pubblica questa offerta, è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta.
    - *Non avviato* : indica che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o che sono necessarie altre informazioni. Tornare alla sezione o aggiornarla.
    - *Complete* : indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Verrà inviato un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta al pubblico (o se un'offerta privata, per i destinatari privati).

### <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

Il passaggio di **convalida manuale** del processo di pubblicazione rappresenta un'ampia revisione dell'offerta e delle risorse tecniche associate (in particolare il modello di Azure Resource Manager), i problemi vengono in genere presentati come collegamenti richiesta pull (PR). Per una spiegazione su come visualizzare e rispondere a queste richieste pull, vedere [Gestione del feedback di revisione](./azure-apps-review-feedback.md).

Se si verificano errori in uno o più passaggi di pubblicazione, è necessario correggerli e ripubblicare l'offerta.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
