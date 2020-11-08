---
title: Pianificare un'offerta applicazione Azure per il Marketplace commerciale
description: Informazioni su come pianificare una nuova offerta di applicazione Azure per l'inserzione o la vendita in Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) usando il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 61c8127941c54270b938babefaf4eb17627a15f3
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370176"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Pianificare un'offerta applicazione Azure per il Marketplace commerciale

Questo articolo illustra le diverse opzioni e i requisiti per la pubblicazione di un'offerta di applicazione Azure nel Marketplace commerciale Microsoft.

## <a name="before-you-begin"></a>Prima di iniziare

La progettazione, la creazione e i test delle offerte per applicazioni Azure richiedono esperienza tecnica nella piattaforma di Azure e nelle tecnologie usate per creare l'offerta. Il team tecnico deve avere il livello di conoscenza delle tecnologie Microsoft seguenti:

- Conoscenza di base dei [servizi di Azure](https://azure.microsoft.com/services/).
- Capacità di [progettare applicazioni Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/).
- Esperienza pratica in [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage#storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking#networking).
- Esperienza pratica in [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
- Esperienza pratica in [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Documentazione e risorse tecniche

Esaminare le risorse seguenti durante la pianificazione dell'offerta di applicazione Azure per il Marketplace commerciale.

- [Comprendere modelli di Azure Resource Manager](/azure/azure-resource-manager/templates/template-syntax.md)
- Argomenti di avvio rapido:
    - [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/)
    - [Guida alle procedure consigliate per i modelli di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Pubblicare una definizione di applicazione](/azure/managed-applications/publish-service-catalog-app)
    - [Distribuire un'app del catalogo di servizi](/azure/managed-applications/deploy-service-catalog-quickstart)
- Esercitazioni:
    - [Creare file di definizioni](/azure/managed-applications/publish-service-catalog-app)
- Esempi:
    - [Interfaccia della riga di comando di Azure](/azure/managed-applications/cli-samples)
    - [Azure PowerShell](/azure/managed-applications/powershell-samples)
    - [Soluzioni di applicazioni gestite](/azure/managed-applications/sample-projects)

Il video [Creazione di modelli di soluzione e applicazioni gestite per Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) offre un'introduzione completa al tipo di offerta applicazione Azure:

- Tipi di offerta disponibili
- Quali asset tecnici sono necessari
- Come creare un modello di Azure Resource Manager
- Sviluppo e test dell'interfaccia utente dell'app
- Come pubblicare l'offerta di app
- Processo di revisione dell'applicazione

### <a name="suggested-tools"></a>Strumenti suggeriti

Per la gestione dell'applicazione di Azure scegliere uno o entrambi gli ambienti di scripting seguenti:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

È consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo:

- [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) con le estensioni seguenti:
    - Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

È possibile esaminare gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/). Se si usa Visual Studio, vedere la [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="listing-options"></a>Opzioni di presentazione

Dopo la pubblicazione dell'offerta, le opzioni di elenco per l'offerta vengono visualizzate sotto forma di pulsante nell'angolo superiore sinistro della pagina di presentazione dell'offerta. Ad esempio, la schermata seguente mostra una pagina di presentazione dell'offerta in Azure Marketplace con il pulsante _Get it Now (Ottieni ora_ ). Se si è scelto di offrire un test drive, viene visualizzato anche il pulsante _test drive_ .

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Viene illustrata una pagina di elenco in Azure Marketplace.":::

## <a name="test-drive"></a>Test drive

È possibile scegliere di abilitare un test drive per l'offerta applicazione Azure che consente ai clienti di provare l'offerta prima di acquistarla. Per ulteriori informazioni sulle unità di test, vedere [che cos'è un test drive?](what-is-test-drive.md). Per informazioni sulla configurazione di diversi tipi di unità di test, vedere [configurazione tecnica di test drive](test-drive-technical-configuration.md).

È anche possibile leggere informazioni sulle [procedure consigliate di test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) e scaricare il file PDF di [test drives Overview](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (assicurarsi che il blocco popup sia disattivato).

> [!NOTE]
> Informazioni che l'utente deve notare anche se skimmingBecause tutte le applicazioni Azure vengono implementate usando un modello di Azure Resource Manager, l'unico tipo di test drive disponibili per un [applicazione Azure è un test drive basato su Azure Resource Manager](azure-resource-manager-test-drive.md).

## <a name="customer-leads"></a>Clienti potenziali

Per raccogliere informazioni sui clienti, è necessario connettere l'offerta al sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e al negozio online in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Il Marketplace commerciale supporta un'ampia gamma di sistemi CRM, oltre alla possibilità di usare una tabella di Azure o di configurare un endpoint HTTPS usando Power automatici.

È possibile aggiungere o modificare una connessione CRM in qualsiasi momento durante o dopo la creazione dell'offerta. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="categories-and-subcategories"></a>Categorie e sottocategorie

È possibile scegliere almeno una delle due categorie per raggruppare l'offerta nelle aree di ricerca del Marketplace commerciale appropriate. È possibile scegliere fino a due sottocategorie per ogni categoria primaria e secondaria. Per un elenco completo di categorie e sottocategorie, vedere le [procedure consigliate](gtm-offer-listing-best-practices.md#categories)per l'elenco delle offerte.

## <a name="legal-contracts"></a>Contratti legali

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard che è possibile usare per le offerte nel Marketplace commerciale. Quando si offre il software nel contratto standard, i clienti devono leggere e accettare una sola volta e non è necessario creare termini e condizioni personalizzati.

Se si sceglie di utilizzare il contratto standard, si ha la possibilità di aggiungere termini di modifica universale e fino a 10 modifiche personalizzate al contratto standard. È anche possibile usare i propri termini e condizioni anziché il contratto standard. Questi dettagli vengono gestiti nella pagina delle **Proprietà** . Per informazioni dettagliate, vedere [contratto standard per Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il Marketplace commerciale, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione in base al contratto standard o a termini e condizioni. Se si desidera modificare le condizioni del contratto standard, è possibile utilizzare le modifiche del contratto standard.

## <a name="offer-listing-details"></a>Dettagli elenco offerte

Quando si crea una nuova offerta applicazione Azure nel centro per i partner, si immetteranno testo, immagini, video facoltativi e altri dettagli nella pagina di presentazione dell'offerta. Si tratta delle informazioni che i clienti visualizzeranno quando scoprono l'inserzione dell'offerta in Azure Marketplace, come illustrato nell'esempio seguente.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

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

Lo screenshot seguente mostra come vengono visualizzate le informazioni sull'offerta nell'portale di Azure:

[![Viene illustrato il modo in cui questa offerta viene visualizzata nel portale di Azure.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Titolo
2. Descrizione
3. Collegamenti utili
4. Schermate

> [!NOTE]
> Se la descrizione dell'offerta inizia con la frase "questa applicazione è disponibile solo in [lingua non inglese]", non è necessario che il contenuto dell'elenco di offerte sia in inglese.

Per semplificare la creazione dell'offerta, è possibile preparare alcuni di questi elementi in anticipo. Se non diversamente specificato, sono necessari gli elementi seguenti.

- **Nome** : questo nome verrà visualizzato come titolo dell'inserzione dell'offerta nel Marketplace commerciale. Il nome può essere un marchio registrato. Non può contenere emoji (ad eccezione dei simboli di marchio e copyright) e deve essere limitato a 50 caratteri.
- **Riepilogo risultati ricerca** : descrivere lo scopo o la funzione dell'offerta come una singola frase, in testo normale senza interruzioni di riga, in 100 caratteri o meno. Questo riepilogo viene usato nei risultati della ricerca nell'elenco dei Marketplace commerciali.
- **Breve descrizione** : fornire fino a 256 caratteri di testo normale. Questo riepilogo verrà visualizzato nella pagina dei dettagli dell'offerta.
- **Descrizione** : questa descrizione verrà visualizzata nella panoramica degli elenchi di Azure Marketplace. Si consiglia di includere una proposta di valore, i vantaggi principali, la base di utenti prevista, le associazioni di categoria o di settore, le opportunità di acquisto in-app, le esigenze dei clienti o il dolore che l'offerta indirizza, eventuali divulgazioni richieste e un collegamento per ottenere ulteriori informazioni.

    Questa casella di testo contiene controlli avanzati dell'editor di testo che è possibile usare per rendere più accattivante la descrizione. È anche possibile usare i tag HTML per formattare la descrizione. In questa casella è possibile immettere fino a 3.000 caratteri di testo, inclusi markup HTML e spazi. Per altri suggerimenti, vedere [scrivere una descrizione di app](/windows/uwp/publish/write-a-great-app-description.md) e [tag HTML supportati nelle descrizioni delle offerte del Marketplace commerciale](supported-html-tags.md).

- **Parole chiave di ricerca** (facoltativo): fornire fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta nel negozio online. Per ottenere risultati ottimali, usare queste parole chiave anche nella descrizione. Non è necessario includere il **nome** dell'offerta e la **Descrizione**. Tale testo viene automaticamente incluso nella ricerca.
- **Collegamento all'informativa sulla privacy** : l'URL per l'informativa sulla privacy dell'azienda. È necessario fornire un'informativa sulla privacy valida e avere la responsabilità di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy.
- **Collegamenti utili** (facoltativo): è possibile fornire collegamenti a diverse risorse per gli utenti dell'offerta. Ad esempio forum, domande frequenti e note sulla versione.
- **Informazioni di contatto** : è necessario designare i contatti seguenti dall'organizzazione:
  - **Contatto del supporto tecnico** : fornire il nome, il telefono e il messaggio di posta elettronica per i partner Microsoft da usare quando i clienti aprono i biglietti. È inoltre necessario includere l'URL per il sito Web di supporto.
  - **Contatto tecnico** : fornire il nome, il telefono e il messaggio di posta elettronica che Microsoft deve usare direttamente quando si verificano problemi con l'offerta. Le informazioni di contatto non sono elencate nel Marketplace commerciale.
  - **Contatto del programma CSP** (facoltativo): specificare il nome, il telefono e il messaggio di posta elettronica se si opta per il programma Cloud Solution Provider (CSP), in modo che i partner possano contattare l'utente con qualsiasi domanda. È anche possibile includere un URL per i materiali di marketing.
- **Supporti – Logos** : fornire un file PNG per il logo di **grandi** dimensioni. Il centro per i partner lo utilizzerà per creare un logo **piccolo** e **medio** . Facoltativamente, è possibile sostituirli con immagini diverse in un secondo momento.
  - Grande (da 216 x 216 a 350 x 350 px, obbligatorio)
  - Media (90 x 90 px, facoltativo)
  - Piccolo (48 x 48 px, facoltativo)

  Questi logo vengono usati in posizioni diverse negli archivi online:
  - Il logo piccolo viene visualizzato nei risultati della ricerca di Azure Marketplace.
  - Il logo medio viene visualizzato quando si crea una nuova risorsa in Microsoft Azure.
  - Il logo di grandi dimensioni viene visualizzato nella pagina di inserzione dell'offerta in Azure Marketplace.

  Seguire queste linee guida per i logo:

  - La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
  - I colori del tema del portale sono il bianco e il nero. Non usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale. Si consiglia di usare colori primari semplici.
  - Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
  - L'aspetto del logo deve essere semplice ed evitare gradienti nel logo o in background. Non inserire testo, nemmeno il nome del marchio o della società, sul logo. Le immagini sfocate saranno rifiutate.
  - Verificare che il logo non venga adattato.

- **Supporti-schermate** (facoltativo): è consigliabile aggiungere schermate che mostrano il funzionamento dell'offerta. È possibile aggiungere fino a cinque schermate con i requisiti seguenti, che mostrano il funzionamento dell'offerta:
  - 1280 x 720 pixel
  - file con estensione png
  - Deve includere una didascalia
- **Supporti-video** (facoltativo): è possibile aggiungere fino a cinque video con i requisiti seguenti, che dimostrano l'offerta:
  - Name
  - URL: deve essere ospitato solo su YouTube o Vimeo.
  - Anteprima: file 1280 x 720. png

> [!NOTE]
> L'offerta deve soddisfare i [criteri di certificazione del Marketplace commerciale](/legal/marketplace/certification-policies#100-general.md) generale da pubblicare nel Marketplace commerciale.

## <a name="preview-audience"></a>Destinatari dell'anteprima

Un pubblico di anteprima può accedere all'offerta prima di essere pubblicata Live nei negozi online per testare la funzionalità end-to-end prima di pubblicarla in tempo reale.

> [!NOTE]
> Un pubblico di anteprima è diverso da un piano privato. Un piano privato è quello reso disponibile solo per un determinato gruppo di destinatari. In questo modo è possibile negoziare un piano personalizzato con clienti specifici.

È possibile definire i destinatari dell'anteprima usando gli ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi viene visualizzato dai clienti.

## <a name="technical-configuration"></a>Configurazione tecnica

Per le applicazioni gestite che generano eventi di misurazione usando le [API del servizio di misurazione del Marketplace](partner-center-portal/marketplace-metering-service-apis.md), è necessario fornire l'identità che il servizio userà per la creazione di eventi di misurazione.

Questa configurazione è obbligatoria se si vuole usare [eventi di utilizzo in batch](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event). Se si vuole inviare un [evento di utilizzo](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event), è anche possibile usare il [servizio metadati dell'istanza](/azure/active-directory/managed-identities-azure-resources/overview.md) per ottenere il [token web JSON (JWT) Bearer token](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)).

- **Azure Active Directory ID tenant** (obbligatorio): all'interno del portale di Azure, è necessario [creare un'app Azure Active Directory (ad)](/azure/active-directory/develop/howto-create-service-principal-portal.md) in modo che sia possibile convalidare la connessione tra i due servizi sia dietro una comunicazione autenticata. Per trovare l' [ID tenant](/azure/active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) per l'app Azure Active Directory (Azure ad), nel pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) della Azure Active Directory. Nella colonna **nome visualizzato** selezionare l'app. Cercare quindi le **Proprietà** e quindi per l' **ID della directory (tenant)** , ad esempio `50c464d3-4930-494c-963c-1e951d15360e` .
- **Azure Active Directory ID applicazione** (obbligatorio): sono necessari anche l' [ID applicazione](/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-and-app-id-values-for-signing-in.md) e una chiave di autenticazione. Per trovare l'ID applicazione, passare al pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in Azure Active Directory. Nella colonna **nome visualizzato** selezionare l'app e quindi cercare l' **ID applicazione (client)** (ad esempio `50c464d3-4930-494c-963c-1e951d15360e` ). Per trovare la chiave di autenticazione, passare a **Impostazioni** e selezionare **Chiavi**. Sarà necessario specificare una descrizione e una durata, quindi verrà fornito un valore numerico.

> [!NOTE]
> L'ID applicazione di Azure verrà associato all'ID editore e potrà essere riutilizzato solo all'interno di questo account del server di pubblicazione.

## <a name="additional-sales-opportunities"></a>Opportunità di vendita aggiuntive

È possibile scegliere di acconsentire ai canali di marketing e vendita supportati da Microsoft. Quando si crea l'offerta nel centro per i partner, si vedranno due schede verso la fine del processo:

- **Rivendere tramite CSP** : usare questa opzione per consentire ai partner di Microsoft Cloud Solution Provider (CSP) di rivendere la soluzione come parte di un'offerta in bundle. Per ulteriori informazioni, vedere il [programma Cloud Solution Provider](/azure/marketplace/cloud-solution-providers.md) .
- **Co-selling con Microsoft** : questa opzione consente ai team di vendita Microsoft di prendere in considerazione la soluzione di co-selling IP idonea per valutare le esigenze dei clienti. Per informazioni dettagliate su come preparare l'offerta per la valutazione, vedere [opzione di co-selling nel centro](partner-center-portal/commercial-marketplace-co-sell.md) per i partner. Per ulteriori informazioni sul marketing dell'offerta tramite i canali del partner Microsoft CSP, vedere [Cloud Solution Provider](cloud-solution-providers.md).

Per altre informazioni, vedere [aumentare le attività del cloud con Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

## <a name="plans"></a>Plans

Applicazione Azure offerte richiedono almeno un piano. Un piano definisce l'ambito e i limiti della soluzione e i prezzi associati, se applicabile. È possibile creare più piani per l'offerta per offrire ai clienti diverse opzioni tecniche e di prezzo.

Per indicazioni generali sui piani, inclusi i modelli di prezzi e i piani privati, vedere [piani e prezzi per le offerte per Marketplace commerciali](plans-pricing.md). Le sezioni seguenti illustrano informazioni aggiuntive specifiche per i piani applicazione Azure.

### <a name="types-of-plans"></a>Tipi di piani

Esistono due tipi di piani per le applicazioni di Azure: il _modello di soluzione_ e l' _applicazione gestita_. Entrambi i tipi di piano supportano l'automazione della distribuzione e della configurazione di una soluzione oltre a una singola macchina virtuale (VM). È possibile automatizzare il processo di fornitura di più risorse, tra cui macchine virtuali, rete e risorse di archiviazione, per offrire soluzioni complesse, ad esempio soluzioni IaaS. Entrambi i tipi di piano possono impiegare molti tipi diversi di risorse di Azure, tra cui, ma non limitati alle macchine virtuali.

- I piani di **modelli di soluzione** sono uno dei modi principali per pubblicare una soluzione nel Marketplace commerciale. I piani di modelli di soluzione non sono transazionali nel Marketplace commerciale, ma possono essere usati per distribuire le offerte di macchine virtuali a pagamento fatturate tramite il Marketplace commerciale. Utilizzare il tipo di piano modello soluzione quando il cliente gestirà la soluzione e le transazioni vengono fatturate in base a un altro piano. Per ulteriori informazioni sulla creazione di modelli di soluzione, vedere [che cos'è Azure Resource Manager?](/azure/azure-resource-manager/resource-group-overview.md)
- I piani di **applicazioni gestite** consentono di creare e distribuire in modo semplice applicazioni chiavi in mano completamente gestite per i clienti. Hanno le stesse funzionalità dei piani di modelli di soluzione, con alcune differenze principali:
    - Le risorse vengono distribuite in un gruppo di risorse e sono gestite dall'autore dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. 
    - In qualità di editore, è possibile specificare il costo per il supporto continuo della soluzione e le transazioni sono supportate tramite il Marketplace commerciale.
 
    Usare il tipo di piano di applicazione gestita quando l'utente o il cliente richiede che la soluzione sia gestita da un partner o si distribuirà una soluzione basata sulla sottoscrizione. Per altre informazioni sui vantaggi e sui tipi di applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](/azure/managed-applications/overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Per pianificare un modello di soluzione, vedere [pianificare un modello di soluzione per un'offerta di applicazione Azure](plan-azure-app-solution-template.md).
- Per pianificare un'applicazione gestita di Azure, vedere [pianificare un'applicazione gestita di Azure per un'offerta di applicazione Azure](plan-azure-app-managed-app.md).
