---
title: Creare un'offerta Dynamics 365 for Operations nel marketplace commerciale
description: Come creare una nuova offerta Dynamics 365 for Operations per la presentazione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) usando il portale del marketplace commerciale nel Centro per i partner.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 13e05a8771be162ebe37cc79fc93cfa404183d1d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846836"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Creare un'offerta Dynamics 365 for Operations

Questo argomento descrive come creare una nuova offerta Dynamics 365 for Operations. [Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) di Microsoft è un servizio ERP (Enterprise Resource Planning) che supporta finanza avanzata, operazioni, produzione e gestione della catena di approvvigionamento. Tutte le offerte per Dynamics 365 for Operations devono essere sottoposte al processo di certificazione.

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

>[!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner verranno aggiornate solo nel sistema e archiviate dopo la nuova pubblicazione. Assicurarsi di inviare l'offerta per la pubblicazione dopo aver apportato le modifiche.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Dynamics 365 for Operations**.

    ![Menu di spostamento di sinistra.](./media/new-offer-dynamics-365-ops.png)

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

Consente di presentare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (che inizia con *http* o *https*) dal quale ottenere una versione di valutazione. Ad esempio: `https://contoso.com/trial/my-app`. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni degli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Consente di raccogliere informazioni di contatto del cliente tramite la connessione al sistema di gestione delle relazioni con i clienti (CRM, Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Clienti potenziali](#customer-leads).

### <a name="test-drive"></a>Test drive

Il test drive è un'ottima soluzione per presentare un'offerta a potenziali clienti, dando loro la possibilità di provarla prima dell'acquisto e ottenendo un incremento delle conversioni e la generazione di clienti potenziali altamente qualificati. [Altre informazioni sui test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita un test drive**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo. Configurare l'ambiente di test drive nella sezione [Configurazione tecnica del test drive](#test-drive-technical-configuration) più avanti in questo argomento.

Per altre informazioni, vedere [Test drive dell'offerta nel marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Tipo di test drive

Selezionare una delle seguenti opzioni:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che rientrano in questo scenario usano solo risorse di Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft ospita e gestisce il servizio per i test drive (inclusi il provisioning e la distribuzione) per un sistema di pianificazione delle risorse aziendali Business Central (finanza, operazioni, catena di approvvigionamento, CRM e così via).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft ospita e gestisce il servizio per i test drive (inclusi il provisioning e la distribuzione) per un sistema Customer Engagement (vendite, assistenza, servizio di progetto, assistenza sul campo e così via).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft ospita e gestisce il servizio per i test drive (inclusi il provisioning e la distribuzione) per un sistema di pianificazione delle risorse aziendali Finance and Operations (finanza, operazioni, produzione, catena di approvvigionamento e così via).
- **[App per la logica](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : modello di distribuzione che comprende tutte le architetture di soluzioni complesse. Tutti i prodotti personalizzati devono usare questo tipo di test drive.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : collegamento incorporato a un dashboard personalizzato. È consigliabile usare questo tipo di test drive per i prodotti che vogliono fornire una dimostrazione di un oggetto visivo interattivo di Power BI. È sufficiente caricare l'URL di Power BI incorporato qui.

#### <a name="additional-test-drive-resources"></a>Risorse aggiuntive per i test drive

- [Procedure consigliate di marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Procedure consigliate tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Panoramica](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (file con estensione pdf, assicurarsi che il blocco popup sia disattivato)

### <a name="customer-leads"></a>Clienti potenziali

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per altre informazioni, vedere [Panoramica della gestione dei clienti potenziali](./commercial-marketplace-get-customer-leads.md).

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="category"></a>Category

Selezionare un minimo di una e un massimo di tre categorie, che verranno usate per inserire l'offerta nelle aree di ricerca appropriate del marketplace. Nella descrizione sottolineare come l'offerta rientri in queste categorie.

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

In questa sezione è possibile fornire logo e immagini che verranno usati quando si mostra l'offerta al cliente. Tutte le immagini devono avere estensione png.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo dello store

Fornire il logo dell'offerta in due dimensioni di pixel:

- **Piccola** (48 x 48)
- **Grande** (216 x 216)

#### <a name="hero"></a>Banner

Il banner alto è facoltativo. Se ne viene fornito uno, deve misurare 815 x 290 pixel.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere screenshot che mostrano il funzionamento dell'offerta. È necessario almeno uno screenshot ed è possibile aggiungerne fino a cinque. Tutti gli screenshot devono avere dimensioni di 1280 x 720 pixel.

#### <a name="videos"></a>Video

Se si vuole, è possibile aggiungere fino a quattro video che descrivono l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ogni video, immettere il nome, l'URL e un'immagine di anteprima (1280 x 720 pixel)

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

[Procedure consigliate per la presentazione di offerte nel marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

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

In questa pagina è possibile configurare una dimostrazione ("test drive") che consente ai clienti di provare l'offerta prima di acquistarla. Per altre informazioni, vedere l'articolo [Informazioni sul test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Per abilitare un test drive, selezionare la casella di controllo **Abilita un test drive** nella scheda [Configurazione dell'offerta](#test-drive). Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Sono disponibili i tipi di test drive seguenti, ognuno con requisiti di configurazione tecnici specifici.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [App per la logica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configurazione tecnica non necessaria)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configurazione tecnica per il modello di Azure Resource Manager per il test drive

Modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che rientrano in questo scenario usano solo risorse di Azure. Per altre informazioni sulla configurazione, vedere [Test drive di Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Aree** (obbligatorio): attualmente esistono 26 aree supportate da Azure in cui è possibile rendere disponibile il test drive. In genere, è consigliabile rendere disponibile il test drive nelle aree in cui si prevede il numero maggiore di clienti, in modo che questi possano selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che alla sottoscrizione sia consentito distribuire tutte le risorse necessarie in ognuna delle aree selezionate.

- **Istanze**: selezionare il tipo (frequente o poco utilizzata) e il numero di istanze disponibili, che verrà moltiplicato per il numero di aree in cui è disponibile l'offerta.

    **Frequente**: questo tipo di istanza viene distribuito ed è in attesa dell'accesso per ogni area selezionata. I clienti possono accedere immediatamente alle istanze impostate su *Frequente* di un test drive, anziché dover attendere la distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. È consigliabile prevedere almeno un'istanza *Frequente*, in quanto la maggior parte dei clienti non vuole attendere il completamento delle distribuzioni e di conseguenza si verifica un calo di utilizzo da parte dei clienti se non è presente alcuna istanza impostata su *Frequente*.

    **Poco utilizzata**: questo tipo di istanza rappresenta il numero totale di istanze che è possibile distribuire per ogni area. Per le istanze di questo tipo è necessario distribuire l'intero modello di Resource Manager del test drive quando un cliente richiede il test drive e di conseguenza le istanze impostate su *Poco utilizzata* vengono caricate più lentamente rispetto a quelle impostate su *Frequente*. Il compromesso consiste nel fatto che è necessario pagare solo per la durata del test drive, che *non* è sempre in esecuzione nella sottoscrizione di Azure come un'istanza *Frequente*.

- **Modello di Azure Resource Manager del test drive**: caricare il file con estensione zip contenente il modello di Azure Resource Manager.  Per altre informazioni sulla creazione di un modello di Azure Resource Manager, vedere l'articolo di avvio rapido [Creare e distribuire modelli di Resource Manager con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durata test drive** (obbligatorio): immettere il periodo di tempo per cui il test drive resterà attivo, in numero di ore. Il test drive termina automaticamente alla fine di questo periodo di tempo. Questa durata può essere impostata solo su un numero intero di ore, ad esempio "2" ore, mentre "1,5" non è un valore valido.

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configurazione tecnica per il test drive di Dynamics 365

Microsoft può rimuovere gli elementi di complessità della configurazione di un test drive ospitando e gestendo il provisioning e la distribuzione del servizio con questo tipo di test drive. La configurazione di questo tipo di test drive ospitato è la stessa, indipendentemente dal fatto che il test drive sia per un gruppo di destinatari di Business Central, Customer Engagement o Operations.

- **Max test drive simultanee** (obbligatorio): impostare il numero massimo di clienti per volta che possono usare il test drive. Poiché ogni utente simultaneo utilizzerà una licenza di Dynamics 365 mentre il test drive è attivo, è necessario assicurarsi di avere un numero sufficiente di licenze per supportare il limite massimo impostato. Valore consigliato da 3 a 5.

- **Durata test drive** (obbligatorio): immettere il periodo di tempo per cui il test drive resterà attivo definendo il numero di ore. Dopo questo numero di ore, la sessione terminerà e non utilizzerà più una delle licenze. È consigliabile immettere un valore di 2-24 ore, a seconda della complessità dell'offerta. Questa durata può essere impostata solo su un numero intero di ore, ad esempio "2" ore, mentre "1,5" non è un valore valido.  Se l'utente esaurisce il tempo e vuole accedere di nuovo al test drive, può richiedere una nuova sessione.

- **URL istanza** (obbligatorio): l'URL in cui il cliente avvierà un test drive. È in genere l'URL dell'istanza di Dynamics 365 che esegue l'app con i dati di esempio installati, ad esempio `https://testdrive.crm.dynamics.com`.

- **URL API Web istanza** (obbligatorio): recuperare l'URL dell'API Web per l'istanza di Dynamics 365 accedendo all'account Microsoft 365 e passando a **Impostazioni** \&gt; **Personalizzazione** \&gt; **Risorse per sviluppatori** \&gt; **Instance Web API (Service Root URL)** (API Web istanza - URL radice del servizio) e copiare l'URL indicato qui, ad esempio `https://testdrive.crm.dynamics.com/api/data/v9.0`.

- **Nome ruolo** (obbligatorio): specificare il nome del ruolo di sicurezza definito nel test drive di Dynamics 365 personalizzato. Il nome verrà assegnato all'utente durante il relativo test drive (ad esempio, test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configurazione tecnica per il test drive di un'app per la logica

Tutti i prodotti personalizzati devono usare questo tipo di modello di distribuzione dei test drive, che comprende un'ampia gamma di architetture di soluzioni complesse. Per altre informazioni sulla configurazione di test drive per app per la logica, vedere [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) (Operazioni) e [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) (Coinvolgimento del cliente) su GitHub.

- **Area** (obbligatorio, elenco a discesa a selezione singola): attualmente esistono 26 aree supportate da Azure in cui è possibile rendere disponibile il test drive. Le risorse per l'app per la logica verranno distribuite nell'area selezionata. Se alcune risorse personalizzate dell'app per la logica sono archiviate in un'area specifica, assicurarsi che l'area sia selezionata qui. Il modo migliore per assicurarsi di disporre delle risorse personalizzate disponibili per l'area è distribuire completamente l'app per la logica in locale nella sottoscrizione di Azure nel portale e verificare che funzioni prima di effettuare questa selezione.

- **Max test drive simultanee** (obbligatorio): impostare il numero massimo di clienti per volta che possono usare il test drive. Questi test drive sono già distribuiti e consentono ai clienti di accedervi istantaneamente senza attendere una distribuzione.

- **Durata test drive** (obbligatorio): immettere il periodo di tempo per cui il test drive resterà attivo, in numero di ore. Il test drive termina automaticamente alla fine di questo periodo di tempo.

- **Nome gruppo di risorse di Azure** (obbligatorio): immettere un nome in [Nome gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in cui salvare il test drive dell'app per la logica.

- **Nome dell'app per la logica** (obbligatorio): immettere il nome dell'app per la logica che assegna il test drive all'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- **Effettua il deprovisioning nome app logica** (obbligatorio): immettere il nome dell'app per la logica per cui viene effettuato il deprovisioning del test drive quando il cliente ha finito. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configurazione tecnica non necessaria per i test drive di Power BI

I prodotti che vogliono fornire una dimostrazione di un oggetto visivo di Power BI interattivo possono usare un collegamento incorporato per condividere un dashboard personalizzato come test drive, senza che siano necessarie altre attività di configurazione tecnica. Sono disponibili altre informazioni sulla configurazione di app modello di [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview).

### <a name="deployment-subscription-details"></a>Dettagli abbonamento distribuzione

Perché il test drive venga distribuito automaticamente, creare e specificare una sottoscrizione di Azure separata e univoca. Questa operazione non è necessaria per i test drive di Power BI.

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile provare a [creare una sottoscrizione di Azure separata](https://docs.microsoft.com/azure/billing/billing-create-subscription) da usare per i test drive, se non esiste già. Per trovare gli ID sottoscrizione di Azure, accedere al [portale di Azure](https://portal.azure.com/) e passare alla scheda **Sottoscrizioni** del menu a sinistra. Se si seleziona la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5ab6-6789-1h234g764ghty".

- **ID tenant di Azure AD** (obbligatorio): immettere l'[ID tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) di Azure Active Directory (AD). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Proprietà** e quindi cercare il numero **ID directory** elencato, ad esempio 50c464d3-4930-494c-963c-1e951d15360e. È anche possibile cercare l'ID tenant dell'organizzazione usando l'indirizzo del nome di dominio alla pagina [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nome tenant di Azure AD** (obbligatorio per Dynamics 365): immettere il nome di Azure Active Directory (AD). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/): nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.

- **Azure AD app ID** (ID app di Azure AD) (obbligatorio): immettere l'[ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) di Azure Active Directory (AD). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Registrazioni app** e quindi cercare il numero **ID applicazione** elencato, ad esempio 50c464d3-4930-494c-963c-1e951d15360e.

- **Azure AD app client secret** (Segreto client app Azure AD) (obbligatorio): immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) dell'app Azure AD. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu a sinistra, selezionare **Registrazioni app** e quindi selezionare l'app test drive. Selezionare **Certificati e segreti** e **Nuovo segreto client**, immettere una descrizione, selezionare **Mai** in **Scade** e quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. Non uscire dalla pagina prima di aver preso nota del valore o non sarà più possibile accedervi.

Prima di continuare, selezionare **Salva bozza**.

### <a name="test-drive-marketplace-listings"></a>Presentazione del test drive nel marketplace

L'opzione **Presentazione nel marketplace** disponibile nella scheda **Test drive** visualizza le lingue in cui è disponibile il test drive. Attualmente, **Inglese (Stati Uniti)** è l'unica posizione disponibile. Selezionare il nome della lingua per immettere le informazioni che descrivono l'esperienza del test drive.

- **Descrizione** (obbligatorio): descrivere il test drive, che cosa si intende dimostrare, gli obiettivi che l'utente può sperimentare, le funzionalità da esplorare e tutte le informazioni pertinenti per aiutare l'utente a determinare se acquistare l'offerta. In questo campo è possibile immettere fino a 3000 caratteri di testo.

- **Informazioni sull'accesso** (obbligatorio per Azure Resource Manager e per i test drive delle app per la logica): descrivere tutto quello che un cliente deve sapere per poter accedere al test drive e usarlo. Descrivere dettagliatamente uno scenario per l'uso dell'offerta e fornire esattamente tutte le informazioni necessarie al cliente per accedere alle funzionalità durante il test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale dell'utente** (obbligatorio): descrizione dettagliata approfondita dell'esperienza del test drive. Il manuale dell'utente deve trattare tutti gli aspetti che si vogliono far sperimentare al cliente con il test drive e deve fungere da riferimento per qualsiasi domanda del cliente. Il file deve essere in formato pdf e il nome deve essere assegnato (lunghezza massima 255 caratteri) dopo il caricamento.

- **Video** (facoltativo): è possibile caricare video in YouTube o Vimeo e farvi riferimento con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una panoramica delle informazioni che consentono di comprendere al meglio il test drive, tra cui come usare correttamente le funzionalità dell'offerta e gli scenari che ne evidenziano i vantaggi.
  - **Nome** (obbligatorio)
  - **Indirizzo** (obbligatorio, solo YouTube o Vimeo)
  - **Anteprima** (l'immagine deve essere in formato png e con dimensioni 533 x 324 pixel)

Prima di continuare, selezionare **Salva bozza**.

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
- Nella sezione **Note per la certificazione** fornire istruzioni di test al team di certificazione, per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per la comprensione dell'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Si riceverà un messaggio di posta elettronica per far sapere all'utente che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Tornare al Centro per i partner e selezionare **Passa allo stato Live** per pubblicare l'offerta al pubblico (o, se si tratta di un'offerta privata, ai destinatari privati).

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
