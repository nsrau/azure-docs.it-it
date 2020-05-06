---
title: Creare un'offerta di modulo Azure IoT Edge con il centro per i partner in Azure Marketplace
description: Informazioni su come creare, configurare e pubblicare un'offerta di modulo IoT Edge in Azure Marketplace tramite il centro per i partner
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: f2ebbf5a8628c5c0321f85fac9901c314048ad50
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82743796"
---
# <a name="create-configure-and-publish-an-iot-edge-module-offer-in-azure-marketplace"></a>Creare, configurare e pubblicare un'offerta di modulo IoT Edge in Azure Marketplace

> [!IMPORTANT]
> Stiamo muovendo la gestione delle offerte del modulo IoT Edge dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni riportate nella [Panoramica della pubblicazione dell'offerta IOT Edge modulo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) per portale cloud partner per gestire le offerte.

Questo articolo descrive come creare e pubblicare un'offerta di modulo perimetrale di Internet delle cose per Azure Marketplace. Prima di iniziare, [creare un account Marketplace commerciale nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma Commercial Marketplace.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di navigazione a sinistra selezionare **Commercial Marketplace** > **Overview**.
3. Nella pagina Panoramica selezionare **+ nuovo offerta** > **IOT Edge modulo**.

    ![Viene illustrato il menu di spostamento a sinistra.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner vengono visualizzate solo nelle vetrine dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre dopo avere apportato modifiche.

### <a name="offer-id-and-alias"></a>ID offerta e alias

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta Marketplace e Azure Resource Manager modelli, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un **alias dell'offerta**. Questo è il nome usato per l'offerta nel centro per i partner.

- Questo nome non viene usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati per i clienti.
- Questa operazione non può essere modificata dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica dell'offerta** Mostra una rappresentazione visiva dei passaggi necessari per pubblicare questa offerta (completa e imminente) e il tempo necessario per completare ogni passaggio.

Questa pagina include i collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è un'offerta bozza di [eliminazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è Live- [stop selling the offer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima- [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se il server di pubblicazione non è stato completato, [annullare la pubblicazione.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Installazione dell'offerta

Per configurare l'offerta, seguire questa procedura.

### <a name="connect-lead-management"></a>Gestione dei lead di connessione

Quando si pubblica l'offerta nel Marketplace con il centro per i partner, è possibile connettersi facoltativamente al sistema CRM (Customer Relationship Management). In questo modo è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime l'interesse o usa il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il centro per i partner supporta i sistemi CRM seguenti:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per il coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è elencato sopra, usare la [tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o l' [endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei clienti potenziali, quindi esportare i dati nel sistema CRM.

2. Connetti l'offerta alla destinazione principale durante la pubblicazione nel centro per i partner.
3. Verificare che la connessione alla destinazione principale sia configurata correttamente. Dopo la pubblicazione nel centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'anteprima dell'offerta prima che venga attivata, è anche possibile testare la connessione del lead provando ad acquistare l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione principale rimanga aggiornata in modo da non perdere i lead.

Di seguito sono riportate alcune risorse di gestione lead aggiuntive:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (assicurarsi che il blocco popup sia disattivato).

Selezionare **Salva bozza** prima di continuare.

### <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie usate per raggruppare l'offerta nel Marketplace e i contratti legali che supportano l'offerta.

#### <a name="category"></a>Categoria

Selezionare un minimo di uno e un massimo di cinque categorie. Queste categorie vengono usate per inserire l'offerta nelle aree di ricerca del Marketplace appropriate e vengono visualizzate nella pagina dei dettagli dell'offerta. Nella descrizione dell'offerta, spiegare in che modo l'offerta supporta queste categorie. Nelle pagine di esplorazione tutti i moduli IOT Edge vengono visualizzati nella categoria **Internet delle cose > modulo** IOT Edge.

#### <a name="legal"></a>Note legali

È necessario specificare i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- Utilizzare il contratto standard per Microsoft Commercial Marketplace.
- Specificare i termini e le condizioni.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per Microsoft Commercial Marketplace

Offriamo un modello di contratto standard per semplificare le transazioni nel Marketplace commerciale. È possibile scegliere di offrire la soluzione nel contratto standard, che i clienti devono solo controllare e accettare una sola volta. Si tratta di un'opzione utile se non si vuole creare termini e condizioni personalizzati.

Per ulteriori informazioni sul contratto standard, vedere [contratto standard per Microsoft Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract). È anche possibile scaricare il file PDF con [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assicurarsi che il blocco popup sia disattivato).

Per usare il contratto standard, selezionare la casella di controllo **USA contratto standard per il Marketplace commerciale di Microsoft** , quindi fare clic su **accetta**.

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per Microsoft Commercial Marketplace, non è possibile usare i termini e le condizioni personalizzati. È possibile offrire la propria soluzione nel contratto standard o in base a termini e condizioni.

![Viene illustrato l'utilizzo della casella di controllo contratto standard per il Marketplace commerciale di Microsoft.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Termini e condizioni

Per specificare i termini e le condizioni personalizzati, immetterli nella casella **termini e condizioni** . In questa casella è possibile immettere una quantità illimitata di caratteri di testo. I clienti devono accettare questi termini prima di poter provare l'offerta.

Selezionare **Salva bozza** prima di continuare con la sezione successiva, ad elenco offerte.

## <a name="offer-listing"></a>Elenco offerte

Qui verranno definiti i dettagli dell'offerta visualizzati nel Marketplace. Sono inclusi il nome dell'offerta, la descrizione, le immagini e così via. Assicurarsi di seguire i criteri descritti in dettaglio nella pagina dei criteri di Microsoft durante la configurazione di questa offerta.

> [!NOTE]
> I dettagli dell'offerta non devono essere in inglese se la descrizione dell'offerta inizia con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile fornire un collegamento utile per offrire contenuti in una lingua diversa da quella usata nell'elenco dei dettagli dell'offerta.

### <a name="name"></a>Nome

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo è già compilato con il testo immesso nella casella **alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Nome:

- Può essere registrato (e possono essere inclusi marchi o simboli di copyright).
- Non può contenere più di 50 caratteri.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo risultati ricerca

Fornire una breve descrizione dell'offerta. Questo può avere una lunghezza di 100 caratteri e viene usato nei risultati della ricerca nel Marketplace.

### <a name="long-summary"></a>Riepilogo lungo

Fornire una descrizione più dettagliata dell'offerta. Questo può avere una lunghezza di 256 caratteri e viene usato nei risultati della ricerca nel Marketplace.

### <a name="description"></a>Descrizione

Fornire una descrizione più lunga dell'offerta, fino a 3.000 caratteri. Viene visualizzato ai clienti nella panoramica dell'elenco del Marketplace.

Includere uno o più dei seguenti elementi nella descrizione:

- Il valore e i vantaggi principali offerti dall'offerta
- Categoria o associazioni di settore o entrambi
- Opportunità di acquisto in-app
- Eventuali divulgazioni richieste

Le offerte del modulo IoT Edge devono includere un paragrafo requisiti hardware minimi nella parte inferiore della descrizione. Ad esempio:

*Requisiti hardware minimi: sistema operativo Linux x64 e ARM32, 1 GB di RAM, 500 MB di spazio di archiviazione*

Ecco alcuni suggerimenti per scrivere la descrizione:

- Descrivere chiaramente il valore dell'offerta nelle prime frasi della descrizione. Includere gli elementi seguenti:
    - Descrizione dell'offerta.
    - Tipo di utente che trae vantaggio dall'offerta.
    - Esigenze dei clienti o problemi relativi agli indirizzi dell'offerta.
- Tenere presente che le prime frasi potrebbero essere visualizzate nei risultati della ricerca.
- Non fare affidamento sulle funzionalità e sulle funzionalità per la vendita del prodotto. Concentrarsi invece sul valore fornito dall'offerta.
- Provare a usare il vocabolario specifico del settore o il wording basato sui vantaggi.

Per rendere più accattivante la **Descrizione** dell'offerta, usare l'editor di testo RTF per formattare la descrizione. L'editor di testo RTF consente di aggiungere numeri, elenchi puntati, grassetto, corsivo e rientri per rendere la descrizione più leggibile.

:::image type="content" source="media/text-editor2.png" alt-text="Viene illustrato l'editor di testo RTF." border="false":::

- Per modificare il formato del contenuto, evidenziare il testo che si desidera formattare e selezionare uno stile di testo, come illustrato nello screenshot seguente:

     :::image type="content" source="media/text-editor3.png" alt-text="Viene illustrato il controllo di stile testo nell'editor di testo RTF." border="false":::

- Per aggiungere un elenco puntato o numerato al testo, usare le opzioni illustrate nello screenshot seguente:
  
    :::image type="content" source="media/text-editor4.png" alt-text="Vengono illustrati i controlli elenco puntato e numero nell'editor di testo RTF." border="false":::

- Per aggiungere o rimuovere il rientro nel testo, usare le opzioni illustrate in questo screenshot:

    :::image type="content" source="media/text-editor5.png" alt-text="Vengono illustrati i controlli di rientro nell'editor di testo RTF." border="false":::

#### <a name="privacy-policy-url"></a>URL informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'offerta sia conforme alle leggi e alle normative sulla privacy. L'utente è anche responsabile dell'invio di un criterio di privacy valido nel sito Web.

#### <a name="useful-links"></a>Collegamenti utili

Fornire documenti online supplementari sull'offerta. È possibile aggiungere fino a 25 collegamenti. Per aggiungere un collegamento, selezionare **+ Aggiungi un collegamento** e quindi completare i campi seguenti:

- **Titolo** : i clienti visualizzeranno il titolo nella pagina dei dettagli dell'offerta.
- **Collegamento (URL)** : immettere un collegamento per i clienti per visualizzare il documento online. Il collegamento deve iniziare con http://o https://.

Assicurarsi di aggiungere almeno un collegamento alla documentazione e un collegamento ai dispositivi compatibili IoT Edge dal [Catalogo dispositivi Azure](https://catalog.azureiotsolutions.com/)Internet.

### <a name="contact-information"></a>Informazioni contatto

È necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per un **contatto di supporto** e un **contatto tecnico.** Queste informazioni non vengono visualizzate ai clienti. È disponibile per Microsoft e può essere fornita ai partner Cloud Solution Provider (CSP).

- Contatto per il supporto tecnico (obbligatorio): per domande di supporto generale.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto del programma CSP (facoltativo): per domande sul rivenditore correlate al programma CSP.

Nella sezione **contatto del supporto tecnico** fornire l'indirizzo Web del **sito Web di supporto** in cui i partner possono trovare il supporto per l'offerta in base al fatto che l'offerta sia disponibile in Azure globale, Azure per enti pubblici o in entrambi.

Nella sezione del **contatto del programma CSP** fornire il collegamento (**materiali di marketing dei programmi CSP**) in cui i partner CSP possono trovare materiali di marketing per l'offerta.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

Per ulteriori informazioni sulla creazione di elenchi di offerte, vedere l'articolo relativo alle [procedure consigliate](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)per l'elenco.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato png. Le immagini sfocate verranno rifiutate.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

#### <a name="store-logos"></a>Logo dello Store

Fornire i file con estensione png del logo dell'offerta in ognuna delle quattro dimensioni seguenti:

- **Piccolo (48 x 48)**
- **Media (90 x 90)**
- **Grande (216 x 216)**
- **Wide (255 x 115)**

Tutti e quattro i logo sono obbligatori e vengono usati in posizioni diverse nell'inserzione nel Marketplace.

#### <a name="screenshots-optional"></a>Schermate (facoltativo)

Aggiungere fino a cinque schermate che mostrano il funzionamento dell'offerta. Ogni deve avere una dimensione di 1280 x 720 pixel e in formato png.

#### <a name="videos-optional"></a>Video (facoltativo)

È possibile aggiungere fino a cinque video che illustrano l'offerta. Immettere il nome del video, il relativo indirizzo Web e un'immagine thumbnail. png del video con dimensioni di 1280 x 720 pixel.

#### <a name="offer-examples"></a>Esempi di offerta

Gli esempi seguenti mostrano come vengono visualizzati i campi dell'elenco di offerte in posizioni diverse dell'offerta.

Questa schermata mostra la pagina di presentazione dell' **offerta** in Azure Marketplace.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Viene illustrata la pagina di presentazione dell'offerta in Azure Marketplace.":::

Questa schermata mostra i risultati della ricerca in Azure Marketplace:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Illustra i risultati della ricerca in Azure Marketplace.":::

Questa schermata mostra la pagina di presentazione dell' **offerta** nel portale di Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Viene illustrata la pagina di inserzione dell'offerta in portale di Azure.":::

Questa schermata mostra i risultati della ricerca nella portale di Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Viene illustrata la pagina di inserzione dell'offerta in portale di Azure.":::

Selezionare **Salva bozza** prima di passare alla sezione successiva, anteprima.

## <a name="preview"></a>Anteprima

Nella **scheda Anteprima**è possibile scegliere un **pubblico di anteprima** limitato per convalidare l'offerta prima di pubblicarla in un gruppo più ampio di destinatari del Marketplace.

> [!IMPORTANT]
> Dopo aver visualizzato l'offerta in anteprima, è necessario selezionare **Go Live** per pubblicare l'offerta sul pubblico.

Specificare i destinatari dell'anteprima usando i GUID di ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi può essere visualizzato dai clienti.

> [!NOTE]
> È possibile trovare l'ID sottoscrizione di Azure nella pagina sottoscrizioni del portale di Azure.

Aggiungere almeno un ID sottoscrizione di Azure, singolarmente (fino a 10) o caricando un file CSV (fino a 100). Aggiungendo questi ID sottoscrizione, si definiscono gli utenti che possono visualizzare l'anteprima dell'offerta prima della pubblicazione in tempo reale. Se l'offerta è già attiva, è possibile definire un pubblico di anteprima per testare le modifiche o gli aggiornamenti dell'offerta.

> [!NOTE]
> Il pubblico di anteprima è diverso da un pubblico privato. Un pubblico di **Anteprima** può visualizzare e confermare tutti i piani di offerta prima che siano attivi nel Marketplace, inclusi quelli che verranno pubblicati solo per i destinatari **privati** (impostati nella scheda disponibilità).

Selezionare **Salva bozza** prima di passare alla sezione successiva, piano panoramica.

### <a name="plan-overview"></a>Panoramica del piano

Questa scheda consente di fornire opzioni di piano diverse all'interno della stessa offerta nel centro per i partner. Questi piani, sono stati definiti in precedenza come SKU o unità di mantenimento delle scorte. I piani possono variare in base ai cloud disponibili, ad esempio i cloud globali, i cloud governativi e l'immagine a cui fa riferimento il piano. Per elencare l'offerta nel Marketplace, è necessario configurare almeno un piano.

Dopo aver creato i piani, la scheda **panoramica piano** Mostra:

- Nomi dei piani
- Modello di prezzi
- Disponibilità cloud (globale o per enti pubblici)
- Stato di pubblicazione corrente
- Eventuali azioni disponibili

Le azioni disponibili nella panoramica del piano variano a seconda dello stato corrente del piano. Tali impostazioni includono:

- **Elimina bozza**: se lo stato del piano è una bozza.
- **Stop sell Plan**: se lo stato del piano è pubblicato in tempo reale.

#### <a name="create-new-plan"></a>Crea nuovo piano

Selezionare **Crea nuovo piano**. Verrà visualizzata la finestra di dialogo **nuovo piano** .

Nella casella **ID piano** creare un ID piano univoco per ogni piano di questa offerta. Questo ID sarà visibile ai clienti nell'indirizzo Web del prodotto. Usare solo lettere minuscole e numeri, trattini o caratteri di sottolineatura e un massimo di 50 caratteri.

Nella casella **nome piano** immettere un nome per il piano. I clienti visualizzano questo nome per decidere quale piano selezionare nell'offerta. Creare un nome univoco per ogni piano nell'offerta. Ad esempio, è possibile usare il nome di un'offerta di **Windows Server** con i piani **Windows Server 2016** e **Windows Server 2019**.

> [!NOTE]
> Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.

Selezionare **Crea**.

### <a name="plan-setup"></a>Pianificare l'installazione

Questa scheda consente di configurare i cloud in cui è disponibile il piano. Le risposte in questa scheda influiscono sui campi che vengono visualizzati nelle altre schede.

#### <a name="cloud-availability"></a>Disponibilità cloud

Il piano deve essere disponibile in almeno un cloud usando l'hub Azure.

Selezionare l'opzione **globale di Azure** in modo che il piano possa essere usato dai clienti in tutte le aree di Azure globali che usano il Marketplace. Per informazioni dettagliate, vedere [disponibilità geografica e supporto della valuta](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selezionare l'opzione [Azure Government cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) per fare in modo che la soluzione appaia qui. Si tratta di un cloud della community per enti pubblici con accesso controllato per i clienti di agenzie governative statunitensi, statali, locali o tribali, oltre ai partner idonei per la loro gestione. In qualità di editore, l'utente è responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate per questa community Cloud. Azure per enti pubblici USA Data Center e reti fisicamente isolate (situate solo negli Stati Uniti). Prima della [pubblicazione](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) in Azure per enti pubblici, testare e confermare la soluzione in tale area, perché i risultati potrebbero essere diversi. Per organizzare e testare la soluzione, richiedere un account di prova da [Microsoft Azure per enti pubblici versione di valutazione](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo che il piano è stato pubblicato e disponibile in un cloud specifico, non è possibile rimuovere il cloud.

#### <a name="azure-government-cloud-certifications"></a>Certificazioni cloud di Azure per enti pubblici

Questa opzione è visibile solo se il **cloud di Azure per enti pubblici** è selezionato in **disponibilità cloud**.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e normative governative. Ad esempio, FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per sensibilizzare le certificazioni per questi programmi, è possibile fornire fino a 100 collegamenti che descrivono le certificazioni. Questi possono essere collegamenti alle inserzioni del programma direttamente o al proprio sito Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

## <a name="plan-listing"></a>Elenco di piani

In questa scheda vengono visualizzate informazioni specifiche per ogni piano diverso all'interno della stessa offerta.

### <a name="plan-name"></a>Nome piano

Questa operazione è precompilata con il nome assegnato al piano al momento della creazione. È possibile modificare questo nome, in base alle esigenze. Può avere una lunghezza di 50 caratteri. Questo nome viene visualizzato come titolo del piano in Azure Marketplace e portale di Azure. Viene usato come nome del modulo predefinito dopo che il piano è pronto per essere usato.

### <a name="plan-summary"></a>Riepilogo del piano

Fornire un breve riepilogo del piano (non l'offerta). Questo riepilogo viene visualizzato nei risultati della ricerca di Azure Marketplace e può contenere un massimo di 100 caratteri.

### <a name="plan-description"></a>Descrizione del piano

Descrivere ciò che rende univoco questo piano, nonché le differenze tra i piani all'interno dell'offerta. Non descrivere l'offerta, ma solo il piano. Questa descrizione verrà visualizzata in Azure Marketplace e nella portale di Azure nella pagina di inserzione dell'offerta. Può trattarsi dello stesso contenuto fornito nel riepilogo del piano e contenere fino a 2.000 caratteri.

Selezionare **Salva bozza** dopo aver completato questi campi.

#### <a name="plan-examples"></a>Esempi di piano

Negli esempi seguenti viene illustrato il modo in cui i campi dell'elenco di piani vengono visualizzati in visualizzazioni diverse.

Questi sono i campi in Azure Marketplace quando si visualizzano i dettagli del piano:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Illustra i campi visualizzati quando si visualizzano i dettagli del piano in Azure Marketplace.":::

Questi sono i dettagli del piano sulla portale di Azure:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Vengono illustrati i dettagli del piano sulla portale di Azure.":::

## <a name="availability"></a>Disponibilità

Se si vuole nascondere l'offerta pubblicata in modo che i clienti non possano cercarla, sfogliarla o acquistarla nel Marketplace, selezionare la casella di controllo **Nascondi piano** nella scheda disponibilità.

Questo campo viene comunemente usato nei casi seguenti:

- L'offerta deve essere utilizzata solo indirettamente se viene fatto riferimento a un'altra applicazione.
- L'offerta non deve essere acquistata singolarmente.
- Il piano è stato usato per il test iniziale e non è più pertinente.
- Il piano è stato usato per le offerte temporanee o stagionali e non dovrebbe essere più disponibile.

## <a name="technical-configuration"></a>Configurazione tecnica

Il tipo di offerta del **modulo IOT Edge** è un tipo specifico di contenitore eseguito in un dispositivo IOT Edge. Nella scheda **configurazione tecnica** verranno fornite informazioni di riferimento per il repository di immagini contenitore all'interno del [container Registry di Azure](https://azure.microsoft.com/services/container-registry/), insieme alle impostazioni di configurazione che consentono ai clienti di utilizzare il modulo in modo semplice.

Dopo la pubblicazione dell'offerta, l'immagine del contenitore IoT Edge viene copiata in Azure Marketplace in un registro contenitori pubblico specifico. Tutte le richieste degli utenti di Azure per l'uso del modulo vengono gestite dal registro contenitori pubblico di Azure Marketplace, non dal registro contenitori privato.

È possibile fare riferimento a più piattaforme e fornire diverse versioni dell'immagine del contenitore del modulo usando i tag. Per altre informazioni sui tag e sul controllo delle versioni, vedere [preparare le risorse tecniche del modulo IOT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset).

### <a name="image-repository-details"></a>Dettagli archivio immagini

Nella scheda **Dettagli archivio immagini** verranno fornite le informazioni seguenti.

**Selezionare l'origine dell'immagine**: selezionare l'opzione **container Registry di Azure** .

**ID sottoscrizione di Azure**: specificare l'ID sottoscrizione in cui viene segnalato l'utilizzo delle risorse e i servizi vengono fatturati per il container Registry di Azure che include l'immagine del contenitore. Questo ID è reperibile nella [pagina Sottoscrizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure.

**Nome del gruppo di risorse di Azure**: specificare il nome del [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) che contiene il container Registry di Azure con l'immagine del contenitore. Il gruppo di risorse deve essere accessibile nell'ID sottoscrizione (sopra). È possibile trovare il nome nella pagina [gruppi di risorse](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) della portale di Azure.

**Nome del registro contenitori di Azure**: specificare il nome del [container Registry di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) con l'immagine del contenitore. Il registro contenitori deve essere presente nel gruppo di risorse di Azure specificato in precedenza. Specificare solo il nome del registro di sistema, non il nome completo del server di accesso. Assicurarsi di omettere **azurecr.io** dal nome. È possibile trovare il nome del registro di sistema nella [pagina registri contenitori](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) del portale di Azure.

**Nome utente amministratore per il container Registry di Azure**: specificare il [nome utente amministratore](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) associato al container Registry di Azure che contiene l'immagine del contenitore. Il nome utente e la password sono necessari per assicurarsi che l'azienda abbia accesso al registro di sistema. Per ottenere il nome utente e la password dell'amministratore, impostare la proprietà **abilitata** per l'amministratore su **true** usando l'interfaccia della riga di comando di Azure. Facoltativamente, è possibile impostare l' **utente amministratore** per **abilitare** nell'portale di Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="Viene illustrata la finestra di dialogo Aggiorna registro contenitori.":::

**Password per il container Registry di Azure**: specificare la password per il nome utente amministratore associato al container Registry di Azure e l'immagine del contenitore. Il nome utente e la password sono necessari per assicurarsi che l'azienda abbia accesso al registro di sistema. È possibile ottenere la password dal portale di Azure accedendo a **container Registry** > **chiavi di accesso** o con l'interfaccia della riga di comando di Azure usando il [comando Mostra.](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Viene illustrata la schermata chiave di accesso in portale di Azure.":::

**Nome del repository all'interno del container Registry di Azure**. Specificare il nome del repository Container Registry di Azure con la propria immagine. Il nome del repository viene specificato quando si esegue il push dell'immagine nel registro di sistema. È possibile trovare il nome del repository passando alla**pagina repository** [container Registry](https://azure.microsoft.com/services/container-registry/) > . Per altre informazioni, vedere [visualizzare i repository del registro contenitori nel portale di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-repositories). Si noti che dopo aver impostato il nome, non è possibile modificarlo. Usare un nome univoco per ogni offerta nell'account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tag di immagine per le nuove versioni dell'offerta

I clienti devono essere in grado di ottenere automaticamente gli aggiornamenti da Azure Marketplace quando si pubblica un aggiornamento. Se non si desidera eseguire l'aggiornamento, è necessario che siano in grado di rimanere in una versione specifica dell'immagine. A tale scopo, è possibile aggiungere nuovi tag immagine ogni volta che si esegue un aggiornamento all'immagine.

**Tag Image**. Questo campo deve includere un tag **più recente** che punti alla versione più recente dell'immagine in tutte le piattaforme supportate. Deve includere anche un tag Version (ad esempio, a partire da XX. XX. XX, dove XX è un numero). I clienti devono usare [tag manifesto](https://github.com/estesp/manifest-tool) per più piattaforme di destinazione. È necessario aggiungere anche tutti i tag referenziati da un tag di manifesto per poterli caricare. Tutti i tag del manifesto (ad eccezione del tag più recente) devono iniziare con X. Y-o X. Y. Z-dove X, Y e Z sono numeri interi. Se, ad esempio, un tag più recente punta a 1.0.1-Linux-x64, 1.0.1-Linux-ARM32 e 1.0.1-Windows-ARM32, questi sei tag devono essere aggiunti a questo campo. Per informazioni dettagliate sui tag e sul controllo delle versioni, vedere [preparare le risorse tecniche del modulo IOT Edge.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)

### <a name="default-deployment-settings-optional"></a>Impostazioni di distribuzione predefinite (facoltativo)

Definire le impostazioni più comuni per distribuire il modulo IoT Edge. Ottimizza le distribuzioni dei clienti consentendo di avviare il modulo IoT Edge predefinito con queste impostazioni predefinite.

**Route predefinite**. L'hub IoT Edge gestisce la comunicazione tra i moduli, l'hub Internet delle cose e i dispositivi. È possibile impostare le route per l'input e l'output dei dati tra i moduli e l'hub Internet, che offre la flessibilità necessaria per inviare messaggi in cui è necessario procedere senza la necessità di servizi aggiuntivi per elaborare i messaggi o scrivere codice aggiuntivo. Le route vengono costruite mediante coppie nome/valore. È possibile definire fino a cinque nomi di route predefiniti, ognuno con una lunghezza di 512 caratteri.

Assicurarsi di usare la sintassi di [Route](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) corretta nel valore di route (in genere definito da/Message/* in $upstream). Ciò significa che tutti i messaggi inviati da qualsiasi modulo passano all'hub Internet. Per fare riferimento al modulo, usare il nome predefinito del modulo, che sarà il **nome dell'offerta**, senza spazi o caratteri speciali. Per fare riferimento ad altri moduli non ancora noti, utilizzare la convenzione <FROM_MODULE_NAME> per informare i clienti che è necessario aggiornare queste informazioni. Per informazioni dettagliate sulle route IoT Edge, vedere [dichiarare le route](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes).

Ad esempio, se il modulo ContosoModule è in ascolto per gli input in ContosoInput e i dati di output in ContosoOutput, è opportuno definire le due route predefinite seguenti:

- Nome #1: ToContosoModule
- Valore #1: da/messages/Modules/<FROM_MODULE_NAME>/Outputs/* in BrokeredEndpoint ("/modules/ContosoModule/inputs/ContosoInput")
- Nome #2: FromContosoModuleToCloud
- Valore #2: da/messages/modules/ContonsoModule/outputs/ContosoOutput in $upstream

**Proprietà desiderate del modulo gemello predefinito**. Un modulo gemello è un documento JSON nell'hub Internet che archivia le informazioni sullo stato per un'istanza del modulo, incluse le proprietà desiderate. Le proprietà desiderate vengono usate insieme alle proprietà segnalate per sincronizzare la configurazione o le condizioni del modulo. Il back-end della soluzione può impostare le proprietà desiderate, che possono essere lette dal modulo. Il modulo può inoltre ricevere notifiche delle modifiche nelle proprietà desiderate. Le proprietà desiderate vengono create usando fino a cinque coppie nome/valore e ogni valore predefinito deve essere minore di 512 caratteri. È possibile definire fino a cinque proprietà di nome/valore gemello desiderato. I valori delle proprietà desiderate gemelle devono essere JSON validi, non di escape, senza matrici con una gerarchia nidificata massima di quattro livelli. In uno scenario in cui un parametro necessario per un valore predefinito non ha senso, ad esempio l'indirizzo IP del server del cliente, è possibile aggiungere un parametro come valore predefinito. Per altre informazioni sulle proprietà desiderate del dispositivo gemello, vedere [definire o aggiornare le proprietà desiderate](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

Se, ad esempio, un modulo supporta una frequenza di aggiornamento configurabile in modo dinamico usando le proprietà desiderate del dispositivo gemello, è consigliabile definire la proprietà desiderata del gemello predefinita seguente:

- Nome #1: RefreshRate
- #1 valore: 60

**Variabili di ambiente predefinite**. Le variabili di ambiente forniscono informazioni aggiuntive a un modulo che facilita il processo di configurazione. Le variabili di ambiente vengono create utilizzando le coppie nome/valore. Ogni nome e valore predefinito della variabile di ambiente deve essere composto da meno di 512 caratteri ed è possibile definirne fino a cinque. Quando un parametro necessario per un valore predefinito non ha senso, ad esempio l'indirizzo IP del server del cliente, è possibile aggiungere un parametro come valore predefinito.

Se ad esempio un modulo richiede di accettare le condizioni d'uso prima dell'avvio, è possibile definire la variabile di ambiente seguente:

- Nome #1: ACCEPT_EULA
- #1 valore: Y

**Opzioni predefinite di creazione del contenitore**. Le opzioni di creazione del contenitore indirizzano la creazione del contenitore docker del modulo IoT Edge. IoT Edge supporta le opzioni di creazione del contenitore dell'API del motore docker. Vedere tutte le opzioni in [elenco contenitori.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Il campo Crea opzioni deve essere JSON valido, non di escape e minore di 512 caratteri.

Se, ad esempio, un modulo richiede il binding delle porte, definire le opzioni di creazione seguenti:

"HostConfig": {"PortBindings": {"5012/TCP": [{"Portahost": "5012"}]}

## <a name="review-and-publish"></a>Esaminare e pubblicare

Una volta completate tutte le sezioni obbligatorie dell'offerta, è possibile inviarle per la revisione e la pubblicazione.

Nell'angolo superiore destro del portale selezionare **revisione e pubblicazione**.

Nella pagina verifica è possibile visualizzare lo stato di pubblicazione:

- Vedere lo stato di completamento di ogni sezione dell'offerta. Non è possibile pubblicare fino a quando tutte le sezioni dell'offerta non vengono contrassegnate come complete.
    - **Non avviato** : la sezione non è stata avviata e deve essere completata.
    - **Incompleto** . nella sezione sono presenti errori che devono essere corretti oppure è necessario fornire altre informazioni. Per informazioni aggiuntive, vedere le sezioni precedenti di questo documento.
    - **Completato** : la sezione contiene tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere completate prima di poter inviare l'offerta.
- Fornire al team di certificazione istruzioni di test per assicurarsi che l'offerta venga testata correttamente. Inoltre, fornire eventuali note supplementari utili per comprendere l'offerta.

Per inviare l'offerta per la pubblicazione, selezionare **pubblica**.

Ti invieremo un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Per pubblicare l'offerta al pubblico (o, se un'offerta privata, per un pubblico privato), accedere al centro per i partner e selezionare **Go-Live**.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel Marketplace commerciale](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)