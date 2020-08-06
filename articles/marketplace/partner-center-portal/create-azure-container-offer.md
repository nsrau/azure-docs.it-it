---
title: Creare un'offerta per il contenitore di Azure - Azure Marketplace
description: Informazioni su come creare e pubblicare un'offerta per i contenitori in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: ff2745a636d25c429a1a40644757c00b28e2668c
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799175"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Creare un'offerta di contenitore di Azure in Azure Marketplace

Questo articolo descrive come creare e pubblicare un'offerta di contenitori per Azure Marketplace. Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).

2. Nel menu di spostamento a sinistra selezionare **Marketplace commerciale** > **Panoramica**.

3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Contenitore di Azure**.

   ![Menu di spostamento a sinistra.](./media/new-offer-azure-container.png)

> [!TIP]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

### <a name="offer-id-and-alias"></a>ID e alias dell'offerta

Immettere l'ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Se, ad esempio, si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica offerta** mostra una rappresentazione visiva dei passaggi necessari per pubblicare l'offerta, sia completa che in arrivo, e il tempo necessario per completare ogni passaggio.

Questa pagina mostra diversi collegamenti in base allo stato attuale dell'offerta. Ad esempio:

- Se l'offerta è una bozza - [Eliminare un'offerta bozza](update-existing-offer.md#delete-a-draft-offer)
- Se l'offerta è live - [Interrompere la vendita dell'offerta](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima - [Pubblicare](publishing-status.md#publisher-approval)
- Se non è stata completata la disconnessione dell'editore - [Annullare la pubblicazione](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Configurazione dell'offerta

Seguire questa procedura per configurare l'offerta.

### <a name="customer-leads--optional"></a>Lead - facoltativo

Quando si pubblica l'offerta nel marketplace commerciale con il Centro per i partner, è possibile connetterla al sistema Customer Relationship Management (CRM). In questo modo è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime interesse o usa il prodotto.

1. **Selezionare la destinazione a cui si desidera che vengano inviati i lead**. Il Centro per i partner supporta i sistemi CRM seguenti:

   - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Se il sistema CRM non è elencato sopra, usare [Tabella di Azure](commercial-marketplace-lead-management-instructions-azure-table.md) o [Endpoint HTTPS](commercial-marketplace-lead-management-instructions-https.md) per archiviare i dati dei lead, quindi esportarli nel sistema CRM.

2. Connettere l'offerta alla destinazione lead durante la pubblicazione nel Centro per i partner.
3. Verificare che la connessione alla destinazione lead sia configurata correttamente. Dopo la pubblicazione nel Centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si usa l'offerta in anteprima, prima della distribuzione, è anche possibile testare la connessione lead tentando di acquisire l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione lead sia aggiornata, in modo da non perdere i lead.

Di seguito sono riportate alcune risorse aggiuntive per la gestione dei lead:

- [Panoramica della gestione dei lead](commercial-marketplace-get-customer-leads.md)
- [Domande frequenti sulla gestione dei lead](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](../lead-management-for-cloud-marketplace.md#publishing-config-errors)
- PDF [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (assicurarsi che il blocco popup sia disattivato)

Prima di continuare, selezionare **Salva bozza**.

### <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie usate per raggruppare l'offerta nel marketplace e i contratti legali che supportano l'offerta.

#### <a name="category"></a>Category

Selezionare le categorie e le sottocategorie per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta. Selezionare:

- Almeno una delle due categorie, incluse una categoria primaria e una secondaria (facoltativo).
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**.

Vedere l'elenco completo di categorie e sottocategorie nell'elenco delle [procedure](../gtm-offer-listing-best-practices.md)consigliate. I contenitori vengono sempre visualizzati in **contenitori** e quindi nella categoria **Immagini contenitore** .

#### <a name="legal"></a>Note legali

È necessario specificare i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- Usare il contratto standard per il marketplace commerciale Microsoft.
- Specificare i termini e le condizioni.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per il marketplace commerciale Microsoft

Microsoft offre un modello di contratto standard per semplificare le transazioni nel marketplace commerciale. È possibile scegliere di offrire la propria soluzione nel contratto standard, che i clienti devono selezionare e accettare una sola volta. È una buona opzione se non si desidera creare termini e condizioni personalizzati.

Per altre informazioni sul contratto standard, vedere [Contratto standard per il marketplace commerciale Microsoft](../standard-contract.md). È anche possibile scaricare il PDF sul [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assicurarsi che il blocco popup sia disattivato).

Per usare il contratto standard, selezionare * * Usa il contratto standard per il Marketplace commerciale di Microsoft] (.. /standard-contract.md)

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il marketplace commerciale Microsoft, non è possibile usare i termini e le condizioni personalizzati. È possibile offrire la propria soluzione con il contratto standard oppure con i propri termini e condizioni.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Casella di controllo per usare il contratto standard per il marketplace commerciale Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Termini e condizioni personalizzati

Per specificare i termini e le condizioni personalizzati, immetterli nella casella **Termini e condizioni**. In questa casella è possibile immettere una quantità illimitata di caratteri di testo. I clienti devono accettare questi termini prima di poter provare l'offerta.

Prima di passare alla sezione successiva, Presentazione dell'offerta, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

Questa pagina consente di definire i dettagli dell'offerta visualizzati nel marketplace commerciale. Sono inclusi il nome, la descrizione e le immagini dell'offerta.

> [!NOTE]
> Non è necessario usare l'inglese per i dettagli dell'offerta, se la descrizione dell'offerta inizia con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile inserire un collegamento utile ai contenuti dell'offerta in una lingua diversa da quella usata nei dettagli di presentazione dell'offerta.

### <a name="name"></a>Nome

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo viene precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. È possibile modificarlo in seguito.

Il nome:

- Può essere un marchio registrato ed è possibile includere i simboli di marchio o copyright.
- La sua lunghezza non può superare i 50 caratteri.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati di ricerca

Breve descrizione dell'offerta. Può avere una lunghezza di massimo 100 caratteri e viene usato nei risultati di ricerca nel marketplace.

### <a name="long-summary"></a>Riepilogo lungo

Descrizione più dettagliata dell'offerta. Può avere una lunghezza di massimo 256 caratteri e viene usato nei risultati di ricerca nel marketplace.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Collegamento dell'Informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. L'utente deve garantire che l'offerta sia conforme alle leggi e alle normative sulla privacy. L'utente è anche responsabile della pubblicazione di una valida informativa sulla privacy nel sito Web.

#### <a name="useful-links"></a>Collegamenti utili

Specificare altri documenti online sull'offerta. È possibile aggiungere fino a 25 collegamenti. Per aggiungere un collegamento, selezionare **+ Aggiungi un collegamento**, quindi completare i campi seguenti:

- **Titolo**: i clienti lo visualizzeranno nella pagina dei dettagli dell'offerta.
- **Collegamento (URL)** : immettere un collegamento affinché i clienti possano visualizzare il documento online. Il collegamento deve iniziare con http:// o https://.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto supporto tecnico** e **Contatto tecnico**. Queste informazioni non vengono visualizzate dai clienti, ma sono disponibili per Microsoft. Possono essere visibili anche per i partner di Cloud Solution Provider (CSP).

- Contatto supporto tecnico (obbligatorio) Per domande di supporto generiche.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto programma CSP (facoltativo): Per domande destinate al rivenditore relative al programma CSP.

Nella sezione **Contatto supporto tecnico** specificare il **sito Web di supporto** dove i partner possono trovare il supporto per l'offerta in base al fatto che l'offerta sia disponibile in Azure globale, in Azure per enti pubblici o in entrambi.

Nella sezione **Contatto programma CSP** specificare il collegamento (**materiale di marketing del programma CSP**) in cui i partner CSP possono trovare materiali di marketing per l'offerta.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

Per altre informazioni sulla creazione della presentazione dell'offerta, vedere [Procedure consigliate per le inserzioni di offerte](../gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfuocate verranno rifiutate.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo dello store

Fornire un file PNG per il logo di **grandi** dimensioni (compreso tra 216 x 216 e 350 x 350 pixel). Il centro per i partner lo utilizzerà per creare un logo di dimensioni **ridotte** (48 x 48 pixel) e un valore **medio** (90 x 90 pixel). Facoltativamente, è possibile sostituirli con immagini diverse.

Tutte e tre le dimensioni del logo sono necessarie per l'uso in posizioni diverse nell'elenco:

- **Piccola** (48 x 48)
- **Media** (90 x 90)
- **Grande** (compreso tra 216 x 216 e 350 x 350)

[!INCLUDE [Logo suggestions](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Screenshot (facoltativo)

Aggiungere fino a cinque screenshot che illustrano il funzionamento dell'offerta. Ogni screenshot deve avere dimensioni pari a 1280 x 720 pixel ed essere in formato PNG.

#### <a name="videos-optional"></a>Video (facoltativi)

Aggiungere fino a cinque video che descrivono l'offerta. Immettere il nome del video, l'indirizzo Web e un'immagine PNG di anteprima del video con dimensioni pari a 1280 x 720 pixel.

#### <a name="offer-examples"></a>Esempi di offerta

Gli esempi seguenti mostrano come vengono visualizzati i campi della presentazione dell'offerta in posizioni diverse.

Questa immagine mostra la pagina **Presentazione dell'offerta** in Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Pagina di presentazione dell'offerta in Azure Marketplace." :::

Questa immagine mostra i risultati della ricerca in Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Risultati della ricerca in Azure Marketplace.":::

Questa immagine mostra la pagina **Presentazione dell'offerta** nel portale di Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Pagina Presentazione dell'offerta nel portale di Azure.":::

Questa immagine mostra i risultati della ricerca nel portale di Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Risultati della ricerca nel portale di Azure.":::

## <a name="preview"></a>Anteprima

Nella scheda Anteprima è possibile scegliere un **gruppo di destinatari in anteprima** limitato per convalidare l'offerta prima di pubblicarla.

> [!IMPORTANT]
> Dopo aver visualizzato l'offerta in **Anteprima**, è necessario selezionare **Pubblicazione** per pubblicare l'offerta.

Specificare il gruppo di destinatari dell'anteprima usando i GUID dell'ID di sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi viene visualizzato dai clienti.

> [!NOTE]
> È possibile trovare l'ID della sottoscrizione di Azure nella pagina Sottoscrizioni del portale di Azure.

Aggiungere almeno un ID della sottoscrizione di Azure, singolarmente (fino a 10) o caricando un file CSV (fino a 100). Aggiungendo questi ID della sottoscrizione, si determina chi può visualizzare l'anteprima dell'offerta prima che venga pubblicata. Se l'offerta è già stata pubblicata, è possibile scegliere un gruppo di destinatari per l'anteprima per testare le modifiche o gli aggiornamenti dell'offerta.

Prima di continuare, selezionare **Salva bozza**.

### <a name="plan-overview"></a>Panoramica del piano

Questa scheda consente di specificare diverse opzioni per il piano all'interno della stessa offerta. I piani (in precedenza denominati SKU) possono variare in termini di cloud disponibili, ad esempio cloud globali, cloud governativi e immagine a cui fa riferimento il piano. Per presentare l'offerta nel marketplace commerciale, è necessario configurare almeno un piano.

Dopo aver creato i piani, la scheda **Panoramica del piano** mostra:

- Nomi dei piani
- Modello di prezzi
- Aree di Azure (globale o per enti pubblici)
- Stato attuale di pubblicazione
- Eventuali azioni disponibili

Le azioni disponibili in Panoramica del piano variano a seconda dello stato attuale del piano. Tali impostazioni includono:

- **Elimina bozza**: se lo stato del piano è Bozza.
- **Stop sell Plan** (Interrompi la vendita del piano): se lo stato del piano è pubblicato.

#### <a name="create-new-plan"></a>Creare un nuovo piano

Selezionare **Crea nuovo piano**. Viene visualizzata la finestra di dialogo **Nuovo piano**.

Nella casella **ID piano** creare un identificatore per il piano univoco per ogni piano dell'offerta. Questo ID sarà visibile ai clienti nell'indirizzo Web del prodotto. Usare solo lettere minuscole, numeri, trattini o caratteri di sottolineatura e fino a un massimo di 50 caratteri.

> [!NOTE]
> Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.

Nella casella **Nome del piano** immettere un nome per il piano. I clienti visualizzano il nome quando decidono quale piano scegliere nell'offerta. Creare un nome univoco per ogni piano dell'offerta. Ad esempio, è possibile usare il nome dell'offerta **Windows Server** con i piani **Windows Server 2016** e **Windows Server 2019**.

### <a name="plan-setup"></a>Configurazione del piano

Questa scheda consente di scegliere i cloud in cui è disponibile il piano. Le risposte in questa scheda determinano i campi che vengono visualizzati nelle altre schede.

#### <a name="azure-regions"></a>Aree di Azure

Tutti i piani per le offerte di contenitori di Azure vengono resi disponibili automaticamente in **Azure Global**.  Il piano può essere usato dai clienti in tutte le aree di Azure globali che usano il Marketplace commerciale. Per informazioni dettagliate, vedere [Disponibilità geografica e supporto delle valute](../marketplace-geo-availability-currencies.md).

Selezionare l'opzione [Azure Government](../../azure-government/documentation-government-welcome.md) per fare in modo che la soluzione venga visualizzata qui. Si tratta di un Government Community Cloud con accesso controllato per i clienti delle agenzie governative federali, statali e locali degli Stati Uniti e partner idonei a servire questi enti. In qualità di editore, l'utente è responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate per questa community cloud. Azure per enti pubblici usa data center e reti fisicamente isolate, situate solo negli Stati Uniti. Prima della [pubblicazione](../../azure-government/documentation-government-manage-marketplace-partners.md) in Azure per enti pubblici, testare e confermare la soluzione in tale area, perché i risultati potrebbero essere diversi. Per creare e testare la soluzione, richiedere un account di prova dalla [valutazione di prova di Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo che il piano è stato pubblicato e disponibile in un'area specifica, non è possibile rimuovere tale area.

#### <a name="azure-government-certifications"></a>Certificazioni di Azure per enti pubblici

Questa opzione può essere visualizzata solo se **Azure per enti pubblici** è selezionato in **aree di Azure**.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e regolamenti governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.

Per visualizzare le certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti che li descrivono. Questi possono essere collegamenti diretti alle presentazioni del programma o al proprio sito Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

## <a name="plan-listing"></a>Elenco piani

Questa scheda contiene informazioni specifiche per ogni piano diverso all'interno dell'offerta attuale.

### <a name="plan-name"></a>Nome del piano

Questo campo è precompilato con il nome assegnato al piano al momento della creazione. È possibile modificare il nome in base alle esigenze. Non può superare la lunghezza di 50 caratteri. Il nome viene visualizzato come titolo del piano in Azure Marketplace e nel portale di Azure. Viene usato come nome del modulo predefinito quando il piano è pronto per essere usato.

### <a name="plan-summary"></a>Riepilogo del piano

Breve riepilogo del piano software (non dell'offerta). Questo riepilogo viene visualizzato nei risultati della ricerca di Azure Marketplace e può contenere fino a 100 caratteri.

### <a name="plan-description"></a>Descrizione del piano

Descrivere che cosa rende unico questo piano software, insieme alle differenze tra i piani all'interno dell'offerta. Non descrivere l'offerta, ma solo il piano. Questa descrizione verrà visualizzata in Azure Marketplace e nel portale di Azure nella pagina **Presentazione dell'offerta**. Può essere identico al contenuto inserito nel riepilogo del piano e contenere fino a 2.000 caratteri.

Selezionare **Salva** dopo aver completato questi campi.

#### <a name="plan-examples"></a>Esempi di piano

Gli esempi seguenti mostrano come vengono visualizzati i campi di Elenco piani in viste diverse.

Questa immagine illustra i campi di Azure Marketplace quando si visualizzano i dettagli del piano:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Campi visualizzati nei dettagli del piano in Azure Marketplace.":::

Questa immagine illustra i dettagli del piano nel portale di Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Dettagli del piano nel portale di Azure.":::

## <a name="plan-availability"></a>Disponibilità del piano

Se si vuole nascondere l'offerta pubblicata in modo che i clienti non possano cercarla, vederla o acquistarla nel marketplace, selezionare la casella di controllo **Nascondi piano** nella scheda **Disponibilità**.

Questo campo viene usato quando:

- L'offerta deve essere utilizzata indirettamente quando si fa riferimento a un'altra applicazione.
- L'offerta non deve essere acquistata singolarmente.
- Il piano è stato usato per il test iniziale e non è più pertinente.
- Il piano è stato usato per le offerte temporanee o stagionali e non deve essere più disponibile.

## <a name="technical-configuration"></a>Configurazione tecnica

Le immagini del contenitore devono essere ospitate in un [Registro Azure Container](https://azure.microsoft.com/services/container-registry/) privato. Nella scheda **Configurazione tecnica** specificare le informazioni di riferimento per il repository dell'immagine del contenitore all'interno del Registro Azure Container.

Dopo aver pubblicato l'offerta, l'immagine del contenitore viene copiata in Azure Marketplace in un registro contenitori pubblico specifico. Tutte le richieste di usare l'immagine del contenitore vengono gestite dal registro contenitori pubblico di Azure Marketplace, non da quello privato. Per informazioni dettagliate, vedere [Preparare gli asset tecnici di Azure Container](create-azure-container-technical-assets.md).

### <a name="image-repository-details"></a>Dettagli del repository di immagini

Specificare le informazioni seguenti nella scheda **Image repository details** (Dettagli del repository di immagini).

**ID della sottoscrizione di Azure**: specificare l'ID della sottoscrizione in cui viene segnalato l'utilizzo e i servizi vengono fatturati per Registro Azure Container che include l'immagine del contenitore. È possibile trovare questo ID nella [pagina Sottoscrizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure.

**Nome del gruppo di risorse di Azure**: specificare il [nome del gruppo di risorse](../../azure-resource-manager/management/manage-resource-groups-portal.md) che contiene Registro Azure Container con l'immagine del contenitore. Il gruppo di risorse deve essere accessibile nell'ID della sottoscrizione (sopra). È possibile trovare il nome nella pagina [Gruppi di risorse ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) del portale di Azure.

**Nome del Registro Azure Container**: specificare il nome del [Registro Azure Container](../../container-registry/container-registry-intro.md) con l'immagine del contenitore. Il registro contenitori deve trovarsi nel gruppo di risorse di Azure specificato in precedenza. Includere solo il nome del registro, non il nome completo del server di accesso. Assicurarsi di non inserire **azurecr.io** nel nome. È possibile trovare il nome del registro nella pagina [Registri contenitori](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) del portale di Azure.

**Nome utente amministratore per il container Registry di Azure** : specificare il [nome utente amministratore](../../container-registry/container-registry-authentication.md#admin-account)) collegato al container Registry di Azure con l'immagine del contenitore. Il nome utente e la password sono necessari per assicurarsi che l'azienda abbia accesso al registro. Per ottenere il nome utente e la password dell'amministratore, impostare la proprietà **admin-enabled** su **True** usando l'interfaccia della riga di comando di Azure. Facoltativamente è possibile impostare **Utente amministratore** su **Abilita** nel portale di Azure.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Finestra di dialogo Aggiorna registro contenitori.":::

**Password per il Registro Azure Container**: specificare la password per il nome utente dell'amministratore associata al Registro Azure Container con l'immagine del contenitore. Il nome utente e la password sono necessari per assicurarsi che l'azienda abbia accesso al registro. Per ottenere la password nel portale di Azure, passare a **Registro contenitori** > **Chiavi di accesso** o con l'interfaccia della riga di comando di Azure usare il [comando show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Menu Chiave di accesso.":::

**Nome del repository all'interno del Registro Azure Container**. Specificare il nome del repository di Registro Azure Container con l'immagine. Includere il nome del repository quando si esegue il push dell'immagine nel registro. È possibile trovare il nome del repository passando alla pagina [Registro contenitori](https://azure.microsoft.com/services/container-registry/) > **Repository**. Per altre informazioni, vedere [Visualizzare repository di registri contenitori di Azure nel portale di Azure](../../container-registry/container-registry-repositories.md).

> [!NOTE]
> Dopo averlo impostato, il nome non potrà essere modificato. Usare un nome univoco per ogni offerta nell'account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tag dell'immagine per le nuove versioni dell'offerta

I clienti devono poter ottenere automaticamente gli aggiornamenti da Azure Marketplace quando si pubblica un aggiornamento. Se non vogliono ricevere l'aggiornamento, devono poter mantenere una versione specifica dell'immagine. A tale scopo, è possibile aggiungere nuovi tag dell'immagine ogni volta che si aggiorna l'immagine.

### <a name="image-tag"></a>Tag dell'immagine

Questo campo deve includere un tag **ultima versione** che punti alla versione più recente dell'immagine in tutte le piattaforme supportate. Deve includere anche un tag di versione, ad esempio, deve iniziare con xx.xx.xx, dove xx rappresenta un numero. I clienti devono usare [tag di manifesto](https://github.com/estesp/manifest-tool) per specificare come destinazione più piattaforme. È necessario aggiungere anche tutti i tag referenziati da un tag di manifesto per poterli caricare.

Tutti i tag di manifesto, tranne il tag ultima versione, devono iniziare con X.Y **-** o X.Y.Z, dove X, Y e Z sono numeri interi. Se ad esempio un tag **ultima versione** punta a 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, questi sei tag devono essere aggiunti a questo campo. Per informazioni dettagliate, vedere [Preparare gli asset tecnici di Azure Container](create-azure-container-technical-assets.md).

> [!NOTE]
> Ricordarsi di aggiungere un tag di test all'immagine, in modo da poterla identificare durante i test.

## <a name="review-and-publish"></a>Rivedi e pubblica

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, è possibile inviarle per la revisione e la pubblicazione.

Nell'angolo in alto a destra del portale selezionare **Rivedi e** **pubblica**.

Nella pagina di revisione è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta. Non è possibile eseguire la pubblicazione fino a quando tutte le sezioni dell'offerta non vengono contrassegnate come complete.
  - **Non avviata**: non è stata avviata e deve essere completata.
  - **Incompleta**: la sezione contiene errori che devono essere corretti o è necessario inserire altre informazioni. Per informazioni, vedere le sezioni precedenti di questo documento.
  - **Completata**: contiene tutti i dati necessari senza errori. Tutte le sezioni dell'offerta devono essere completate prima di poter inviare l'offerta.
- Inviare al team di certificazione istruzioni di test per assicurarsi che l'offerta venga testata correttamente. Specificare anche eventuali note supplementari utili per comprendere l'offerta.

Per inviare l'offerta per la pubblicazione, selezionare **Pubblica**.

Si riceverà un messaggio di posta elettronica per far sapere all'utente che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione.

Per pubblicare l'offerta al pubblico, passare a centro per i partner e selezionare **Go-Live**.

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel marketplace commerciale](update-existing-offer.md)
