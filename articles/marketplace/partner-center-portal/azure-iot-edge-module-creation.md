---
title: Creare un'offerta del modulo Azure IoT Edge con il Centro per i partner - Azure MarketplaceCreate an Azure IoT Edge module offer with Partner Center - Azure Marketplace
description: Informazioni su come creare un'offerta di modulo IoT Edge in Azure Marketplace usando il Centro per i partner
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: cca54e4e456fe766b190f64657cd1aca1d9520e0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869138"
---
# <a name="create-an-iot-edge-module-offer"></a>Creare un'offerta di moduli IoT Edge

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte dei moduli IoT Edge dal portale per i partner cloud al Centro per i partner. Fino alla migrazione delle offerte, segui le istruzioni nel [modulo IoT Edge che offre una panoramica della pubblicazione](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) di Cloud Partner Portal per gestire le tue offerte.

Questo articolo descrive come creare e pubblicare un'offerta di moduli Edge di Internet of Things (IoT) per Azure Marketplace.This article describes how to create and publish an Internet of Things (IoT) Edge module offer for Azure Marketplace.

Prima di poter creare un'offerta del modulo IoT Edge, è necessario disporre di un account del marketplace commerciale nel Centro per i partner. Se non ne hai ancora creato uno, vedi [Creare un account marketplace commerciale nel Centro per i partner.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al Centro per i partner.
2. Nel menu di spostamento a sinistra selezionare**Panoramica** **Marketplace** > commerciale .

    ![Illustra il menu di navigazione a sinistra.](./media/cs-menu-overview.png)

3. Selezionare **: Modulo** > Nuova offerta**IoT Edge**. Viene visualizzata la finestra di dialogo **Nuova offerta.**

> [!IMPORTANT]
> Dopo la pubblicazione di un'offerta, le modifiche apportate nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurati di ripubblicare sempre dopo aver apportato le modifiche.

### <a name="offer-id-and-alias"></a>ID e alias dell'offerta

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nel tuo account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi, ed è limitato a 50 caratteri. Ad esempio, se si immette **test-offer-1**, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`l'indirizzo Web dell'offerta sarà .
- L'ID offerta non può essere modificato dopo aver selezionato Crea.

Immettere un **alias di offerta**. Questo è il nome usato per fare riferimento all'offerta nel Centro per i partner.

- Questo nome non viene utilizzato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Questa operazione non può essere modificata dopo aver selezionato **Crea**.

Dopo aver immesso questi due valori, selezionare **Crea** prima di passare alla pagina successiva, Panoramica offerta.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica dell'offerta** mostra una rappresentazione visiva dei passaggi necessari per pubblicare questa offerta (sia completata che successiva) e della durata del completamento di ogni passaggio.

Questa pagina include collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è una bozza - [Elimina bozza di offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è in diretta - [Smetti di vendere l'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se non hai completato la disconnessione del publisher - [Annulla pubblicazione.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configurazione dell'offerta

Segui questi passaggi per configurare la tua offerta.

### <a name="connect-lead-management"></a>Gestione dei lead di Connect

Quando pubblichi l'offerta nel marketplace con il Centro per i partner, puoi facoltativamente connetterla al sistema CRM (Customer Relationship Management). Ciò consente di ricevere le informazioni di contatto del cliente non appena qualcuno esprime interesse o utilizza il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il Centro per i partner supporta i seguenti sistemi CRM:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è elencato in precedenza, usare [Tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o Endpoint [https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei lead dei clienti, quindi esportare i dati nel sistema CRM.

2. Collegare l'offerta alla destinazione lead durante la pubblicazione nel Centro per i partner.
3. Verificare che la connessione alla destinazione del lead sia configurata correttamente. Dopo averla pubblicata nel Centro per i partner, convalideremo la connessione e ti invieremo un responsabile di test. Durante l'anteprima dell'offerta prima che venga messa in diretta, puoi anche testare la connessione lead provando ad acquistare l'offerta nell'ambiente di anteprima.
4. Assicurati che la connessione alla destinazione lead rimanga aggiornata in modo da non perdere alcun lead.

Di seguito sono riportate alcune risorse aggiuntive per la gestione dei lead:Here are some additional lead management resources:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Assicurarsi che il blocco popup sia disattivato).

Selezionare **Salva bozza** prima di continuare con la sezione successiva, Proprietà.

### <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie utilizzate per raggruppare l'offerta sul mercato e i contratti legali che supportano l'offerta.

#### <a name="category"></a>Category

Selezionare un minimo di una e un massimo di cinque categorie. Queste categorie vengono utilizzate per inserire l'offerta nelle aree di ricerca del marketplace appropriate e vengono visualizzate nella pagina dei dettagli dell'offerta. Nella descrizione dell'offerta, spiega come la tua offerta supporta queste categorie. Nelle pagine di ricerca, tutti i moduli IoT Edge sono visualizzati nella categoria di  **moduli Internet of Things > IoT Edge.**

#### <a name="legal"></a>Note legali

È necessario fornire i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- Utilizzare il contratto standard per il Marketplace commerciale Microsoft.
- Fornire i propri termini e condizioni.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per Microsoft Commercial Marketplace

Offriamo un modello di contratto standard per facilitare le transazioni sul mercato commerciale. Puoi scegliere di offrire la tua soluzione in base al Contratto Standard, che i clienti devono solo controllare e accettare una volta. Questa è una buona opzione se non vuoi creare termini e condizioni personalizzati.

Per ulteriori informazioni sul contratto standard, vedere [Contratto standard per Il Marketplace commerciale Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Puoi anche scaricare il PDF [del contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assicurati che il blocco popup sia disattivato).

Per utilizzare il contratto standard, selezionare la casella di controllo **Usa contratto standard per** il mercato commerciale di Microsoft e quindi fare clic su **Accetta**.

> [!NOTE]
> Dopo aver pubblicato un'offerta utilizzando il contratto Standard per Microsoft Commercial Marketplace, non è possibile utilizzare i termini e le condizioni personalizzati. Offri la tua soluzione in base al Contratto Standard o ai tuoi termini e condizioni.

![Illustra l'utilizzo della casella di controllo Contratto standard per il marketplace commerciale di Microsoft.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>I tuoi termini e condizioni

Per fornire i termini e le condizioni personalizzati, immetterli nella casella **Termini e condizioni.** È possibile immettere un numero illimitato di caratteri di testo in questa casella. I clienti devono accettare questi termini prima di poter provare la tua offerta.

Seleziona **Salva bozze** prima di continuare con la sezione successiva, Offerta.

## <a name="offer-listing"></a>Inserzione dell'offerta

Qui definirai i dettagli dell'offerta che vengono visualizzati nel marketplace. Sono inclusi il nome, la descrizione, le immagini e così via. Assicurarsi di seguire i criteri descritti nella pagina dei criteri di Microsoft durante la configurazione di questa offerta.

> [!NOTE]
> I dettagli dell'offerta non sono necessari per essere in inglese se la descrizione dell'offerta inizia con la frase "Questa applicazione è disponibile solo in [lingua non in lingua inglese]". Va anche bene fornire un Link utile per offrire contenuti in una lingua diversa da quella utilizzata nei dettagli dell'inserzione Offerta.

### <a name="name"></a>Nome

Il nome che inserisci qui viene visualizzato come titolo dell'offerta. Questo campo è precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Il nome:

- Può essere registrato (e l'utente può includere simboli di marchio o copyright).
- Non può essere lungo più di 50 caratteri.
- Non è possibile includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Fornisci una breve descrizione della tua offerta. Può essere lungo fino a 100 caratteri e viene utilizzato nei risultati di ricerca del marketplace.

### <a name="long-summary"></a>Riassunto lungo

Fornisci una descrizione più dettagliata della tua offerta. Può essere lungo fino a 256 caratteri e viene utilizzato nei risultati di ricerca del marketplace.

### <a name="description"></a>Descrizione

Fornisci una descrizione più lunga della tua offerta, fino a 3.000 caratteri. Viene visualizzato dai clienti nella panoramica dell'elenco dei marketplace.

Includere una o più delle seguenti opzioni nella descrizione:

- Il valore e i vantaggi principali che la tua offerta offre
- Associazioni di categoria o di settore, o entrambe
- Opportunità di acquisto in-app
- Eventuali divulgazioni obbligatorie

Le offerte del modulo IoT Edge devono includere un paragrafo dei requisiti hardware minimi nella parte inferiore della descrizione. Ad esempio:

*Requisiti hardware minimi: Linux x64 e arm32 OS, 1 GB di RAM, 500 MB di spazio di archiviazione*

Ecco alcuni suggerimenti per scrivere la tua descrizione:

- Descrivi chiaramente il valore della tua offerta nelle prime frasi della tua descrizione. Includere i seguenti elementi:
    - Descrizione dell'offerta.
    - Il tipo di utente che beneficia dell'offerta.
    - Il cliente ha bisogno o emette gli indirizzi dell'offerta.
- Ricorda che le prime frasi potrebbero essere visualizzate nei risultati della ricerca.
- Non fare affidamento su caratteristiche e funzionalità per vendere il tuo prodotto. Concentrati invece sul valore offerto dalla tua offerta.
- Prova a usare il vocabolario specifico del settore o la formulazione basata sui vantaggi.

Per rendere **Description** la descrizione dell'offerta più coinvolgente, usa l'editor RTF per formattare la descrizione. L'editor RTF consente di aggiungere numeri, punti elenco, grassetto, corsivo e rientri per rendere la descrizione più leggibile.

:::image type="content" source="media/text-editor2.png" alt-text="Illustra l'editor RTF." border="false":::

- Per modificare il formato del contenuto, evidenziare il testo che si desidera formattare e selezionare uno stile di testo, come illustrato in questa schermata:

     :::image type="content" source="media/text-editor3.png" alt-text="Illustra il controllo dello stile di testo nell'editor RTF." border="false":::

- Per aggiungere un elenco puntato o numerato al testo, utilizzare le opzioni visualizzate in questa schermata:
  
    :::image type="content" source="media/text-editor4.png" alt-text="Illustra i controlli elenco puntato e elenco numeri nell'editor RTF." border="false":::

- Per aggiungere o rimuovere il rientro al testo, utilizzare le opzioni mostrate in questa schermata:

    :::image type="content" source="media/text-editor5.png" alt-text="Illustra i controlli di rientro nell'editor RTF." border="false":::

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. Hai la responsabilità di garantire che la tua offerta sia conforme alle leggi e ai regolamenti sulla privacy. Sei anche responsabile per la pubblicazione di una politica sulla privacy valida sul tuo sito web.

#### <a name="useful-links"></a>Collegamenti utili

Fornisci documenti online supplementari sulla tua offerta. È possibile aggiungere fino a 25 collegamenti. Per aggiungere un collegamento, selezionare **Aggiungi un collegamento** e quindi completare i seguenti campi:

- **Titolo** - I clienti vedranno il titolo nella pagina dei dettagli dell'offerta.
- **Collegamento (URL):** immettere un collegamento per la visualizzazione del documento online da parte dei clienti. Il collegamento deve iniziare con http:// o https://.

Assicurarsi di aggiungere almeno un collegamento alla documentazione e un collegamento ai dispositivi IoT Edge compatibili dal catalogo dei [dispositivi IoT](https://catalog.azureiotsolutions.com/)di Azure.

### <a name="contact-information"></a>Informazioni contatto

È necessario fornire il nome, l'indirizzo e-mail e il numero di telefono per un **contatto di supporto** e un contatto di **progettazione.** Queste informazioni non vengono mostrate ai clienti. È disponibile per Microsoft e può essere fornito ai partner Cloud Solution Provider (CSP).

- Contatto di supporto (obbligatorio): per domande generali sul supporto tecnico.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto del programma CSP (facoltativo): per le domande dei rivenditori relative al programma CSP.

Nella sezione **Contatto di supporto** specificare l'indirizzo Web del sito Web del supporto **tecnico** in cui i partner possono trovare supporto per l'offerta in base al fatto che l'offerta sia disponibile in Azure globale, Azure per enti pubblici o in entrambi.

Nella sezione di contatto del **programma CSP,** fornire il collegamento (**CSP Program Marketing Materials**) in cui i partner CSP possono trovare materiale di marketing per la tua offerta.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione del marketplace

Per ulteriori informazioni sulla creazione di inserzioni di offerte, consulta Best practice per [l'inserzione di offerte.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornisci loghi e immagini da utilizzare con la tua offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate verranno rifiutate.

>[!Note]
>Se si verifica un problema durante il caricamento dei https://upload.xboxlive.com file, assicurarsi che la rete locale non blocchi il servizio utilizzato dal Centro per i partner.

#### <a name="store-logos"></a>Loghi del Negozio

Fornisci i file .png del logo della tua offerta in ognuna delle seguenti quattro dimensioni in pixel:

- **Piccolo (48 x 48)**
- **Medio (90 x 90)**
- **Grande (216 x 216)**
- **Ampio (255 x 115)**

Tutti e quattro i loghi sono obbligatori e vengono utilizzati in luoghi diversi nella presentazione del marketplace.

#### <a name="screenshots-optional"></a>Screenshot (facoltativo)

Aggiungi fino a cinque screenshot che mostrano come funziona la tua offerta. Ciascuno deve avere dimensioni 1280 x 720 pixel e in formato PNG.

#### <a name="videos-optional"></a>Video (opzionale)

Aggiungi fino a cinque video che dimostrano la tua offerta. Inserisci il nome del video, il suo indirizzo web e un'immagine .png in miniatura del video con dimensioni di 1280 x 720 pixel.

#### <a name="offer-examples"></a>Esempi di offerta

Gli esempi seguenti mostrano come i campi dell'offerta vengono visualizzati in luoghi diversi dell'offerta.

Questa schermata mostra la pagina **di presentazione dell'offerta** in Azure Marketplace.This screenshot shows the Offer listing page in Azure Marketplace.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Illustra la pagina di presentazione dell'offerta in Azure Marketplace.":::

Questa schermata mostra i risultati della ricerca in Azure Marketplace:This screenshot shows the search results in Azure Marketplace:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Illustra i risultati della ricerca in Azure Marketplace.Illustrates the search results in Azure Marketplace.":::

Questa schermata mostra la pagina **Di presentazione dell'offerta** nel portale di Azure.This screenshot shows the Offer listing page in the Azure portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Illustra la pagina di elenco Offerta nel portale di Azure.Illustrates the Offer listing page in Azure portal.":::

Questa schermata mostra i risultati della ricerca nel portale di Azure.This screenshot shows search results in the Azure portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Illustra la pagina di elenco Offerta nel portale di Azure.Illustrates the Offer listing page in Azure portal.":::

Selezionare **Salva bozza** prima di passare alla sezione successiva, Anteprima.

## <a name="preview"></a>Anteprima

Nella **scheda Anteprima**puoi scegliere un pubblico di **anteprima** limitato per convalidare la tua offerta prima di pubblicarla in diretta al pubblico del marketplace più ampio.

> [!IMPORTANT]
> Dopo aver visualizzato l'offerta in Anteprima, è necessario selezionare **Vai in diretta** per pubblicare l'offerta al pubblico.

Specificare il gruppo di destinatari di anteprima usando i GUID dell'ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi può essere visto dai clienti.

> [!NOTE]
> È possibile trovare l'ID sottoscrizione di Azure nella pagina Sottoscrizioni nel portale di Azure.You can find your Azure subscription ID on the Subscriptions page in the Azure portal.

Aggiungere almeno un ID sottoscrizione di Azure singolarmente (fino a 10) o caricando un file CSV (fino a 100). Aggiungendo questi ID sottoscrizione, è possibile definire chi può visualizzare in anteprima l'offerta prima che venga pubblicata dal vivo. Se la tua offerta è già attiva, puoi definire un pubblico di anteprima per testare le modifiche o gli aggiornamenti alla tua offerta.

> [!NOTE]
> Il pubblico di anteprima è diverso da un pubblico privato. Un pubblico di **anteprima** può visualizzare e confermare tutti i piani di offerta prima che siano attivi nel marketplace, inclusi quelli che verranno pubblicati solo per un pubblico **privato** (impostato nella scheda Disponibilità).

Selezionare **Salva bozza** prima di passare alla sezione successiva, Panoramica del piano.

### <a name="plan-overview"></a>Panoramica del piano

Questa scheda consente di fornire diverse opzioni di piano all'interno della stessa offerta nel Centro per i partner. Questi piani, sono stati precedentemente indicati come SKU, o unità di conservazione delle scorte. I piani possono differire in termini di cloud disponibili, ad esempio cloud globali, cloud governativi e l'immagine a cui fa riferimento il piano. Per elencare l'offerta nel marketplace, è necessario impostare almeno un piano.

Dopo aver creato i piani, la scheda **Panoramica piano** mostra:

- Nomi dei piani
- Modello di prezzi
- Disponibilità cloud (globale o governativo)
- Stato pubblicazione corrente
- Eventuali azioni disponibili

Le azioni disponibili nella panoramica del piano variano a seconda dello stato corrente del piano. Tali impostazioni includono:

- **Elimina bozza**: Se lo stato del piano è Bozza.
- **Interrompi vendita piano**: Se lo stato del piano viene pubblicato in tempo reale.

#### <a name="create-new-plan"></a>Crea nuovo piano

Selezionare **Crea nuovo piano**. Viene visualizzata la finestra di dialogo **Nuovo piano.**

Nella casella **ID piano** creare un ID piano univoco per ogni piano dell'offerta. Questo ID sarà visibile ai clienti nell'indirizzo Web del prodotto. Utilizzare solo lettere minuscole e numeri, trattini o caratteri di sottolineatura e un massimo di 50 caratteri.

Nella casella **Nome piano** immettere un nome per il piano. I clienti vedono questo nome quando decidono quale piano selezionare all'interno dell'offerta. Creare un nome univoco per ogni piano in questa offerta. Ad esempio, è possibile utilizzare un nome di offerta di **Windows Server** con piani Windows **Server 2016** e **Windows Server 2019**.

> [!NOTE]
> L'ID piano non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Create** (Crea).

### <a name="plan-setup"></a>Pianificare l'installazione

Questa scheda consente di configurare i cloud in cui è disponibile il piano. Le risposte in questa scheda influiscono sui campi visualizzati in altre schede.

#### <a name="cloud-availability"></a>Disponibilità del cloud

Il piano deve essere disponibile in almeno un cloud usando l'hub IoT di Azure.Your plan must be available in at least one cloud using Azure IoT Hub.

Selezionare l'opzione **Azure globale** in modo che il piano possa essere usato dai clienti in tutte le aree globali di Azure che usano il marketplace. Per informazioni dettagliate, vedere [Disponibilità geografica e supporto valutario](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selezionare l'opzione [Cloud di Azure per](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) annunciare la soluzione qui. Si tratta di un cloud di comunità governativa con accesso controllato per i clienti provenienti da agenzie governative federali, statali e locali o tribali degli Stati Uniti, nonché partner idonei a servirli. In qualità di editore, sei responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate per questa community cloud. Azure per enti pubblici usa data center e reti fisicamente isolati (solo negli Stati Uniti). Prima di [pubblicare in](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) Azure per enti pubblici, testare e confermare la soluzione all'interno di tale area poiché i risultati potrebbero essere diversi. Per eseguire la fase e testare la soluzione, richiedere un account di valutazione dalla versione di valutazione di [Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo che il piano è stato pubblicato e disponibile in un cloud specifico, non è possibile rimuoverlo.

#### <a name="azure-government-cloud-certifications"></a>Certificazioni cloud di Azure per enti pubblici

Questa opzione è visibile solo se in **Disponibilità cloud**è selezionata **l'opzione Azure Government Cloud** .

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinate normative e requisiti governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per dare consapevolezza alle certificazioni per questi programmi, è possibile fornire fino a 100 link che descrivono le certificazioni. Questi possono essere link ai tuoi annunci sul programma direttamente o al tuo sito web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

## <a name="plan-listing"></a>Pianificare l'inserzione

Questa scheda visualizza informazioni specifiche per ogni piano diverso all'interno della stessa offerta.

### <a name="plan-name"></a>Nome piano

Questo è precompilato con il nome che hai dato al tuo piano quando l'hai creato. È possibile modificare questo nome, in base alle esigenze. Può avere una lunghezza fino a 50 caratteri. Questo nome viene visualizzato come titolo di questo piano in Azure Marketplace e nel portale di Azure.This name appears as the title of this plan in Azure Marketplace and Azure portal. Viene utilizzato come nome del modulo predefinito dopo che il piano è pronto per essere utilizzato.

### <a name="plan-summary"></a>Riepilogo del piano

Fornisci un breve riepilogo del tuo piano (non l'offerta). Questo riepilogo viene visualizzato nei risultati della ricerca di Azure Marketplace e può contenere fino a 100 caratteri.

### <a name="plan-description"></a>Descrizione del piano

Descrivi cosa rende unico questo piano e le differenze tra i piani all'interno della tua offerta. Non descrivere l'offerta, solo il piano. Questa descrizione verrà visualizzata in Azure Marketplace e nel portale di Azure nella pagina Elenco offerte. Può essere lo stesso contenuto fornito nel riepilogo del piano e contenere fino a 2.000 caratteri.

Selezionare **Salva bozza** dopo aver completato questi campi.

#### <a name="plan-examples"></a>Esempi di piano

Negli esempi seguenti viene illustrato come i campi di elenco dei piani vengono visualizzati in visualizzazioni diverse.

Di seguito sono riportati i campi in Azure Marketplace quando si visualizzano i dettagli del piano:These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Illustra i campi visualizzati quando si visualizzano i dettagli del piano in Azure Marketplace.":::

Questi sono i dettagli del piano nel portale di Azure:These are plan details on the Azure portal:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Illustra i dettagli del piano nel portale di Azure.Illustrates plan details on the Azure portal.":::

## <a name="availability"></a>Disponibilità

Se si desidera nascondere l'offerta pubblicata in modo che i clienti non possano cercarla, esplorarla o acquistarla nel marketplace, selezionare la casella di controllo **Nascondi piano** nella scheda Disponibilità.

Questo campo viene comunemente utilizzato quando:

- L'offerta deve essere utilizzata solo indirettamente quando viene fatto riferimento tramite un'altra applicazione.
- L'offerta non deve essere acquistata singolarmente.
- Il piano è stato utilizzato per i test iniziali e non è più rilevante.
- Il piano è stato utilizzato per offerte temporanee o stagionali e non dovrebbe più essere offerto.

## <a name="technical-configuration"></a>Configurazione tecnica

Il tipo di offerta del modulo IoT Edge è un tipo specifico di contenitore che viene eseguito in un dispositivo IoT Edge.The **IoT Edge module** offer type is a specific type of container that runs on an IoT Edge device. Nella scheda **Configurazione tecnica** verranno fornite informazioni di riferimento per il repository di immagini del contenitore all'interno del Registro di sistema contenitori di [Azure,](https://azure.microsoft.com/services/container-registry/)insieme alle impostazioni di configurazione che consentono ai clienti di utilizzare facilmente il modulo.

Dopo la pubblicazione dell'offerta, l'immagine del contenitore IoT Edge viene copiata in Azure Marketplace in un registro dei contenitori pubblici specifico. Tutte le richieste degli utenti di Azure di usare il modulo vengono servite dal Registro di sistema dei contenitori pubblici di Azure Marketplace, non dal registro dei contenitori privati.

È possibile scegliere come destinazione più piattaforme e fornire diverse versioni dell'immagine del contenitore del modulo usando i tag. Per ulteriori informazioni sui tag e sul controllo delle versioni, consultate Preparare le risorse tecniche del [modulo IoT Edge.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset)

### <a name="image-repository-details"></a>Dettagli del repository di immagini

Nella scheda **Dettagli repository immagini** verranno fornite le informazioni seguenti.

**Selezionare l'origine dell'immagine:** selezionare l'opzione Registro di sistema **del contenitore** di Azure.Select the image source : Select the Azure Container Registry option.

**ID sottoscrizione di Azure:** specificare l'ID sottoscrizione in cui viene segnalato l'utilizzo delle risorse e i servizi vengono fatturati per il Registro di sistema del contenitore di Azure che include l'immagine del contenitore. È possibile trovare questo ID nella pagina Sottoscrizioni nel portale di Azure.You can find this ID on the [Subscriptions page](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in the Azure portal.

**Nome del gruppo di risorse di Azure:** specificare il nome del gruppo di [risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) che contiene il Registro di sistema del contenitore di Azure con l'immagine del contenitore. Il gruppo di risorse deve essere accessibile nell'ID sottoscrizione (sopra). È possibile trovare il nome nella pagina Gruppi di risorse nel portale di Azure.You can find the name on the [Resource groups](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) page in the Azure portal.

**Nome del Registro di sistema del contenitore di Azure:** specificare il nome del Registro di sistema [del contenitore](https://docs.microsoft.com/azure/container-registry/container-registry-intro) di Azure con l'immagine del contenitore. Il registro contenitori deve essere presente nel gruppo di risorse di Azure fornito in precedenza. Specificare solo il nome del Registro di sistema, non il nome completo del server di accesso. Assicurarsi di omettere **azurecr.io** dal nome. È possibile trovare il nome del Registro di sistema nella pagina Registri contenitori nel portale di Azure.You can find the registry name on the [Container Registries page](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in the Azure portal.

**Nome utente di amministrazione per il Registro**di sistema del contenitore di Azure: fornire il [nome utente di amministratore](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) associato al Registro di sistema del contenitore di Azure con l'immagine del contenitore. Il nome utente e la password sono necessari per garantire che l'azienda abbia accesso al registro. Per ottenere il nome utente e la password dell'amministratore, impostare la proprietà **admin-enabled su** True usando l'interfaccia della riga di comando (CLI) di Azure.To get the admin username and password, set the admin-enabled property to **True** using the Azure Command-Line Interface (CLI). Facoltativamente, è possibile impostare Admin user su Enable nel portale di Azure.You can optionally set **Admin user** to **Enable** in the Azure portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="Viene illustrata la finestra di dialogo Aggiorna registro contenitori.":::

**Password per il Registro**di sistema del contenitore di Azure: fornire la password per il nome utente di amministratore associato al Registro di sistema del contenitore di Azure e con l'immagine del contenitore. Il nome utente e la password sono necessari per garantire che l'azienda abbia accesso al registro. È possibile ottenere la password dal portale di Azure accedendo a Chiavi di accesso al **Registro di sistema del contenitore** > o con l'interfaccia della riga di comando di Azure usando il [comando show.You](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show) can get the password from the Azure portal by going to Container Registry**Access Keys** or with Azure CLI using the show command.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Illustra la schermata del tasto di scelta nel portale di Azure.Illustrates the access key screen in Azure portal.":::

**Nome del repository all'interno del Registro**di sistema del contenitore di Azure . Specificare il nome del repository del Registro di sistema del contenitore di Azure con l'immagine. Specificare il nome del repository quando si esegue il push dell'immagine nel Registro di sistema. È possibile trovare il nome del repository accedendo alla pagina Repository [del Registro](https://azure.microsoft.com/services/container-registry/) > **contenitori**. Per altre informazioni, vedere Visualizzare i repository del Registro di sistema dei contenitori nel portale di Azure.For more information, see [View container registry repositories in the Azure portal](https://docs.microsoft.com/azure/container-registry/container-registry-repositories). Si noti che dopo aver impostato il nome, non può essere modificato. Utilizza un nome univoco per ogni offerta nel tuo account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tag immagine per le nuove versioni dell'offerta

I clienti devono essere in grado di ottenere automaticamente gli aggiornamenti da Azure Marketplace quando si pubblica un aggiornamento. Se non vogliono aggiornare, devono essere in grado di rimanere su una versione specifica dell'immagine. È possibile farlo aggiungendo nuovi tag immagine ogni volta che si effettua un aggiornamento all'immagine.

**Tag immagine**. Questo campo deve includere un tag **più recente** che punta alla versione più recente dell'immagine su tutte le piattaforme supportate. Deve inoltre includere un tag di versione (ad esempio, a partire da xx.xx.xx, dove xx è un numero). I clienti devono usare [i tag manifesto](https://github.com/estesp/manifest-tool) per scegliere come target più piattaforme. È necessario aggiungere anche tutti i tag referenziati da un tag di manifesto per poterli caricare. Tutti i tag del manifesto (ad eccezione del tag più recente) devono iniziare con X.Y- o X.Y. - dove X, Y, e s sono numeri interi. Ad esempio, se un tag più recente punta a 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, questi sei tag devono essere aggiunti a questo campo. Per informazioni dettagliate sui tag e sul controllo delle versioni, vedere Preparare le risorse tecniche del [modulo IoT Edge.For](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) details about tags and versioning, see Prepare your IoT Edge module technical assets.

### <a name="default-deployment-settings-optional"></a>Impostazioni di distribuzione predefinite (facoltativo)Default deployment settings (optional)

Definire le impostazioni più comuni per distribuire il modulo IoT Edge. Ottimizza le distribuzioni dei clienti consentendo loro di avviare il modulo IoT Edge out-of-the-box con queste impostazioni predefinite.

**Route predefinite**. L'hub Edge IoT gestisce la comunicazione tra i moduli, l'hub IoT e i dispositivi. È possibile impostare route per l'input e l'output dei dati tra i moduli e l'hub IoT, che offre la flessibilità di inviare messaggi dove devono andare senza la necessità di servizi aggiuntivi per elaborare messaggi o scrivere codice aggiuntivo. Le route vengono costruite utilizzando coppie nome/valore. È possibile definire fino a cinque nomi di route predefiniti, ognuno di 512 caratteri.

Assicurarsi di utilizzare la [sintassi](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) di route corretta nel valore della route (in genere definita come FROM/message/' INTO $upstream). Ciò significa che tutti i messaggi inviati da qualsiasi modulo vengono inviati all'hub IoT. Per fare riferimento al modulo, utilizzare il nome predefinito del modulo, che sarà il **nome dell'offerta,** senza spazi o caratteri speciali. Per fare riferimento ad altri moduli che non sono ancora noti, utilizzare la convenzione <FROM_MODULE_NAME> per indire ai clienti che è necessario aggiornare queste informazioni. Per informazioni dettagliate sulle route IoT Edge, consultate [Dichiarare route.](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

Ad esempio, se il modulo ContosoModule è in ascolto degli input in ContosoInput e dei dati di output in ContosoOutput, è opportuno definire le due route predefinite seguenti:For example, if module ContosoModule listens for inputs on ContosoInput and output data at ContosoOutput, it sense to define the following two default routes:

- Nome #1: ToContosoModule
- Valore #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/'INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nome #2: FromContosoModuleToCloudName #2: FromContosoModuleToCloud
- #2 dei valori: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Proprietà predefinite del modulo gemello .** Un modulo gemello è un documento JSON nell'hub IoT che archivia le informazioni sullo stato per un'istanza del modulo, incluse le proprietà desiderate. Le proprietà desiderate vengono utilizzate insieme alle proprietà segnalate per sincronizzare la configurazione o le condizioni del modulo. Il back-end della soluzione può impostare le proprietà desiderate e il modulo può leggerle. Il modulo può anche ricevere notifiche di modifica nelle proprietà desiderate. Le proprietà desiderate vengono create utilizzando fino a cinque coppie nome/valore e ogni valore predefinito deve essere inferiore a 512 caratteri. È possibile definire fino a cinque proprietà desiderate nome/valore gemello. I valori delle proprietà desiderate gemelle devono essere JSON validi, senza caratteri di escape, senza matrici con una gerarchia annidata massima di quattro livelli. In uno scenario in cui un parametro necessario per un valore predefinito non ha senso (ad esempio, l'indirizzo IP del server di un cliente), è possibile aggiungere un parametro come valore predefinito. Per ulteriori informazioni sulle proprietà gemelle desiderate, consultate [Definire o aggiornare le proprietà desiderate.](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)

Ad esempio, se un modulo supporta una frequenza di aggiornamento configurabile dinamicamente utilizzando le proprietà do desiderate, è opportuno definire la seguente proprietà doppia desiderata predefinita:For example, if a module supports a dynamically configurable refresh rate using twin desired properties, it sense to define the following default twin desired property:

- Nome #1: RefreshRate
- Valore #1: 60

**Variabili di ambiente predefinite**. Le variabili di ambiente forniscono informazioni aggiuntive a un modulo che aiuta il processo di configurazione. Le variabili di ambiente vengono create utilizzando coppie nome/valore. Ogni valore e il nome della variabile di ambiente predefinita devono essere inferiori a 512 caratteri ed è possibile definire fino a cinque. Quando un parametro necessario per un valore predefinito non ha senso (ad esempio, l'indirizzo IP del server di un cliente), è possibile aggiungere un parametro come valore predefinito.

Se ad esempio un modulo richiede di accettare le condizioni d'uso prima dell'avvio, è possibile definire la variabile di ambiente seguente:

- Nome #1: ACCEPT_EULA
- Valore #1: Y

**Opzioni di creazione contenitore predefinite**. Le opzioni di creazione del contenitore indirizzano la creazione del contenitore Docker del modulo IoT Edge. IoT Edge supporta le opzioni Create Container dell'API del motore Docker. Vedere tutte le opzioni in [Contenitori di elenchi.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Il campo delle opzioni di creazione deve essere un JSON valido, un non di escape e meno di 512 caratteri.

Ad esempio, se un modulo richiede l'associazione di porta, definire le opzioni di creazione seguenti:For example, if a module requires port binding, define the following create options:

"HostConfig": "PortBindings": "5012/tcp":"PortaHost":"5012"

## <a name="review-and-publish"></a>Revisione e pubblicazione

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, puoi inviarla per la revisione e la pubblicazione.

Nell'angolo superiore destro del portale selezionare **Revisione e pubblicazione**.

Nella pagina di revisione è possibile visualizzare lo stato di pubblicazione:

- Vedi lo stato di completamento per ogni sezione dell'offerta. Non puoi pubblicare finché tutte le sezioni dell'offerta non vengono contrassegnate come complete.
    - **Non avviato:** la sezione non è stata avviata e deve essere completata.
    - **Incompleto:** la sezione contiene errori che devono essere corretti o richiede di fornire ulteriori informazioni. Per istruzioni, vedere le sezioni precedenti in questo documento.
    - **Completo** - La sezione contiene tutti i dati richiesti e non ci sono errori. Tutte le sezioni dell'offerta devono essere complete prima di poter inviare l'offerta.
- Fornisci le istruzioni di test al team di certificazione per assicurarti che la tua offerta sia testata correttamente. Inoltre, fornisci eventuali note supplementari utili per comprendere la tua offerta.

Per inviare l'offerta per la pubblicazione, selezionare **Pubblica**.

Ti invieremo un'email per comunicarti quando è disponibile una versione di anteprima dell'offerta per la revisione e l'approvazione. Per pubblicare l'offerta al pubblico (o se un'offerta privata, a un pubblico privato), vai al Centro per i partner e seleziona **Go-live**.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel mercato commerciale](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)