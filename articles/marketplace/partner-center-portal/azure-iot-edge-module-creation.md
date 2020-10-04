---
title: Creare un'offerta del modulo Azure IoT Edge con il Centro per i partner in Azure Marketplace
description: Informazioni su come creare, configurare e pubblicare un'offerta di modulo IoT Edge in Azure Marketplace tramite il centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 5b823b0429feffa87a05faadc78574287bc9b4a4
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708340"
---
# <a name="create-an-iot-edge-module-offer"></a>Creare un'offerta di moduli IoT Edge

Questo articolo descrive come creare e pubblicare un'offerta del modulo IoT Edge per Azure Marketplace. Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Modulo IoT Edge**.

    ![Menu di spostamento di sinistra.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner vengono visualizzate solo negli archivi online dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo aver apportato modifiche.

### <a name="offer-id-and-alias"></a>ID e alias dell'offerta

Immettere l'ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica offerta** mostra una rappresentazione visiva dei passaggi necessari per pubblicare l'offerta, sia completa che in arrivo, e il tempo necessario per completare ogni passaggio.

Questa pagina include i collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è una bozza: [Eliminare un'offerta bozza](update-existing-offer.md#delete-a-draft-offer)
- Se l'offerta è live: [Interrompere la vendita dell'offerta](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima: [Passare allo stato Live](publishing-status.md#publisher-approval)
- Se non è stata completata la disconnessione dell'editore: [Annullare la pubblicazione](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Configurazione dell'offerta

Per configurare l'offerta, seguire questa procedura.

### <a name="customer-leads"></a>Clienti potenziali

Quando si pubblica l'offerta nel marketplace con il Centro per i partner, è possibile connetterla al sistema Customer Relationship Management (CRM). In questo modo, è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime interesse o usa il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il Centro per i partner supporta i sistemi CRM seguenti:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se il sistema CRM non è incluso nell'elenco, usare [Tabella di Azure](commercial-marketplace-lead-management-instructions-azure-table.md) o [Endpoint HTTPS](commercial-marketplace-lead-management-instructions-https.md) per archiviare i dati dei clienti potenziali e quindi esportarli nel sistema CRM.

2. Quando si pubblica l'offerta nel Centro per i partner, connettere l'offerta alla destinazione del cliente potenziale.
3. Verificare che la connessione alla destinazione del cliente potenziale sia configurata correttamente. Dopo la pubblicazione nel Centro per i partner, la connessione verrà convalidata e verrà inviato un cliente potenziale di prova. Quando si visualizza l'offerta in anteprima prima della pubblicazione, è anche possibile testare la connessione dei clienti potenziali tentando di acquistare l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione dei clienti potenziali rimanga aggiornata, in modo da non perdere alcun cliente potenziale.

Di seguito sono elencate alcune risorse aggiuntive per la gestione dei clienti potenziali:

- [Lead di clienti tramite l'offerta del marketplace commerciale](commercial-marketplace-get-customer-leads.md)
- [Domande frequenti sulla gestione dei lead](../lead-management-faq.md#common-questions-about-lead-management)
- [Risoluzione degli errori di configurazione del lead](../lead-management-faq.md#publishing-config-errors)
- PDF [Panoramica della gestione dei clienti potenziali](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (assicurarsi che il blocco dei popup sia disattivato)

Prima di continuare, selezionare **Salva bozza**.

### <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie usate per raggruppare l'offerta nel marketplace e i contratti legali che supportano l'offerta.

#### <a name="category"></a>Category

Selezionare le categorie e le sottocategorie per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta. Selezionare:

- Almeno una delle due categorie, incluse una categoria primaria e una secondaria (facoltativo).
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**.

Vedere l'elenco completo di categorie e sottocategorie nell'elenco delle [procedure](../gtm-offer-listing-best-practices.md)consigliate. Nel Marketplace i moduli IOT Edge vengono sempre visualizzati nella categoria **Internet delle cose**  >  **IOT Edge modulo**   .

#### <a name="legal"></a>Note legali

È necessario specificare i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- Usare il contratto standard per il marketplace commerciale Microsoft.
- Specificare i termini e le condizioni.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per il marketplace commerciale Microsoft

Microsoft offre un modello di contratto standard per semplificare le transazioni nel marketplace commerciale. È possibile scegliere di offrire la propria soluzione nel contratto standard, che i clienti devono selezionare e accettare una sola volta. È una buona opzione se non si vogliono creare termini e condizioni personalizzati.

Per altre informazioni sul contratto standard, vedere [Contratto standard per il marketplace commerciale Microsoft](../standard-contract.md). È anche possibile scaricare il documento pdf sul [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assicurarsi che il blocco popup sia disattivato).

Per usare il contratto standard, selezionare la casella di controllo **Use the Standard Contract for Microsoft's commercial marketplace** (Usa il contratto standard per il marketplace commerciale Microsoft) e quindi fare clic su **Accetta**.

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il marketplace commerciale Microsoft, non è possibile usare i termini e le condizioni personalizzati. È possibile offrire la propria soluzione con il contratto standard oppure con termini e condizioni personalizzati.

![Casella di controllo per usare il contratto standard per il marketplace commerciale Microsoft.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Termini e condizioni personalizzati

Per specificare i termini e le condizioni personalizzati, immetterli nella casella **Termini e condizioni**. In questa casella è possibile immettere una quantità illimitata di caratteri di testo. I clienti devono accettare questi termini prima di poter provare l'offerta.

Prima di passare alla sezione successiva, Presentazione dell'offerta, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

È possibile definire i dettagli dell'offerta visualizzati nel marketplace. È possibile specificare il nome dell'offerta, la descrizione, le immagini e così via. Durante la configurazione dell'offerta, assicurarsi di seguire i criteri descritti in dettaglio nella pagina dei criteri di Microsoft.

> [!NOTE]
> Se la descrizione dell'offerta inizia con la frase "Questa applicazione è disponibile solo in [lingua non inglese]", non è necessario usare l'inglese per i dettagli dell'offerta. È anche possibile inserire un collegamento utile ai contenuti dell'offerta in una lingua diversa da quella usata nei dettagli di presentazione dell'offerta.

### <a name="name"></a>Nome

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo viene precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. È possibile modificarlo in seguito.

Il nome:

- Può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright).
- Non può avere una lunghezza superiore a 50 caratteri.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Specificare una breve descrizione dell'offerta. Può avere una lunghezza massima di 100 caratteri e viene usato nei risultati di ricerca nel marketplace.

### <a name="long-summary"></a>Riepilogo lungo

Specificare una descrizione più dettagliata dell'offerta. Può avere una lunghezza massima di 256 caratteri e viene usato nei risultati di ricerca nel marketplace.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

Le offerte del modulo IoT Edge devono includere un paragrafo sui requisiti hardware minimi nella parte inferiore della descrizione, ad esempio:

- Requisiti hardware minimi: sistema operativo Linux x64 e arm32, 1 GB di RAM, risorsa di archiviazione da 500 MB

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. L'utente deve garantire che l'offerta sia conforme alle leggi e alle normative sulla privacy. L'utente è anche responsabile della pubblicazione di una valida informativa sulla privacy nel sito Web.

#### <a name="useful-links"></a>Collegamenti utili

Specificare altri documenti online sull'offerta. È possibile aggiungere fino a 25 collegamenti. Per aggiungere un collegamento, selezionare **+ Aggiungi un collegamento** e quindi completare i campi seguenti:

- **Titolo**: i clienti visualizzeranno il titolo nella pagina dei dettagli dell'offerta.
- **Collegamento (URL)** : immettere un collegamento affinché i clienti possano visualizzare il documento online. Il collegamento deve iniziare con `http://` o `https://` .

Assicurarsi di aggiungere almeno un collegamento alla documentazione e un collegamento ai dispositivi IoT Edge compatibili dal  [catalogo di dispositivi Azure IoT](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Informazioni contatto

È necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto supporto tecnico** e **Contatto tecnico**. Queste informazioni non vengono visualizzate dai clienti. Sono disponibili per Microsoft e possono essere fornite ai partner CSP (Cloud Solution Provider).

- Contatto supporto tecnico (obbligatorio): per domande di supporto generiche.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto programma CSP (facoltativo): per domande destinate al rivenditore relative al programma CSP.

Nella sezione **Contatto supporto tecnico** specificare l'indirizzo Web del **sito Web di supporto** dove i partner possono trovare il supporto per l'offerta a seconda che sia disponibile in Azure globale, Azure per enti pubblici o entrambi.

Nella sezione **Contatto programma CSP** specificare il collegamento (**materiale di marketing del programma CSP**) in cui i partner CSP possono trovare materiali di marketing per l'offerta.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

Per altre informazioni sulla creazione della presentazione dell'offerta, vedere [Procedure consigliate per le inserzioni di offerte](../gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfuocate verranno rifiutate.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo dello store

Fornire un file PNG per il logo di **grandi** dimensioni. Il centro per i partner lo utilizzerà per creare un logo **piccolo** e **medio** . Facoltativamente, è possibile sostituirli con immagini diverse in un secondo momento.

- **Grande** (da 216 x 216 a 350 x 350 px, obbligatorio)
- **Media** (90 x 90 px, facoltativo)
- **Piccolo** (48 x 48 px, facoltativo)

Questi logo vengono usati in posizioni diverse nell'elenco:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Screenshot (facoltativo)

Aggiungere fino a cinque screenshot che illustrano il funzionamento dell'offerta. Ogni screenshot deve avere dimensioni pari a 1280 x 720 pixel ed essere in formato PNG.

#### <a name="videos-optional"></a>Video (facoltativi)

Aggiungere fino a cinque video che descrivono l'offerta. Immettere il nome del video, l'indirizzo Web e un'immagine PNG di anteprima del video con dimensioni pari a 1280 x 720 pixel.

#### <a name="marketplace--examples"></a>Esempi di Marketplace

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo grande
2. Categorie
3. Indirizzo di supporto (collegamento)
4. Termini e condizioni
5. Indirizzo privacy policy (collegamento)
6. Name
7. Riepilogo
8. Descrizione
9. Collegamenti utili
10. Screenshot/video

<br>Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta nei risultati della ricerca in Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo piccolo
2. Nome offerta
3. Riepilogo dei risultati della ricerca

<br>Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta nell'portale di Azure:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Name
2. Descrizione
3. Collegamenti utili
4. Schermate

<br>Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta nei risultati della ricerca portale di Azure:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo piccolo
2. Nome offerta
3. Riepilogo dei risultati della ricerca

<br>Prima di passare alla sezione successiva, Anteprima, selezionare **Salva bozza**.

## <a name="preview"></a>Anteprima

Nella scheda **Anteprima** è possibile scegliere un **gruppo di destinatari in anteprima** limitato, per convalidare l'offerta prima di pubblicarla per i destinatari del marketplace.

> [!IMPORTANT]
> Dopo aver visualizzato l'offerta in Anteprima, è necessario selezionare **Passa allo stato Live** per pubblicare l'offerta.

Specificare il gruppo di destinatari dell'anteprima usando i GUID dell'ID di sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi viene visualizzato dai clienti.

> [!NOTE]
> È possibile trovare l'ID della sottoscrizione di Azure nella pagina Sottoscrizioni del portale di Azure.

Aggiungere almeno un ID della sottoscrizione di Azure, singolarmente (fino a 10) o caricando un file con estensione csv (fino a 100). Aggiungendo questi ID della sottoscrizione, si definisce chi può visualizzare l'anteprima dell'offerta prima che venga pubblicata. Se l'offerta è già stata pubblicata, è possibile definire un gruppo di destinatari per l'anteprima per testare le modifiche o gli aggiornamenti dell'offerta.

Prima di passare alla sezione successiva, Panoramica del piano, selezionare **Salva bozza**.

## <a name="plan-overview"></a>Panoramica del piano

Questa scheda consente di specificare diverse opzioni per il piano all'interno della stessa offerta nel Centro per i partner. I piani (in precedenza denominati SKU) possono variare in termini di cloud disponibili, ad esempio cloud globali, cloud governativi e immagine a cui fa riferimento il piano. Per presentare l'offerta nel marketplace, è necessario configurare almeno un piano.

È possibile creare fino a 100 piani per ogni offerta: fino a 45 di questi possono essere privati. Scopri di più sui piani privati in [offerte private in Microsoft Commercial Marketplace](../private-offers.md).

Dopo aver creato i piani, la scheda **Panoramica del piano** mostra:

- Nomi dei piani
- Modello di prezzi
- Aree di Azure (globale o per enti pubblici)
- Stato attuale di pubblicazione
- Eventuali azioni disponibili

Le azioni disponibili in Panoramica del piano variano a seconda dello stato attuale del piano. Tali impostazioni includono:

- **Elimina bozza**: se lo stato del piano è Bozza.
- **Stop sell Plan** (Interrompi la vendita del piano): se lo stato del piano è pubblicato.

### <a name="create-new-plan"></a>Creare un nuovo piano

Selezionare **Crea nuovo piano**. Viene visualizzata la finestra di dialogo **Nuovo piano**.

Nella casella **ID piano** creare un ID univoco per ogni piano dell'offerta. Questo ID sarà visibile ai clienti all'indirizzo Web del prodotto. Usare solo lettere minuscole, numeri, trattini o caratteri di sottolineatura e fino a un massimo di 50 caratteri.

Nella casella **Nome del piano** immettere un nome per il piano. I clienti visualizzano il nome quando decidono quale piano scegliere nell'offerta. Creare un nome univoco per ogni piano dell'offerta. Ad esempio, è possibile usare il nome dell'offerta **Windows Server** con i piani **Windows Server 2016** e **Windows Server 2019**.

> [!NOTE]
> Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.

Selezionare **Create** (Crea).

### <a name="plan-setup"></a>Configurazione del piano

Questa scheda consente di configurare i cloud in cui è disponibile il piano. Le risposte in questa scheda determinano i campi che vengono visualizzati nelle altre schede.

#### <a name="azure-regions"></a>Aree di Azure

Tutti i piani per le offerte del modulo IoT Edge vengono resi disponibili automaticamente in **Azure Global**.  Il piano può essere usato dai clienti in tutte le aree di Azure globali che usano il Marketplace. Per informazioni dettagliate, vedere [Disponibilità geografica e supporto delle valute](../marketplace-geo-availability-currencies.md).

Selezionare l'opzione [Azure Government](../../azure-government/documentation-government-welcome.md) per fare in modo che la soluzione venga visualizzata qui. Si tratta di un Government Community Cloud con accesso controllato per i clienti delle agenzie governative federali, statali e locali degli Stati Uniti e partner idonei a servire questi enti. In qualità di editore, l'utente è responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate per questa community cloud. Azure per enti pubblici usa data center e reti fisicamente isolate, situate solo negli Stati Uniti. Prima della [pubblicazione](../../azure-government/documentation-government-manage-marketplace-partners.md) in Azure per enti pubblici, testare e confermare la soluzione in tale area, perché i risultati potrebbero essere diversi. Per preparare e testare la soluzione, richiedere un account di prova dalla [versione di valutazione di Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo che il piano è stato pubblicato e disponibile in un'area specifica, non è possibile rimuovere tale area.

#### <a name="azure-government-certifications"></a>Certificazioni di Azure per enti pubblici

Questa opzione è visibile solo se **Azure per enti pubblici** è selezionato in **aree di Azure**.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e regolamenti governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per rendere consapevoli delle certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti che descrivono tali certificazioni. Questi possono essere collegamenti diretti alle presentazioni del programma o al proprio sito Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

### <a name="plan-listing"></a>Elenco piani

Questa scheda contiene informazioni specifiche per i vari piani all'interno della stessa offerta.

### <a name="plan-name"></a>Nome del piano

Questo campo è precompilato con il nome assegnato al piano al momento della creazione. È possibile modificare il nome in base alle esigenze. Non può superare la lunghezza di 50 caratteri. Questo nome viene visualizzato come titolo del piano in Azure Marketplace e nel portale di Azure. Viene usato come nome del modulo predefinito quando il piano è pronto per essere usato.

### <a name="plan-summary"></a>Riepilogo del piano

Specificare un breve riepilogo del piano (non dell'offerta). Questo riepilogo viene visualizzato nei risultati della ricerca di Azure Marketplace e può contenere fino a 100 caratteri.

### <a name="plan-description"></a>Descrizione del piano

Descrivere le caratteristiche uniche del piano, nonché le differenze tra i piani all'interno dell'offerta. Non descrivere l'offerta, ma solo il piano. Questa descrizione verrà visualizzata in Azure Marketplace e nel portale di Azure nella pagina Presentazione dell'offerta. Può essere identico al contenuto inserito nel riepilogo del piano e contenere fino a 2000 caratteri.

Dopo aver completato questi campi, selezionare **Salva bozza**.

#### <a name="plan-examples"></a>Esempi di piano

Di seguito è riportato un esempio di dettagli dei piani di Azure Marketplace (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Nome offerta
2. Nome piano
3. Descrizione piano

<br>Di seguito è riportato un esempio dei dettagli del piano di portale di Azure (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Nome offerta
2. Nome piano
3. Descrizione piano

### <a name="availability"></a>Disponibilità

Se si vuole nascondere l'offerta pubblicata in modo che i clienti non possano cercarla, vederla o acquistarla nel marketplace, selezionare la casella di controllo **Nascondi piano** nella scheda Disponibilità.

Questo campo viene comunemente usato quando:

- L'offerta deve essere usata solo indirettamente quando si fa riferimento a essa tramite un'altra applicazione.
- L'offerta non deve essere acquistata singolarmente.
- Il piano è stato usato per il test iniziale e non è più pertinente.
- Il piano è stato usato per le offerte temporanee o stagionali e non deve essere più disponibile.

## <a name="technical-configuration"></a>Configurazione tecnica

Il tipo di offerta di **modulo IoT Edge** è un tipo specifico di contenitore in esecuzione in un dispositivo IoT Edge. Nella scheda **configurazione tecnica** verranno fornite informazioni di riferimento per il repository di immagini contenitore all'interno del [container Registry di Azure](https://azure.microsoft.com/services/container-registry/), insieme a impostazioni di configurazione che consentono ai clienti di utilizzare il modulo in modo semplice.

Dopo aver pubblicato l'offerta, l'immagine del contenitore IoT Edge viene copiata in Azure Marketplace in un registro contenitori pubblico specifico. Tutte le richieste degli utenti di Azure per l'uso del modulo vengono gestite dal registro contenitori pubblico di Azure Marketplace, non da quello privato.

È possibile specificare come destinazione più piattaforme e indicare varie versioni dell'immagine del contenitore del modulo tramite tag. Per altre informazioni sui tag e sul controllo delle versioni, vedere [Preparare gli asset tecnici del modulo IoT Edge](create-iot-edge-module-asset.md).

### <a name="image-repository-details"></a>Dettagli del repository di immagini

Specificare le informazioni seguenti nella scheda **Image repository details** (Dettagli del repository di immagini).

**Selezionare l'origine dell'immagine**: selezionare l'opzione **Registro Azure Container**.

**ID della sottoscrizione di Azure**: specificare l'ID della sottoscrizione in cui viene segnalato l'utilizzo della risorsa e i servizi vengono fatturati per Registro Azure Container che include l'immagine del contenitore. È possibile trovare questo ID nella pagina [Sottoscrizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure.

**Nome del gruppo di risorse di Azure**: specificare il [nome del gruppo di risorse](../../azure-resource-manager/management/manage-resource-groups-portal.md) che contiene Registro Azure Container con l'immagine del contenitore. Il gruppo di risorse deve essere accessibile nell'ID della sottoscrizione (sopra). È possibile trovare il nome nella pagina [Gruppi di risorse ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) del portale di Azure.

**Nome del Registro Azure Container**: Specificare il nome del [Registro Azure Container](../../container-registry/container-registry-intro.md) con l'immagine del contenitore. Il registro contenitori deve trovarsi nel gruppo di risorse di Azure specificato in precedenza. Specificare solo il nome del registro, non il nome completo del server di accesso. Assicurarsi di non inserire **azurecr.io** nel nome. È possibile trovare il nome del registro nella pagina [Registri contenitori](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) del portale di Azure.

**Nome utente amministratore per il container Registry di Azure**: specificare il [nome utente amministratore](../../container-registry/container-registry-authentication.md#admin-account)) associato al container Registry di Azure con l'immagine del contenitore. Il nome utente e la password sono necessari per assicurarsi che l'azienda abbia accesso al registro. Per ottenere il nome utente e la password dell'amministratore, impostare la proprietà **admin-enabled** su **True** usando l'interfaccia della riga di comando di Azure (CLI). Facoltativamente è possibile impostare **Utente amministratore** su **Abilita** nel portale di Azure.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-description"></a>Descrizione della chiamata

1. Utente amministratore

<br>**Password del Registro Azure Container**: specificare la password per il nome utente dell'amministratore associata al Registro Azure Container con l'immagine del contenitore. Il nome utente e la password sono necessari per assicurarsi che l'azienda abbia accesso al registro. Per ottenere la password nel portale di Azure, passare a **Registro contenitori** > **Chiavi di accesso** o con l'interfaccia della riga di comando di Azure usare il [comando show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Chiavi di accesso
2. Username
3. Password

**Nome del repository all'interno del Registro Azure Container**. Specificare il nome del repository di Registro Azure Container con l'immagine. Specificare il nome del repository quando si esegue il push dell'immagine nel registro. È possibile trovare il nome del repository passando alla pagina [Registro contenitori](https://azure.microsoft.com/services/container-registry/) > **Repository**. Per altre informazioni, vedere [Visualizzare i repository del registro contenitori nel portale di Azure](../../container-registry/container-registry-repositories.md). Dopo averlo impostato, il nome non potrà essere modificato. Usare un nome univoco per ogni offerta nell'account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tag dell'immagine per le nuove versioni dell'offerta

I clienti devono poter ottenere automaticamente gli aggiornamenti da Azure Marketplace quando si pubblica un aggiornamento. Se non vogliono ricevere l'aggiornamento, devono poter mantenere una versione specifica dell'immagine. A tale scopo, è possibile aggiungere nuovi tag dell'immagine ogni volta che si aggiorna l'immagine.

**Tag dell'immagine**. Questo campo deve includere un tag **ultima versione** che punti alla versione più recente dell'immagine in tutte le piattaforme supportate. Deve includere anche un tag di versione, ad esempio, deve iniziare con xx.xx.xx, dove xx rappresenta un numero. I clienti devono usare [tag di manifesto](https://github.com/estesp/manifest-tool) per specificare come destinazione più piattaforme. È necessario aggiungere anche tutti i tag referenziati da un tag di manifesto per poterli caricare. Tutti i tag di manifesto, tranne il tag ultima versione, devono iniziare con X.Y o X.Y.Z, dove X, Y e Z sono numeri interi. Se, ad esempio, un tag ultima versione punta a 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, questi sei tag devono essere aggiunti a questo campo. Per informazioni dettagliate sui tag e sul controllo delle versioni, vedere [Preparare gli asset tecnici del modulo IoT Edge](create-iot-edge-module-asset.md).

### <a name="default-deployment-settings-optional"></a>Impostazioni di distribuzione predefinite (facoltativo)

Definire le impostazioni più comuni per distribuire il modulo IoT Edge. Ottimizzare le distribuzioni dei clienti permettendo loro di avviare il modulo IoT Edge con queste impostazioni predefinite.

**Route predefinite**. L'hub di IoT Edge gestisce la comunicazione tra i moduli, l'hub IoT e i dispositivi. È possibile impostare route per l'input e l'output dei dati tra i moduli e l'hub IoT, per inviare i messaggi alla destinazione desiderata senza dover usare servizi aggiuntivi per l'elaborazione dei messaggi e senza scrivere codice aggiuntivo. Le route vengono costruite mediante coppie nome/valore. È possibile definire fino a cinque nomi di route predefiniti, ognuno con una lunghezza di 512 caratteri.

Assicurarsi di usare la sintassi di [Route](../../iot-edge/module-composition.md#declare-routes)corretta nel valore di route (in genere definito da/Message/* in $upstream). Ciò significa che tutti i messaggi inviati dai moduli passano all'hub IoT. Per fare riferimento al modulo, usarne il nome predefinito, che sarà il **nome dell'offerta** senza spazi e caratteri speciali. Per fare riferimento ad altri moduli non ancora noti, usare la convenzione <FROM_MODULE_NAME> per indicare ai clienti che devono aggiornare queste informazioni. Per informazioni dettagliate sulle route IoT Edge, vedere [dichiarare le route](../../iot-edge/module-composition.md#declare-routes)).

Ad esempio, se il modulo ContosoModule è in ascolto di dati di input in ContosoInput e di output in ContosoOutput, è opportuno definire le due route predefinite seguenti:

- Nome 1: ToContosoModule
- Valore 1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nome 2: FromContosoModuleToCloud
- Valore 2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Proprietà desiderate predefinite del modulo gemello**. Un modulo gemello è un documento Json contenuto nell'hub IoT in cui vengono archiviate le informazioni di stato relative a un'istanza del modulo, incluse le proprietà desiderate. Le proprietà desiderate vengono usate insieme alle proprietà segnalate per sincronizzare la configurazione o le condizioni del modulo. Il back-end della soluzione è in grado di impostare le proprietà desiderate e il modulo è in grado di leggerle. Il modulo può anche ricevere notifiche relative alle modifiche apportate alle proprietà desiderate. Le proprietà desiderate vengono create usando fino a cinque coppie nome/valore e ogni valore predefinito deve essere composto da meno di 512 caratteri. È possibile definire fino a cinque proprietà nome/valore desiderate del dispositivo gemello. I valori delle proprietà desiderate del dispositivo gemello devono essere un JSON valido, non preceduto da carattere di escape, senza matrici e con una gerarchia annidata massima pari a quattro livelli. In uno scenario in cui un parametro obbligatorio per un valore predefinito non ha senso (ad esempio, l'indirizzo IP del server di un cliente), è possibile aggiungere un parametro come valore predefinito. Per altre informazioni sulle proprietà desiderate del dispositivo gemello, vedere [definire o aggiornare le proprietà desiderate](../../iot-edge/module-composition.md#define-or-update-desired-properties).

Se, ad esempio, un modulo supporta una frequenza di aggiornamento configurabile in modo dinamico tramite le proprietà desiderate del dispositivo gemello, è opportuno definire le proprietà desiderate del dispositivo gemello predefinite seguenti:

- Nome 1: RefreshRate
- Valore 1: 60

**Variabili di ambiente predefinite**. Le variabili di ambiente forniscono a un modulo informazioni aggiuntive che semplificano il processo di configurazione. Le variabili di ambiente vengono create usando le coppie nome/valore. Ogni nome e valore predefinito della variabile di ambiente deve essere composto da meno di 512 caratteri ed è possibile definirne fino a cinque. Quando un parametro obbligatorio per un valore predefinito non ha senso (ad esempio, l'indirizzo IP del server di un cliente), è possibile aggiungere un parametro come valore predefinito.

Se ad esempio un modulo richiede di accettare le condizioni d'uso prima dell'avvio, è possibile definire la variabile di ambiente seguente:

- Nome 1: ACCEPT_EULA
- Valore 1: S

**Default container create options** (Opzioni di creazione del contenitore predefinito). Le opzioni di creazione del contenitore guidano la creazione del contenitore Docker del modulo IoT Edge. IoT Edge supporta le opzioni del contenitore per la creazione dell'API del motore Docker. Vedere tutte le opzioni in [List containers](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) (Elencare i contenitori). Il campo Crea opzioni deve essere JSON valido, non di escape e inferiore a 512 caratteri.

Se, ad esempio, un modulo richiede l'associazione della porta, è possibile definire le opzioni di creazione seguenti:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Rivedere e pubblicare

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, è possibile inviarle per la revisione e la pubblicazione.

Nell'angolo in alto a destra del portale selezionare **Rivedi e pubblica**.

Nella pagina di revisione è possibile visualizzare lo stato di pubblicazione:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta. Non è possibile eseguire la pubblicazione fino a quando tutte le sezioni dell'offerta non vengono contrassegnate come complete.
    - **Non avviata**: la sezione non è stata avviata e deve essere completata.
    - **Incompleta**: la sezione contiene errori che devono essere corretti o è necessario inserire altre informazioni. Per indicazioni, vedere le sezioni precedenti di questo documento.
    - **Completa**: la sezione contiene tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere completate prima dell'invio.
- Inviare al team di certificazione istruzioni di test per assicurarsi che l'offerta venga testata correttamente. Specificare anche eventuali note supplementari utili per comprendere l'offerta.

Per inviare l'offerta per la pubblicazione, selezionare **Pubblica**.

Si riceverà un messaggio di posta elettronica per far sapere all'utente che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Per pubblicare l'offerta al pubblico, passare a centro per i partner e selezionare **Go-Live**.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)
