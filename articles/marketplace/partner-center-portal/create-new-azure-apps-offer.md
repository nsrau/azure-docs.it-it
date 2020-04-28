---
title: Creare una nuova offerta di app di Azure nel marketplace commercialeCreate a new Azure Apps offer in the commerciale marketplace
description: Come creare una nuova offerta di app di Azure per l'elenco o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) tramite il portale Commercial Marketplace nel Centro per i partner Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 516ebce176f53a0495ea493f5327658162e7ea9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867277"
---
# <a name="create-an-azure-application-offer"></a>Creare un'offerta per un'applicazione di Azure

I passaggi per la pubblicazione di un'offerta di applicazione azure nel marketplace commerciale sono descritti di seguito.

## <a name="azure-application-offer-type"></a>Tipo di offerta dell'applicazione AzureAzure application offer type

Questo argomento illustra le nozioni di base sulle offerte delle applicazioni di Azure.This topic outlines fundamentals about Azure application offers.  È necessario avere familiarità con questi concetti prima di iniziare il processo di pubblicazione di una nuova offerta di applicazione Azure nel Marketplace.You should be familiar with these concepts before starting the process of publishing a new Azure application offer in the Marketplace.

### <a name="publishing-overview"></a>Panoramica della pubblicazione

Il video Creazione di modelli di [soluzione e applicazioni gestite per Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) è un'introduzione al tipo di offerta dell'applicazione Azure:The video Building Solution Templates, and Managed Applications for the Azure Marketplace is an introduction to the Azure application offer type:

* Quali tipi di offerta sono disponibili;
* Quali sono le risorse tecniche necessarie;
* Come creare un modello di Azure Resource Manager.
* Sviluppo e test dell'interfaccia utente dell'app;
* Come pubblicare l'offerta dell'app;
* Processo di revisione dell'applicazione.

### <a name="types-of-azure-application-plans"></a>Tipi di piani delle applicazioni di AzureTypes of Azure application plans

Esistono due tipi di piani di applicazione di Azure, applicazioni gestite e modelli di soluzione.

* **Modello** di soluzione è uno dei modi principali per pubblicare una soluzione nel Marketplace. Questo tipo di piano viene usato quando la soluzione richiede ulteriore automazione di distribuzione e configurazione oltre a una singola macchina virtuale (VM).  Con un modello di soluzione, è possibile automatizzare la fornitura di più risorse, tra cui macchine virtuali, rete e risorse di archiviazione per fornire soluzioni IaaS complesse.  Per altre informazioni sulla creazione di modelli di soluzione, vedere la documentazione di [Azure Resource Manager.For](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) more information about building solution templates, see the Azure Resource Manager documentation.

* **L'applicazione gestita** è simile ai modelli di soluzione, con una differenza fondamentale. In un'applicazione gestita le risorse vengono distribuite in un gruppo di risorse gestito dall'entità di pubblicazione dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. In qualità di entità di pubblicazione dell'app, è possibile specificare il costo del supporto continuativo per la soluzione. Utilizza le applicazioni gestite per creare e distribuire facilmente applicazioni chiavi in mano completamente gestite ai tuoi clienti.  Per altre informazioni sui vantaggi e sui tipi di applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](https://docs.microsoft.com/azure/managed-applications/overview).

Tutte le applicazioni di Azure includono almeno `.zip` due file nella cartella radice di un archivio:All Azure applications include at least two files in the root folder of a archive:

* Un file modello di Resource Manager denominato [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Questo è il modello che definisce le risorse da distribuire nella sottoscrizione di Azure del cliente.  Per esempi di modelli di Resource Manager, vedere la raccolta di modelli di avvio rapido di Azure o il corrispondente repository GitHub: Modelli di avvio rapido di Azure Resource Manager.For examples of Resource Manager templates, see the [Azure Quickstart Templates gallery](https://azure.microsoft.com/resources/templates/) or the corresponding [GitHub: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) repo.

* Definizione dell'interfaccia utente per l'esperienza di creazione dell'applicazione Azure denominata [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Nell'interfaccia utente specificare gli elementi che consentono ai consumer di fornire i valori dei parametri.

Tutte le nuove offerte di applicazioni di Azure devono includere un GUID di [attribuzione dell'utilizzo dei clienti dei partner di Azure.](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)

### <a name="before-you-begin"></a>Prima di iniziare

Rivedere la documentazione seguente relativa all'applicazione di Azure, che include argomenti di avvio rapido, esercitazioni ed esempi.

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

### <a name="fundamentals-in-technical-knowledge"></a>Fondamenti nella conoscenza tecnica

La progettazione, la compilazione e il test di queste risorse richiedono tempo e conoscenze tecniche sia della piattaforma di Azure che delle tecnologie usate per creare l'offerta.

Il team tecnico deve avere il livello di conoscenza delle tecnologie Microsoft seguenti:

* Informazioni di base sui servizi di [Azure](https://azure.microsoft.com/services/).
* Come [progettare e progettare le applicazioni di Azure](https://azure.microsoft.com/solutions/architecture/).
* Conoscenza di [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), Archiviazione di [Azure](https://azure.microsoft.com/services/?filter=storage#storage)e Rete [di Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Conoscenza operativa di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Conoscenza di lavoro di [JSON](https://www.json.org/).

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

È possibile esaminare gli strumenti disponibili nella pagina Strumenti di sviluppo di [Azure.You](https://azure.microsoft.com/tools/) can review the available tools in the Azure Developer Tools page.  Inoltre, se si utilizza Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Creare un'offerta per un'applicazione di Azure

Prima di poter creare un'offerta per le applicazioni di Azure, è necessario [creare un account del Centro per](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) i partner e aprire il dashboard del marketplace [commerciale,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)con la scheda **Panoramica** selezionata.

>[!Note]
>Una volta pubblicata un'offerta, le modifiche apportate all'offerta effettuata nel Centro per i partner verranno aggiornate nel sistema e nelle vetrine dopo la ripubblicazione.  Assicurati di inviare l'offerta per la pubblicazione dopo aver apportato le modifiche.

### <a name="create-a-new-offer"></a>Creare una nuova offerta

Selezionare il pulsante Nuova offerta, quindi selezionare la voce di menu **Applicazione di Azure.Select** the **"New offer** button, then select the Azure Application menu item item. Verrà visualizzata la finestra di dialogo **Nuova offerta.**

### <a name="offer-id-and-alias"></a>ID e alias dell'offerta

* **ID offerta**: Un identificatore univoco per ogni offerta nel tuo account. Questo ID sarà visibile ai clienti nell'indirizzo URL dell'offerta del marketplace e nei modelli di Azure Resource Manager (se applicabile). <br> <br> L'ID offerta deve essere carattere alfanumerico minuscolo (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti). È limitato a 50 caratteri e non può essere modificato dopo aver selezionato Crea. <br> <br> Ad esempio, se `test-offer-1` si immette qui, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`l'URL dell'offerta sarà . 

* **Alias dell'offerta:** nome utilizzato per fare riferimento all'offerta all'interno del Centro per i partner. Questo nome non verrà utilizzato nel marketplace ed è diverso dal nome dell'offerta e da altri valori che verranno mostrati ai clienti. Questo valore non può essere modificato dopo aver selezionato **Crea**.

Una volta inseriti **l'ID offerta** e l'alias **dell'offerta,** selezionare **Crea**. Potrai quindi lavorare su tutte le altre parti della tua offerta.

## <a name="offer-setup"></a>Configurazione dell'offerta

Nella pagina **Impostazione offerta** vengono richieste le seguenti informazioni. Assicurarsi di selezionare **Salva** dopo aver completato questi campi.

### <a name="test-drive"></a>Test drive

Un test drive è un ottimo modo per mostrare la tua offerta ai potenziali clienti dando loro la possibilità di "provare prima di acquistare", con conseguente aumento della conversione e la generazione di lead altamente qualificati. [Ulteriori informazioni sui test drive.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Per abilitare un'unità di test, selezionare la casella **Abilita un'unità** di test. Sarà quindi necessario configurare un ambiente dimostrativo nella [configurazione tecnica](#types-of-azure-application-plans) del test drive per consentire ai clienti di provare l'offerta per un periodo di tempo fisso. 

>[!Note]
>Poiché tutte le applicazioni di Azure vengono implementate usando un modello di Azure Resource Manager, l'unico tipo di test drive che può essere configurato per un'applicazione Azure è un test [drive basato su Azure Resource Manager.Because](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)all Azure applications are implemented using an Azure Resource Manager template, the only type of test drive that can be configured for an Azure Application is an Azure Resource Manager based test drive .

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

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per il mercato commerciale Microsoft

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Inserzione dell'offerta

La pagina Dell'offerta mostra le lingue in cui verrà elencata l'offerta. Attualmente, **English (Stati Uniti)** è l'unica opzione disponibile.

Dovrai definire i dettagli del marketplace (nome dell'offerta, descrizione, immagini, ecc.) per ogni lingua/mercato. Selezionare la lingua/nome del mercato per fornire queste informazioni.

> [!NOTE]
> Il contenuto dell'elenco dell'offerta (come la descrizione, i documenti, le schermate, le condizioni per l'utilizzo, ecc.) non è necessario per essere in inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non in lingua inglese]". È inoltre accettabile fornire un *URL di collegamento utile* per offrire contenuti in una lingua diversa da quella utilizzata nel contenuto della scheda Offerta.

### <a name="name"></a>Nome

Il nome che inserisci qui verrà mostrato ai clienti come titolo della tua offerta. Questo campo viene prepopolato con il testo immesso per **l'alias dell'offerta** al momento della creazione dell'offerta, ma è possibile modificare questo valore. Questo nome può essere registrato (e l'utente può includere simboli di marchio o copyright). Il nome non può contenere più di 50 caratteri e non può includere emoji.

### <a name="summary"></a>Summary

Fornisci una breve descrizione dell'offerta (fino a 100 caratteri), che può essere utilizzata nei risultati di ricerca del marketplace.

### <a name="long-summary"></a>Riassunto lungo

Fornisci una descrizione più lunga della tua offerta (fino a 256 caratteri). La descrizione può essere utilizzata nei risultati di ricerca del marketplace.

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

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Facoltativamente, puoi inserire fino a tre parole chiave di ricerca per aiutare i clienti a trovare la tua offerta nel marketplace. Per ottenere risultati ottimali, prova a utilizzare queste parole chiave anche nella tua descrizione.

### <a name="support-urls"></a>URL di supporto

Questa sezione ti consente di fornire link per aiutare i clienti a comprendere meglio la tua offerta.

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'app sia conforme alle leggi e ai regolamenti sulla privacy e di fornire una valida informativa sulla privacy.

#### <a name="useful-links"></a>Collegamenti utili

Fornire documenti online supplementari facoltativi sulla soluzione.  Aggiungere ulteriori collegamenti utili facendo clic su **Aggiungi un collegamento**.

### <a name="contacts"></a>Contatti

In questa sezione è necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono di un **contatto di supporto** e di un contatto **Engineering**. Queste informazioni non vengono mostrate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP.

Nella sezione **Contatto supporto** è inoltre necessario fornire l'URL del **supporto** in cui i partner CSP possono trovare supporto per l'offerta.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione, è possibile fornire loghi e immagini che verranno utilizzati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato PNG.

>[!Note]
>Se si verifica un problema durante il caricamento dei https://upload.xboxlive.com file, assicurarsi che la rete locale non blocchi il servizio utilizzato dal Centro per i partner.

#### <a name="store-logos"></a>Loghi del Negozio

Fornisci il logo della tua offerta in tre dimensioni: **Piccolo (48 x 48),** **Medio (90 x 90)** e **Grande (216 x 216)**.

#### <a name="hero"></a>Banner

L'immagine dell'eroe è facoltativa. Se ne fornisci uno, deve misurare 815 x 290 pixel.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungi screenshot che mostrano come funziona la tua offerta. Puoi aggiungere fino a cinque screenshot. Tutte le schermate devono essere 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, puoi aggiungere fino a cinque video che dimostrano la tua offerta. Questi video devono essere ospitati su YouTube e/o Vimeo. Per ognuno di essi, inserisci il nome del video, il suo URL e un'immagine di anteprima del video (1280 x 720 pixel).

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione del marketplace

- [Procedure consigliate per le inserzioni con offerte di marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Anteprima

La scheda **Anteprima** ti consente di definire un pubblico di **anteprima** limitato per convalidare la tua offerta prima di pubblicare la tua offerta in diretta al pubblico del marketplace più ampio.

> [!IMPORTANT]
> Devi selezionare **Vai in diretta** prima che l'offerta venga pubblicata in diretta per il pubblico del marketplace dopo aver controllato l'offerta in Anteprima.

Il gruppo di destinatari di anteprima è identificato dai GUID dell'ID sottoscrizione di Azure, associati a una descrizione facoltativa per ognuno di essi.  Nessuno di questi campi è visibile ai clienti.

Aggiungere fino a 10 ID sottoscrizione di Azure manualmente o fino a 100 se si carica un file CSV.  Aggiungendo queste sottoscrizioni, si definisce un gruppo di destinatari a cui sarà consentito l'accesso in anteprima all'offerta prima che venga pubblicata completamente.  Se la tua offerta è già attiva, puoi comunque definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti alla tua offerta.

>[!Note]
>Il pubblico di anteprima è diverso da un pubblico privato. Un pubblico di anteprima può accedere alla tua offerta *prima* di essere pubblicato in diretta nei marketplace. Puoi anche scegliere di creare un piano e renderlo disponibile solo a un pubblico privato (utilizzando la scheda Disponibilità del piano).  Il pubblico di anteprima sarà in grado di visualizzare e convalidare tutti i piani, inclusi i piani che saranno disponibili solo per un pubblico privato una volta che l'offerta sarà completamente pubblicata sul marketplace.

## <a name="plan-overview"></a>Panoramica del piano

La scheda **Panoramica piano** consente di fornire diverse opzioni di piano all'interno della stessa offerta. Questi piani (denominati SKU nel portale Cloud Partner) possono differire in termini di tipo di piano (modello di soluzione e applicazione gestita), monetizzazione o pubblico.  Configurare almeno un piano per elencare l'offerta nel marketplace.

Una volta creati, vedrai i nomi del piano, gli ID, il tipo di piano, la disponibilità (Pubblico o Privato), lo stato di pubblicazione corrente e tutte le azioni disponibili in questa scheda.

**Le azioni** disponibili nella **panoramica** del piano variano a seconda dello stato corrente del piano e possono includere:

* Se lo stato del piano è **Bozza** - Elimina bozza.
* Se lo stato del piano è **In diretta** – Interrompi vendita piano o Sincronizza pubblico privato.

### <a name="create-new-plan"></a>Crea nuovo piano

***ID piano:*** consente di creare un ID piano univoco per ogni piano dell'offerta. Questo ID sarà visibile ai clienti nell'URL del prodotto.  Utilizzare solo caratteri minuscoli, alfanumerici, trattini o caratteri di sottolineatura. Per questo ID piano sono consentiti al massimo 50 caratteri. Questo ID non può essere modificato dopo aver selezionato create.

***Nome del piano*** - I clienti vedranno questo nome quando decidono quale piano selezionare all'interno dell'offerta. Crea un nome di offerta univoco per ogni piano in questa offerta. Il nome del piano viene utilizzato per differenziare i piani software che possono far parte della stessa offerta (ad es. Nome dell'offerta: Windows Server; piani: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Pianificare l'installazione

La scheda **Impostazione piano** consente di impostare la configurazione di alto livello per il tipo di piano, se riutilizza i pacchetti di un altro piano e in quali cloud il piano deve essere disponibile.  Le risposte in questa scheda influenzeranno i campi visualizzati in altre schede per lo stesso piano.

#### <a name="plan-type"></a>Tipo di piano

Come descritto in [Tipi di piani dell'applicazione di Azure](#types-of-azure-application-plans), selezionare se il piano conterrà un modello di soluzione o un'applicazione gestita.

#### <a name="this-plan-reuses-packages"></a>Questo piano riutilizza i pacchetti

Se si dispone di più planimetrie dello stesso tipo e i pacchetti sono identici tra di essi, è possibile selezionare **questo piano riutilizza i pacchetti di un altro piano.**  Quando si seleziona questa opzione, sarà possibile selezionare uno degli altri piani dello stesso tipo da cui riutilizzare i pacchetti. 

>[!Note]
>Quando si riutilizzano i pacchetti di un altro piano, l'intera scheda Configurazione tecnica scomparirà da questo piano.  I dettagli di configurazione tecnica dell'altro piano, inclusi gli aggiornamenti eseguiti in futuro, verranno utilizzati anche per questo piano. <br> <br> Inoltre, questa impostazione non può essere modificata dopo la pubblicazione del piano.

#### <a name="cloud-availability"></a>Disponibilità del cloud

Questo piano deve essere reso disponibile in almeno un cloud. 

Selezionare l'opzione Azure pubblico per rendere la soluzione distribuibile ai clienti in tutte le aree di Azure pubbliche con l'integrazione del Marketplace.Select the **Public Azure** option to make your solution deployable to customers in all public Azure regions that have Marketplace integration.  Ulteriori informazioni sulla [disponibilità geografica](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selezionare l'opzione **Cloud di Azure per** rendere la soluzione distribuibile in Azure Government [Cloud,](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)un cloud della comunità governativa con accesso controllato per i clienti della Fascia federale, statale, locale o tribale degli Stati Uniti e dei partner idonei a servire queste entità.  L'utente, in qualità di editore, è responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate per servire questa community cloud.  Azure per enti pubblici usa data center e reti fisicamente isolati (solo negli Stati Uniti).  Prima della pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft consiglia di testare e convalidare la soluzione nell'ambiente in quanto determinati endpoint potrebbero essere diversi. Per eseguire la fase e il test della soluzione, richiedere un account di prova da questo [collegamento.](https://azure.microsoft.com/global-infrastructure/government/request/)

>[!Note]
>Una volta che un piano viene pubblicato come disponibile in un cloud specifico, tale cloud non può essere rimosso.

**Certificazioni cloud di Azure per enti pubblici**

Questa opzione è visibile solo se in **Disponibilità cloud**è selezionata **l'opzione Azure Government Cloud** .

I servizi di Azure per enti pubblici gestiscono i dati soggetti a determinate normative e requisiti governativi, ad esempio FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.  Per sensibilizzare le certificazioni per questi programmi, è possibile fornire fino a 100 link che descrivono le certificazioni.  Questi link possono essere direttamente link al tuo annuncio sul programma o un link a descrizioni della tua conformità con loro sui tuoi siti web.  Questi collegamenti saranno visibili solo ai clienti di Cloud per enti pubblici di Azure.These links will only be visible to Azure Government Cloud customers.

## <a name="plan-listing"></a>Pianificare l'inserzione

Nella scheda **Elenco dei piani** vengono visualizzate le informazioni sull'elenco specifiche del piano che possono essere diverse tra i diversi piani per la stessa offerta.

### <a name="name"></a>Nome

Precompilato in base al tuo nome a cui hai assegnato il piano al momento della creazione.  Questo nome verrà visualizzato come titolo di questo "Piano software" visualizzato nel marketplace.  Può contenere fino a 100 caratteri.

### <a name="summary"></a>Summary

Fornire un breve riepilogo del piano software.  Può contenere fino a 100 caratteri.

### <a name="description"></a>Descrizione

Questa descrizione è un'opportunità per spiegare ciò che rende questo piano software unico e tutte le differenze da altri piani software all'interno della vostra offerta. Può contenere fino a 2.000 caratteri.

Selezionare **Salva** dopo aver completato questi campi.

## <a name="availability"></a>Disponibilità

La scheda **Disponibilità** è visibile solo ai piani del modello di soluzione.  È possibile rendere il piano visibile a tutti, solo a clienti specifici (un pubblico privato) e se rendere il piano nascosto per l'utilizzo da parte di altri modelli di soluzione o solo applicazioni gestite.

### <a name="plan-audience"></a>Pianificare il pubblico

Hai la possibilità di configurare ogni piano per essere visibile a tutti o solo a un pubblico specifico di tua scelta. È possibile assegnare l'appartenenza a questo gruppo di destinatari con restrizioni usando gli ID sottoscrizione di Azure.You can assign membership in this restricted audience using Azure subscription IDs.

**Privacy / Questo è un piano privato** (casella di controllo opzionale) - Seleziona questa casella per rendere il tuo piano privato e visibile solo al pubblico limitato di tua scelta. Una volta pubblicato come piano privato, puoi aggiornare il pubblico o scegliere di rendere il piano disponibile a tutti. Una volta che un piano è pubblicato come visibile a tutti, deve rimanere visibile a tutti. (Il piano non può essere configurato nuovamente come piano privato).

**Destinatari con restrizioni (ID sottoscrizione di Azure):** assegnare il gruppo di destinatari che avrà accesso a questo piano privato. All'accesso viene assegnato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione di Azure assegnato. È possibile aggiungere un massimo di 10 ID sottoscrizione o 20.000 ID sottoscrizione clienti se si importa un file di foglio di calcolo CON estensione csv.  Gli ID sottoscrizione di Azure sono rappresentati come GUID e le lettere devono essere minuscole.

>[!Note]
>Il pubblico privato (o pubblico con restrizioni) è diverso dal pubblico di anteprima definito nella scheda [**Anteprima.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Un pubblico di anteprima può accedere alla tua offerta *prima* che l'offerta venga pubblicata dal vivo nel marketplace. Mentre la designazione del pubblico privato si applica solo a un piano specifico, il pubblico di anteprima può visualizzare tutti i piani (privati o meno) ascopo di convalida.

### <a name="hide-plan"></a>Nascondi piano

Se il modello di soluzione deve essere distribuito solo indirettamente quando viene fatto riferimento tramite un altro modello di soluzione o un'altra applicazione gestita, selezionare questa casella per pubblicare il modello di soluzione ma nasconderlo ai clienti per cercarlo e cercarlo direttamente.

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

La scheda **Prezzi e disponibilità** è visibile solo ai piani dell'applicazione gestita.  È possibile configurare i mercati in cui questo piano sarà disponibile, il prezzo al mese della gestione della soluzione e se rendere il piano visibile a tutti o solo a clienti specifici (un pubblico privato).

### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Seleziona la casella di controllo per qualsiasi località di mercato in cui desideri rendere disponibile questo piano. Una casella di ricerca e un pulsante per la selezione dei paesi "Tassa rimessi", in cui Microsoft rimette le vendite e l'imposta sull'utente per conto dell'utente, sono inclusi.

Se hai già impostato i prezzi per il tuo piano in Dollari USA (USD) e aggiungi un'altra posizione di mercato, il prezzo per il nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Controlla sempre il prezzo per ogni mercato prima della pubblicazione. I prezzi possono essere rivisti utilizzando il collegamento "Prezzi all'esportazione (xlsx)" dopo aver salvato le modifiche.

### <a name="pricing"></a>Prezzi

Fornire il prezzo mensile per questo piano.  Questo prezzo si aggiunge a qualsiasi infrastruttura di Azure o costi software con pagamento in base al costo sostenuti dalle risorse distribuite da questa soluzione.

I prezzi impostati in USD (USD - Dollaro degli Stati Uniti) vengono convertiti nella valuta locale di tutti i mercati selezionati utilizzando i tassi di cambio correnti al momento del salvataggio. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e rivedendo il prezzo in ogni mercato. Se si desidera impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi. 

>[!Note]
>È innanzitutto necessario salvare le modifiche dei prezzi per consentire l'esportazione dei dati sui prezzi.

Rivedi attentamente i tuoi prezzi prima della pubblicazione, poiché ci sono alcune restrizioni su ciò che può cambiare dopo la pubblicazione di un piano.  

>[!Note]
>Una volta pubblicato, un prezzo per un mercato nel tuo piano, non può essere modificato in un secondo momento.

### <a name="plan-audience"></a>Pianificare il pubblico

Hai la possibilità di configurare ogni piano per essere visibile a tutti o solo a un pubblico specifico di tua scelta. È possibile assegnare l'appartenenza a questo gruppo di destinatari con restrizioni usando gli ID sottoscrizione di Azure.You can assign membership in this restricted audience using Azure subscription IDs.

**Privacy / Questo è un piano privato** (casella di controllo opzionale) - Seleziona questa casella per rendere il tuo piano privato e visibile solo al pubblico limitato di tua scelta. Una volta pubblicato come piano privato, puoi aggiornare il pubblico o scegliere di rendere il piano disponibile a tutti. Una volta che un piano è pubblicato come visibile a tutti, deve rimanere visibile a tutti. (Il piano non può essere configurato nuovamente come piano privato).

**Destinatari con restrizioni (ID sottoscrizione di Azure):** assegnare il gruppo di destinatari che avrà accesso a questo piano privato. All'accesso viene assegnato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione di Azure assegnato. È possibile aggiungere un massimo di 10 ID sottoscrizione o 20.000 ID sottoscrizione clienti se si importa un file di foglio di calcolo CON estensione csv.  Gli ID sottoscrizione di Azure sono rappresentati come GUID e le lettere devono essere minuscole.

>[!Note]
>Il pubblico privato (o pubblico con restrizioni) è diverso dal pubblico di anteprima definito nella scheda [**Anteprima.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Un pubblico di anteprima può accedere alla tua offerta *prima* che l'offerta venga pubblicata dal vivo nel marketplace. Mentre la designazione del pubblico privato si applica solo a un piano specifico, il pubblico di anteprima può visualizzare tutti i piani (privati o meno) ascopo di convalida.

## <a name="technical-configuration"></a>Configurazione tecnica 

La scheda **Configurazione tecnica** consente di caricare il pacchetto di distribuzione che consentirà ai clienti di distribuire il piano.

>[!Note]
>Questa scheda non sarà visibile se questo piano è stato configurato per riutilizzare i pacchetti di un altro piano nella scheda **Impostazione piano.**

### <a name="package-details"></a>Dettagli del pacchetto

La sottoscheda **Dettagli pacchetto** consente di modificare la versione bozza della configurazione tecnica.

***Versione*** - Assegnare la versione corrente della configurazione tecnica.  Incrementare questa versione ogni volta che si pubblica una modifica a questa pagina. La versione deve `{integer}.{integer}.{integer}`essere nel formato .

***File*** di`.zip`pacchetto ( ) - Questo pacchetto contiene tutti i file di modello necessari `.zip` per questo piano, nonché eventuali risorse aggiuntive, inserite nel pacchetto come file.

Tutti i pacchetti del piano dell'applicazione Azure `.zip` devono includere questi due file nella cartella radice di un archivio:All Azure application plan packages must include these two files in the root folder of a archive:

* Un file modello di Resource Manager denominato [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Questo modello automatizza la distribuzione delle risorse nella sottoscrizione di Azure dei clienti.  Per esempi di modelli di Resource Manager, vedere la raccolta di modelli di avvio rapido di Azure o il corrispondente repository GitHub: Modelli di avvio rapido di Azure Resource Manager.For examples of Resource Manager templates, see the [Azure Quickstart Templates gallery](https://azure.microsoft.com/documentation/templates/) or the corresponding [GitHub: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) repo.

* Definizione dell'interfaccia utente per l'esperienza di creazione dell'applicazione Azure denominata [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Le dimensioni massime dei file supportate sono:

* Fino a 1 Gb `.zip` in totale dimensione dell'archivio compresso

* Fino a 1 Gb per ogni `.zip` singolo file non compresso all'interno dell'archivio  

Tutte le nuove offerte di applicazioni di Azure devono includere anche un GUID di [attribuzione dell'utilizzo dei clienti dei partner di Azure.All](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) new Azure application offers must also include an Azure partner customer usage attribution GUID.

>[!Note]
>Se si verifica un problema durante il caricamento dei https://upload.xboxlive.com file, assicurarsi che la rete locale non blocchi il servizio utilizzato dal Centro per i partner.

### <a name="previously-published-packages"></a>Pacchetti pubblicati in precedenza 

La sottoscheda **Pacchetti pubblicati in precedenza** consente di visualizzare tutte le versioni pubblicate della configurazione tecnica.

## <a name="technical-configuration-managed-application-plans-only"></a>Configurazione tecnica (solo piani applicazioni gestite)

I piani di applicazioni gestite presentano ulteriore complessità nella scheda **Configurazione tecnica** oltre i campi **Versione** e **File pacchetto** descritti in precedenza. 

### <a name="enable-just-in-time-jit-access"></a>Abilitare l'accesso JIT (Just-In-Time)

Selezionare questa opzione per abilitare l'accesso JIT (Just-In-Time) per questo piano.  L'accesso JIT consente di richiedere un accesso con privilegi elevati alle risorse di un'applicazione gestita per la risoluzione dei problemi o la manutenzione. Si dispone sempre dell'accesso in sola lettura alle risorse, ma per un periodo di tempo specifico è possibile ottenere un accesso maggiore.  Per altre informazioni, vedere [Abilitare e richiedere l'accesso Just-In-Time per](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)le applicazioni gestite di Azure.For more information, see Enable and request just-in-time access for Azure Managed Applications .  Per richiedere che i consumatori dell'applicazione gestita concedano all'account l'accesso permanente, lasciare deselezionata questa opzione.

>[!Note]
>Assicurarsi di `createUiDefinition.json` aggiornare il file per supportare questa funzionalità.  

### <a name="deployment-mode"></a>Modalità di distribuzione

Selezionare se configurare la **modalità di distribuzione** completa o incrementale durante la distribuzione di questo piano:Select whether to configure **Complete** or Incremental deployment mode when deploying this plan: 

* In **modalità completa,** una ridistribuzione dell'applicazione da parte del cliente comporterà la rimozione `mainTemplate.json`delle risorse nel gruppo di risorse gestite se le risorse non sono definite nel file . 
* In **modalità incrementale,** una ridistribuzione dell'applicazione lascia invariate le risorse esistenti.

Per altre informazioni sulle modalità di distribuzione, vedere Modalità di distribuzione di Azure Resource Manager.To learn more about deployment [modes,](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)see Azure Resource Manager deployment modes .

### <a name="notification-endpoint-url"></a>URL endpoint di notifica

Specificare un endpoint Webhook HTTPS per ricevere notifiche su tutte le operazioni CRUD nelle istanze dell'applicazione gestita di questa versione del piano.

### <a name="customize-allowed-customer-actions"></a>Personalizzare le azioni consentite per i clienti

Selezionare questa opzione per specificare le azioni che i`*/read`clienti possono eseguire sulle risorse gestite oltre alle azioni " " disponibili per impostazione predefinita. 

Elencare le azioni aggiuntive che si desidera consentire al cliente di eseguire qui, separati da punto e virgola.  Per altre informazioni, vedere Informazioni sulle assegnazioni di negazione per le risorse di Azure.For more information, see [Understanding deny assignments for Azure resources.](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)  Per informazioni sulle azioni disponibili, vedere [Operazioni di provider di risorse con Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Ad esempio, per consentire agli utenti di riavviare le macchine virtuali, aggiungere `Microsoft.Compute/virtualMachines/restart/action` alle azioni consentite.

### <a name="global-azure--azure-government-cloud"></a>Globale Azure / Azure Government Cloud

Indicare chi deve avere accesso di gestione a questa applicazione gestita in ogni cloud supportato.  Gli utenti, i gruppi o le applicazioni a cui si desidera concedere l'autorizzazione per il gruppo di risorse gestite vengono identificati tramite le identità di Azure Active Directory (AAD).

ID tenant di ***Azure Active Directory:*** ID tenant AAD (noto anche come ID directory) contenente le identità degli utenti, dei gruppi o delle applicazioni a cui si desidera concedere le autorizzazioni.  È possibile trovare l'ID tenant AAD nel portale di Azure in [Proprietà per Azure Active Directory.You](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)can find your AAD Tenant ID on the Azure portal, in Properties for Azure Active Directory .

***Autorizzazioni:*** aggiungere l'ID oggetto di Azure Active Directory dell'utente, del gruppo o dell'applicazione a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite. Identificare l'utente in base all'ID entità, disponibile nel pannello Utenti di Azure Active Directory nel portale di Azure.Identify the user by their Principal ID, which can be found at the [Azure Active Directory users blade on the Azure portal.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)

Per ogni entità, selezionare uno dei ruoli predefiniti di Azure AD dall'elenco (Proprietario o Collaboratore). Il ruolo selezionato descriverà le autorizzazioni di cui l'entità dia sulle risorse nella sottoscrizione del cliente. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Per altre informazioni sul controllo degli accessi in base al ruolo, vedere Introduzione al controllo degli accessi in base al ruolo nel portale di Azure.For more information about role-based access control (RBAC), see [Get started with RBAC in the Azure portal.](https://docs.microsoft.com/azure/role-based-access-control/overview)

>[!Note]
>Sebbene sia possibile aggiungere fino a 100 autorizzazioni per cloud, è in genere più semplice creare un gruppo di utenti di Active Directory e specificarne l'ID nell'"ID principale".  In questo modo sarà possibile aggiungere altri utenti al gruppo di gestione dopo la distribuzione del piano e ridurre la necessità di aggiornare il piano solo per aggiungere altre autorizzazioni.

### <a name="policy-settings"></a>Impostazioni dei criteri

Applicare criteri di [Azure](https://docs.microsoft.com/azure/governance/policy/overview) all'applicazione gestita per specificare i requisiti di conformità per la soluzione distribuita.  Per le definizioni dei criteri e il formato dei valori dei parametri, vedere [Esempi di criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).  È possibile configurare un massimo di cinque criteri e una sola istanza di ogni opzione Criteri.  Alcuni criteri richiedono parametri aggiuntivi.  Lo SKU Standard è necessario per i criteri di controllo.  Il nome del criterio è limitato a 50 caratteri.

## <a name="co-sell"></a>Co-Sell

Fornire informazioni sulla scheda Cosell è del tutto facoltativo per la pubblicazione dell'offerta. È necessario per ottenere lo status di Co-sell Ready e IP Co-sell Ready. Le informazioni fornite verranno utilizzate dai team di vendita Microsoft per ottenere ulteriori informazioni sulla soluzione durante la valutazione della misura in base alle esigenze dei clienti. Non è disponibile direttamente per i clienti.

Per ulteriori informazioni sul completamento di questa scheda, vedere [Opzione di co-selling nel Centro per i partner.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)

## <a name="test-drive"></a>Test drive

La scheda **Test drive** consente di impostare una dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di impegnarsi ad acquistarla. Scopri di più nell'articolo [Che cos'è Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina **Impostazione offerta** e deselezionare **Abilita test drive**.

### <a name="technical-configuration"></a>Configurazione tecnica

Le applicazioni di Azure usano intrinsecamente il tipo di test di Azure Resource Manager.Azure Applications inherently use the Azure Resource Manager test drive type.  Per altre informazioni, vedere Configurazione tecnica per l'unità di test di [Azure Resource Manager.See Technical configuration for Azure Resource Manager test drive](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) for more information.

### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione distribuzione

Per distribuire test Drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca. (Non richiesto per i test drive di Power BI).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la creazione di report e la fatturazione dell'utilizzo delle risorse. È consigliabile [creare una sottoscrizione](https://docs.microsoft.com/azure/billing/billing-create-subscription) di Azure separata da usare per i test drive se non ne è già presente una. È possibile trovare l'ID sottoscrizione di Azure accedendo al portale di [Azure](https://portal.azure.com/) e passando alla scheda **Sottoscrizioni** del menu a sinistra. Selezionando la scheda verrà visualizzato l'ID sottoscrizione (ad esempio "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID tenant di Azure AD** (obbligatorio): immettere l'ID [tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (AD). Per trovare questo ID, accedere al portale di [Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare , quindi cercare il numero **ID** directory elencato (ad esempio 50c464d3-4930-494c-963c-1e951d15360e). È inoltre possibile cercare l'ID tenant dell'organizzazione [https://www.whatismytenantid.com](https://www.whatismytenantid.com)utilizzando l'URL del nome di dominio all'indirizzo: .

- **Nome tenant di Azure AD** (obbligatorio per Dynamic 365): immettere il nome di Azure Active Directory (AD). Per trovare questo nome, accedere al portale di [Azure](https://portal.azure.com/), nell'angolo superiore destro il nome del tenant verrà elencato sotto il nome dell'account.

- **ID app Azure AD** (obbligatorio): immettere [l'ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (AD). Per trovare questo ID, accedere al portale di [Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Registrazioni app**, quindi cercare il numero ID **applicazione** elencato (ad esempio 50c464d3-4930-494c-963c-1e951d15360e).

- **Segreto client dell'app Azure AD** (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure AD.Azure AD app client secret (required): Enter your Azure AD application client secret . Per trovare questo valore, accedere al portale di [Azure](https://portal.azure.com/). Seleziona la scheda **Azure Active Directory** nel menu a sinistra, seleziona **Registrazioni app**, quindi seleziona l'app di test drive. Selezionare quindi **Certificati e segreti**, Nuovo **segreto client**, immettere una descrizione, **selezionare Mai** in **Scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. (Non uscire dalla pagina prima di aver copiato il valore, altrimenti non avrai accesso al valore).

Ricordati di **salvare** prima di passare alla sezione successiva!

### <a name="test-drive-listings-optional"></a>Elenchi di test drive (facoltativo)

L'opzione **Elenchi Test Drive** disponibile nella scheda Test **drive** visualizza le lingue (e i mercati) in cui è disponibile il test drive, attualmente l'inglese (Stati Uniti) è l'unica posizione disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e la data/ora in cui è stato aggiunto. Dovrai definire i dettagli del test drive (descrizione, manuale utente, video, ecc.) per ogni lingua/mercato.

- **Descrizione** (obbligatorio): descrivere il test drive, ciò che verrà dimostrato, gli obiettivi per l'utente da sperimentare, le funzionalità da esplorare e tutte le informazioni pertinenti che consentono all'utente di determinare se acquistare l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

- **Accedere alle informazioni** (necessarie per Azure Resource Manager e test drive della logica): spiegare ciò che un cliente deve sapere per poter accedere e usare questo test drive. Esaminare uno scenario per l'utilizzo dell'offerta e esattamente ciò che il cliente deve sapere per accedere alle funzionalità durante il test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale utente** (obbligatorio): una procedura dettagliata dell'esperienza di test drive. Il Manuale dell'utente dovrebbe coprire esattamente ciò che si desidera che il cliente di ottenere da sperimentare il test drive e servire come riferimento per tutte le domande che possono avere. Il file deve essere in formato PDF e deve essere denominato (255 caratteri max) dopo il caricamento.

- **Video: aggiungi video** (facoltativo): i video possono essere caricati su YouTube o Vimeo e referenziati qui con un link e un'immagine di anteprima (533 x 324 pixel) in modo che un cliente possa visualizzare una procedura dettagliata di informazioni per aiutarli a comprendere meglio il test drive, incluso come utilizzare con successo le funzionalità della tua offerta e comprendere gli scenari che ne evidenziano i vantaggi.
  - **Nome** (obbligatorio)
  - **URL (solo YouTube o Vimeo)** (obbligatorio)
  - **Miniatura (533 x 324 px):** il file di immagine deve essere in formato PNG.

Selezionare **Salva** dopo aver completato questi campi.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Dopo aver completato tutte le sezioni richieste dell'offerta, selezionare **Pubblica** nell'angolo in alto a destra del portale. Si verrà reindirizzati alla pagina **Revisione e pubblicazione.** 

Se è la prima volta che pubblichi questa offerta, puoi:

- Vedi lo stato di completamento per ogni sezione dell'offerta.
    - *Non avviato* - significa che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* - significa che la sezione contiene errori che devono essere corretti o che richiedono ulteriori informazioni. Tornare alla sezione o alle sezioni e aggiornarla.
    - *Completo* - significa che la sezione è completa, tutti i dati richiesti sono stati forniti e non ci sono errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Fornisci istruzioni di test al team di certificazione per assicurarti che l'app sia testata correttamente, oltre a eventuali note aggiuntive utili per comprendere la tua app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Ti invieremo un'email per comunicarti quando è disponibile una versione di anteprima dell'offerta da rivedere e approvare. Torna al Centro per i partner e seleziona **Go-live** per l'offerta di pubblicare la tua offerta al pubblico (o se un'offerta privata, al pubblico privato).

### <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

Il passaggio **di convalida manuale** nel processo di pubblicazione rappresenta una revisione approfondita dell'offerta e degli asset tecnici associati (in particolare il modello di Azure Resource Manager), i problemi vengono in genere presentati come collegamenti a richieste pull (PR). Per una spiegazione su come visualizzare e rispondere a queste richieste pull, vedere [Gestione del feedback di revisione](./azure-apps-review-feedback.md).

Se si verificano errori in uno o più passaggi di pubblicazione, è necessario correggerli e ripubblicare l'offerta.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
