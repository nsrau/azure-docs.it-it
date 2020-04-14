---
title: Creare un'offerta di contenitore di Azure nel Centro per i partner - Azure MarketplaceCreate an Azure container offer in Partner Center - Azure Marketplace
description: Questo articolo descrive come creare e pubblicare un'offerta Contenitore per Azure Marketplace.This article describes how to create and publish a Container offer for Azure Marketplace.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266318"
---
# <a name="create-an-azure-container-offer"></a>Creare un'offerta di contenitore di AzureCreate an Azure Container offer

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte di Azure Container dal portale per i partner cloud al Centro per i partner. Fino alla migrazione delle offerte, segui le istruzioni in [Contenitori](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) per cloud Partner Portal per gestire le tue offerte.

Questo articolo descrive come creare e pubblicare un'offerta Contenitore per Azure Marketplace.This article describes how to create and publish a Container offer for Azure Marketplace. Prima di iniziare, [creare un account Mercato commerciale nel Centro per i partner.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per](https://partner.microsoft.com/dashboard/home)i partner , quindi nel menu in alto selezionare **Dashboard**.
2. Nel menu a sinistra selezionare **Mercato commerciale**, quindi **Panoramica**.
3. Nella pagina **Panoramica** selezionare **Nuova offerta**, quindi **Contenitore di Azure**. Viene visualizzata la finestra di dialogo **Nuova offerta.**

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="È illustrata la pagina Panoramica nel Centro per i partner. Vengono evidenziati il pulsante Nuova offerta e l'offerta di servizio di consulenza.":::

> [!TIP]
> Dopo la pubblicazione di un'offerta, le modifiche apportate nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurati di ripubblicare sempre dopo aver apportato le modifiche.

### <a name="offer-id-and-alias"></a>ID e alias dell'offerta

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nel tuo account.

- Questo ID può essere visualizzato dai clienti nell'indirizzo Web per l'offerta del marketplace e dai modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi, ed è limitato a 50 caratteri. Ad esempio, se si immette **test-offer-1**, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`l'indirizzo Web dell'offerta sarà .
- L'ID offerta non può essere modificato dopo aver selezionato **Crea**.

**Immettere un** **alias di offerta**. Questo è il nome usato per fare riferimento all'offerta nel Centro per i partner.

- Questo nome non viene utilizzato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Questa operazione non può essere modificata dopo aver selezionato **Crea**.

Selezionare **Crea** prima di continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica dell'offerta** mostra una rappresentazione visiva dei passaggi necessari per pubblicare questa offerta (sia completata che successiva) e della durata del completamento di ogni passaggio.

Questa pagina mostra diversi link in base allo stato corrente dell'offerta. Ad esempio:

- Se l'offerta è una bozza - [Elimina bozza di offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è in diretta - [Smetti di vendere l'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se non hai completato la disconnessione del publisher - [Annulla pubblicazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configurazione dell'offerta

Segui questi passaggi per configurare la tua offerta.

### <a name="connect-lead-management--optional"></a>Gestione dei lead Connect – opzionale

Quando pubblichi l'offerta nel marketplace con il Centro per i partner, puoi connetterla al tuo sistema CRM (Customer Relationship Management). Ciò consente di ricevere le informazioni di contatto del cliente non appena qualcuno esprime interesse o utilizza il prodotto.

1. **Selezionare una destinazione lead in cui si desidera inviare lead clienti.** Il Centro per i partner supporta i seguenti sistemi CRM:

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
- [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Assicurarsi che il blocco popup sia disattivato)

Selezionare **Salva bozza** prima di continuare con la sezione successiva, Proprietà.

### <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie utilizzate per raggruppare l'offerta sul mercato e i contratti legali che supportano l'offerta.

#### <a name="category"></a>Category

Selezionare un minimo di una e un massimo di cinque categorie. Queste categorie vengono utilizzate per inserire l'offerta nelle aree di ricerca del marketplace appropriate e vengono visualizzate nella pagina dei dettagli dell'offerta. Nella descrizione dell'offerta, spiega come la tua offerta supporta queste categorie. I contenitori vengono **visualizzati** in Contenitori e quindi nella categoria **Immagini contenitore.**

#### <a name="legal"></a>Note legali

È necessario fornire i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- Utilizzare il contratto standard per il marketplace commerciale Microsoft.
- Fornire i propri termini e condizioni.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per il marketplace commerciale Microsoft

Offriamo un modello di contratto standard per facilitare le transazioni sul mercato commerciale. Puoi scegliere di offrire la tua soluzione in base al Contratto Standard, che i clienti devono solo controllare e accettare una volta. Questa è una buona opzione se non si desidera creare termini e condizioni personalizzati.

Per ulteriori informazioni sul contratto standard, vedere [Contratto standard per il marketplace commerciale Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Puoi anche scaricare il PDF [del contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assicurati che il blocco popup sia disattivato).

Per utilizzare il contratto standard, selezionare la casella di controllo **Usa contratto standard per** il mercato commerciale di Microsoft e quindi fare clic su **Accetta**.

> [!NOTE]
> Dopo aver pubblicato un'offerta utilizzando il contratto Standard per Microsoft Commercial Marketplace, non è possibile utilizzare i termini e le condizioni personalizzati. Offri la tua soluzione in base al Contratto Standard o ai tuoi termini e condizioni.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Viene illustrata la casella di controllo Utilizzo del contratto standard per il marketplace commerciale di Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>I tuoi termini e condizioni

Per fornire i termini e le condizioni personalizzati, immetterli nella casella **Termini e condizioni.** È possibile immettere un numero illimitato di caratteri di testo in questa casella. I clienti devono accettare questi termini prima di poter provare la tua offerta.

Seleziona **Salva bozze** prima di continuare con la sezione successiva, Offerta.

## <a name="offer-listing"></a>Inserzione dell'offerta

Questa pagina consente di definire i dettagli dell'offerta visualizzati nel marketplace commerciale. Sono inclusi il nome, la descrizione e le immagini dell'offerta.

> [!NOTE]
> I dettagli dell'offerta non sono necessari per essere in inglese se la descrizione dell'offerta inizia con la frase "Questa applicazione è disponibile solo in [lingua non in lingua inglese]". Va anche bene fornire un Link utile per offrire contenuti in una lingua diversa da quella utilizzata nei dettagli dell'inserzione Offerta.

### <a name="name"></a>Nome

Il nome che inserisci qui viene visualizzato come titolo dell'offerta. Questo campo è precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Il nome:

- Può essere registrato (e l'utente può includere simboli di marchio e copyright).
- Non può essere lungo più di 50 caratteri.
- Non è possibile includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Breve descrizione dell'offerta. Può essere lungo fino a 100 caratteri e viene utilizzato nei risultati di ricerca del marketplace.

### <a name="long-summary"></a>Riassunto lungo

Una descrizione più dettagliata della tua offerta. Può essere lungo fino a 256 caratteri e viene utilizzato nei risultati di ricerca del marketplace.

### <a name="description"></a>Descrizione

Fornisci una descrizione più lunga della tua offerta, fino a 3.000 caratteri. Viene visualizzato dai clienti nella panoramica dell'elenco dei marketplace.

Includere una o più delle seguenti opzioni nella descrizione:

- Il valore e i vantaggi principali che la tua offerta offre
- Associazioni di categoria o di settore, o entrambe
- Opportunità di acquisto in-app
- Eventuali divulgazioni obbligatorie

Ecco alcuni suggerimenti per scrivere la tua descrizione:

- Descrivi chiaramente il valore della tua offerta nelle prime frasi della tua descrizione. Includere i seguenti elementi:
  - Descrizione dell'offerta.
  - Il tipo di utente che beneficia dell'offerta
  - Il cliente ha bisogno o emette gli indirizzi dell'offerta.
- Ricorda che le prime frasi potrebbero essere visualizzate nei risultati della ricerca.
- Non fare affidamento su caratteristiche e funzionalità per vendere il tuo prodotto. Concentrati invece sul valore offerto dalla tua offerta.
- Prova a usare il vocabolario specifico del settore o la formulazione basata sui vantaggi.

Per rendere più coinvolgente la **descrizione** dell'offerta, usa l'editor RTF per formattare la descrizione. con numerazione, elenchi puntati, grassetto, corsivo e rientri per rendere la descrizione più leggibile.

:::image type="content" source="media/text-editor2.png" alt-text="Illustra l'editor RTF." border="false" :::

- Utilizzate questo menu a discesa per applicare uno stile di paragrafo al testo.

    :::image type="content" source="media/text-editor3.png" alt-text="Illustra il controllo dello stile di testo nell'editor RTF." border="false":::

- Utilizzare queste icone per applicare elenchi puntati o puntati al testo.

     :::image type="content" source="media/text-editor4.png" alt-text="Illustra i controlli elenco puntato e elenco numeri nell'editor RTF." border="false":::

- Utilizzare queste icone per aggiungere o rimuovere il rientro da o verso il testo.

    :::image type="content" source="media/text-editor5.png" alt-text="Illustra i controlli di rientro nell'editor RTF." border="false":::

#### <a name="privacy-policy-link"></a>Collegamento all'informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. Hai la responsabilità di garantire che la tua offerta sia conforme alle leggi e ai regolamenti sulla privacy. Sei anche responsabile per la pubblicazione di una politica sulla privacy valida sul tuo sito web.

#### <a name="useful-links"></a>Collegamenti utili

Fornisci documenti online supplementari sulla tua offerta. È possibile aggiungere fino a 25 collegamenti. Per aggiungere un collegamento, selezionare **Aggiungi un collegamento** e quindi completare i seguenti campi:

- **Titolo:** i clienti lo vedranno nella pagina dei dettagli dell'offerta.
- **Collegamento (URL):** immettere un collegamento per la visualizzazione del documento online da parte dei clienti. Il collegamento deve iniziare con http:// o https://.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario fornire il nome, l'indirizzo e-mail e il numero di telefono per un **contatto di supporto** e un contatto **Engineering**. Queste informazioni non vengono mostrate ai clienti, ma sono disponibili per Microsoft.This information isn't shown to customers but is available to Microsoft. Può anche essere fornito ai partner Cloud Solution Provider (CSP).

- Contatto di supporto (obbligatorio): per domande generali sul supporto tecnico.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto del programma CSP (facoltativo): per le domande dei rivenditori relative al programma CSP.

Nella sezione **Contatto di supporto** fornire il sito Web di **supporto** in cui i partner possono trovare supporto per l'offerta in base al fatto che l'offerta sia disponibile in Azure globale, Azure per enti pubblici o in entrambi.

Nella sezione di contatto del **programma CSP,** fornire il collegamento ( **CSP Program Marketing Materials** ) in cui i partner CSP possono trovare materiale di marketing per la tua offerta.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione del marketplace

Per ulteriori informazioni sulla creazione di inserzioni di offerte, vedi [Best practice per le inserzioni di offerte](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornisci loghi e immagini da utilizzare con la tua offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate verranno rifiutate.

#### <a name="store-logos"></a>Loghi del Negozio

 Fornisci i file PNG del logo della tua offerta in ognuna delle seguenti quattro dimensioni in pixel:

- **Piccolo** (48 x 48)
- **Medio** (90 x 90)
- **Grande** (216 x 216)
- **Ampio** (255 X 115)

Tutti e quattro i loghi sono obbligatori e vengono utilizzati in luoghi diversi nella presentazione del marketplace.

#### <a name="screenshots-optional"></a>Screenshot (facoltativo)

Aggiungi fino a cinque screenshot che mostrano come funziona la tua offerta. Ciascuno deve avere dimensioni 1280 x 720 pixel e in formato PNG.

#### <a name="videos-optional"></a>Video (opzionale)

Aggiungi fino a cinque video che dimostrano la tua offerta. Inserisci il nome del video, il suo indirizzo web e un'immagine PNG in miniatura del video con dimensioni di 1280 x 720 pixel.

#### <a name="offer-examples"></a>Esempi di offerta

Gli esempi seguenti mostrano come i campi dell'offerta vengono visualizzati in luoghi diversi dell'offerta.

Viene visualizzata la pagina Elenco offerte in Azure Marketplace:This shows the **Offer listing** page in Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Illustra la pagina di presentazione dell'offerta in Azure Marketplace." :::

Vengono visualizzati i risultati della ricerca in Azure Marketplace:This shows search results in Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Illustra i risultati della ricerca in Azure Marketplace.Illustrates the search results in Azure Marketplace.":::

Viene visualizzata la pagina Elenco offerta nel portale di Azure:This shows the **Offer listing** page in Azure portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Illustra la pagina di elenco Offerta nel portale di Azure.Illustrates the Offer listing page in Azure portal.":::

Vengono visualizzati i risultati della ricerca nel portale di Azure:This shows search results in Azure portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Illustra i risultati della ricerca nel portale di Azure.Illustrates the search results in Azure portal.":::

## <a name="preview"></a>Anteprima

Nella scheda Anteprima, puoi scegliere un pubblico di **anteprima** limitato per la convalida dell'offerta prima di pubblicarla dal vivo.

> [!IMPORTANT]
> Dopo aver visualizzato l'offerta in **Anteprima,** è necessario selezionare **Vai in diretta** per pubblicare l'offerta al pubblico.

Specificare il gruppo di destinatari di anteprima usando i GUID dell'ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi può essere visto dai clienti.

> [!NOTE]
> È possibile trovare l'ID sottoscrizione di Azure nella pagina Sottoscrizioni nel portale di Azure.You can find your Azure subscription ID on the Subscriptions page in Azure portal.

Aggiungere almeno un ID sottoscrizione di Azure singolarmente (fino a 10) o caricando un file CSV (fino a 100). Aggiungendo questi ID sottoscrizione, è possibile determinare chi può visualizzare in anteprima l'offerta prima che venga pubblicata dal vivo. Se la tua offerta è già attiva, puoi scegliere un pubblico di anteprima per testare le modifiche o gli aggiornamenti alla tua offerta.

> [!NOTE]
> Il pubblico di anteprima è diverso da un pubblico privato. Un pubblico di **anteprima** può visualizzare e confermare tutti i piani di offerta prima che siano attivi nel marketplace, inclusi quelli che verranno pubblicati solo per un pubblico **privato** (impostato nella scheda Disponibilità).

Selezionare **Salva bozza** prima di continuare.

### <a name="plan-overview"></a>Panoramica del piano

Questa scheda consente di fornire diverse opzioni di piano all'interno della stessa offerta. Questi piani sono stati precedentemente indicati come SKU, o unità di conservazione delle scorte. I piani possono differire in termini di cloud disponibili, ad esempio cloud globali, cloud governativi e l'immagine a cui fa riferimento il piano. Per elencare l'offerta nel mercato commerciale, è necessario impostare almeno un piano.

Dopo aver creato i piani, la scheda **Panoramica piano** mostra:

- Nomi dei piani
- Modello di prezzi
- Disponibilità cloud (globale o governativo)
- Stato pubblicazione corrente
- Eventuali azioni disponibili

Le azioni disponibili nella panoramica del piano variano a seconda dello stato corrente del piano. Tali impostazioni includono:

- **Elimina bozza:** se lo stato del piano è Bozza.
- **Interrompi vendita piano:** se lo stato del piano è pubblicato in tempo reale.

#### <a name="create-new-plan"></a>Crea nuovo piano

Selezionare **Crea nuovo piano**. Viene visualizzata la finestra di dialogo **Nuovo piano.**

Nella casella **ID piano** creare un identificatore di piano univoco per ogni piano dell'offerta. Questo ID sarà visibile ai clienti nell'indirizzo Web del prodotto. Utilizzare solo lettere minuscole e numeri, trattini o caratteri di sottolineatura e un massimo di 50 caratteri.

> [!NOTE]
> L'ID piano non può essere modificato dopo aver selezionato **Crea**.

Nella casella **Nome piano** immettere un nome per il piano. I clienti vedono questo nome quando decidono quale piano selezionare all'interno dell'offerta. Creare un nome univoco per ogni piano in questa offerta. Ad esempio, è possibile utilizzare un nome di offerta di **Windows Server** con piani Windows **Server 2016** e **Windows Server 2019**.

### <a name="plan-setup"></a>Pianificare l'installazione

Questa scheda consente di scegliere in quali cloud è disponibile il piano. Le risposte in questa scheda influiscono sui campi visualizzati in altre schede.

#### <a name="cloud-availability"></a>Disponibilità del cloud

Il piano deve essere disponibile in almeno un cloud.

Selezionare l'opzione **Azure globale** in modo che il piano possa essere usato dai clienti in tutte le aree globali di Azure che usano il marketplace commerciale. Per informazioni dettagliate, vedere [Disponibilità geografica e supporto valutario](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selezionare l'opzione [**Cloud di Azure per**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) annunciare la soluzione qui. Si tratta di un cloud di comunità governativa con accesso controllato per i clienti provenienti da agenzie governative federali, statali e locali o tribali degli Stati Uniti, nonché partner idonei a servirli. In qualità di editore, sei responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate per questa community cloud. Azure per enti pubblici usa data center e reti fisicamente isolati (solo negli Stati Uniti).

Prima di [pubblicare in](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) Azure per enti pubblici, testare e confermare la soluzione all'interno di tale area poiché i risultati potrebbero essere diversi. Per creare e testare la soluzione, richiedere un account di valutazione dalla versione di valutazione di [Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo che il piano è stato pubblicato e disponibile in un cloud specifico, non è possibile rimuoverlo.

#### <a name="azure-government-cloud-certifications"></a>Certificazioni cloud di Azure per enti pubblici

Questa opzione può essere visualizzata solo se in **Disponibilità cloud**è selezionata **l'opzione Azure Government Cloud** .

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinate normative e requisiti governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.

Per visualizzare le certificazioni per questi programmi, è possibile fornire fino a 100 collegamenti che li descrivono. Questi possono essere link ai tuoi annunci sul programma direttamente o al tuo sito web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

## <a name="plan-listing"></a>Pianificare l'inserzione

Questa scheda visualizza informazioni specifiche per ogni piano diverso all'interno dell'offerta corrente.

### <a name="plan-name"></a>Nome piano

Questo è precompilato con il nome che hai dato al tuo piano quando l'hai creato. È possibile modificare questo nome in base alle esigenze. Può avere una lunghezza fino a 50 caratteri. Questo nome viene visualizzato come titolo di questo piano in Azure Marketplace e nel portale di Azure.This name appears as the title of this plan in Azure Marketplace and Azure portal. Viene utilizzato come nome del modulo predefinito dopo che il piano è pronto per essere utilizzato.

### <a name="plan-summary"></a>Riepilogo del piano

Un breve riepilogo del piano software (non dell'offerta). Questo riepilogo viene visualizzato nei risultati della ricerca di Azure Marketplace e può contenere fino a 100 caratteri.

### <a name="plan-description"></a>Descrizione del piano

Descrivi cosa rende unico questo piano software, nonché le differenze tra i piani all'interno della tua offerta. Non descrivere l'offerta, solo il piano. Questa descrizione verrà visualizzata in Azure Marketplace e nel portale di Azure nella pagina **Elenco offerte.** Può essere lo stesso contenuto fornito nel riepilogo del piano e contenere fino a 2.000 caratteri.

Selezionare **Salva** dopo aver completato questi campi.

#### <a name="plan-examples"></a>Esempi di piano

Negli esempi seguenti viene illustrato come i campi di elenco dei piani vengono visualizzati in visualizzazioni diverse.

Di seguito sono riportati i campi in Azure Marketplace quando si visualizzano i dettagli del piano:These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Illustra i campi visualizzati quando si visualizzano i dettagli del piano in Azure Marketplace.":::

Questi sono i dettagli del piano nel portale di Azure:These are plan details on the Azure portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Illustra i dettagli del piano nel portale di Azure.Illustrates plan details on the Azure portal.":::

## <a name="plan-availability"></a>Pianificare la disponibilità

Se si desidera nascondere l'offerta pubblicata in modo che i clienti non possano cercarla, esplorarla o acquistarla nel marketplace, selezionare la casella di controllo **Nascondi piano** nella scheda **Disponibilità.**

Questo campo viene utilizzato quando:

- L'offerta deve essere utilizzata indirettamente quando vi viene fatto riferimento tramite un'altra applicazione.
- L'offerta non deve essere acquistata singolarmente.
- Il piano è stato utilizzato per i test iniziali e non è più rilevante.
- Il piano è stato utilizzato per offerte temporanee o stagionali e non dovrebbe più essere offerto.

## <a name="technical-configuration"></a>Configurazione tecnica

Le immagini del contenitore devono essere ospitate in un Registro di [sistema del contenitore](https://azure.microsoft.com/services/container-registry/)di Azure privato. Nella scheda Configurazione tecnica fornire informazioni di riferimento per il repository di immagini del contenitore all'interno del Registro di sistema del contenitore di Azure.On the **Technical Configuration** tab, provide reference information for your container image repository inside the Azure Container Registry.

Dopo la pubblicazione dell'offerta, l'immagine del contenitore viene copiata in Azure Marketplace in un registro dei contenitori pubblici specifico. Tutte le richieste di utilizzo dell'immagine del contenitore vengono soddisfatte dal Registro di sistema del contenitore pubblico di Azure Marketplace, non da quello privato. Per informazioni [dettagliate, vedere Preparare gli asset tecnici del contenitore di Azure.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

### <a name="image-repository-details"></a>Dettagli del repository di immagini

Fornire le seguenti informazioni nella scheda **Dettagli repository immagini.**

**ID sottoscrizione di Azure:** specificare l'ID sottoscrizione in cui viene segnalato l'utilizzo e i servizi vengono fatturati per il Registro di sistema del contenitore di Azure che include l'immagine del contenitore. È possibile trovare questo ID nella pagina Sottoscrizioni nel portale di Azure.You can find this ID on the [Subscriptions page](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in the Azure portal.

Nome del gruppo di **risorse di Azure:** specificare il nome del gruppo di [risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) che contiene il Registro di sistema del contenitore di Azure con l'immagine del contenitore. Il gruppo di risorse deve essere accessibile nell'ID sottoscrizione (sopra). È possibile trovare il nome nella pagina Gruppi di risorse nel portale di Azure.You can find the name on the [Resource groups](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) page in the Azure portal.

**Nome del Registro** di sistema del contenitore di Azure: specificare il nome del Registro di sistema [del contenitore](https://docs.microsoft.com/azure/container-registry/container-registry-intro) di Azure con l'immagine del contenitore. Il Registro di sistema del contenitore deve trovarsi nel gruppo di risorse di Azure fornito in precedenza. Includere solo il nome del Registro di sistema, non il nome completo del server di accesso. Assicurarsi di omettere **azurecr.io** dal nome. È possibile trovare il nome del Registro di sistema nella pagina Registri contenitori nel portale di Azure.You can find the registry name on the [Container Registries page](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in the Azure portal.

**Nome utente di amministrazione per il Registro** di sistema contenitore di Azure: fornire il [nome utente di amministratore](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) collegato al Registro di sistema del contenitore di Azure con l'immagine del contenitore. Il nome utente e la password sono necessari per garantire che l'azienda abbia accesso al registro. Per ottenere il nome utente e la password dell'amministratore, impostare la proprietà **admin-enabled su** True usando l'interfaccia della riga di comando (CLI) di Azure.To get the admin username and password, set the admin-enabled property to **True** using the Azure Command-Line Interface (CLI). Facoltativamente, è possibile impostare Utente amministratore su Abilita nel portale di Azure.You can optionally set **Admin user** to **Enable** in Azure portal.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Viene illustrata la finestra di dialogo Aggiorna registro contenitori.":::

**Password per il Registro** di sistema del contenitore di Azure: fornire la password per il nome utente di amministratore associato al Registro di sistema del contenitore di Azure e con l'immagine del contenitore. Il nome utente e la password sono necessari per garantire che l'azienda abbia accesso al registro. È possibile ottenere la password dal portale di Azure accedendo a**Chiavi** di accesso al **Registro di** > sistema del contenitore o con l'interfaccia della riga di comando di Azure usando il [comando show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Viene illustrato il menu Tasto di scelta.":::

**Nome del repository all'interno del Registro**di sistema del contenitore di Azure . Specificare il nome del repository del Registro di sistema del contenitore di Azure con l'immagine. Includere il nome del repository quando si esegue il push dell'immagine nel Registro di sistema. È possibile trovare il nome del repository accedendo alla pagina Repository [del Registro](https://azure.microsoft.com/services/container-registry/) > **contenitori.** Per altre informazioni, vedere Visualizzare i repository del Registro di sistema del contenitore nel portale di Azure.For more information, see [View container registry repositories in Azure portal.](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)

> [!NOTE]
> Una volta impostato, il nome non può essere modificato. Utilizza un nome univoco per ogni offerta nel tuo account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tag immagine per le nuove versioni dell'offerta

I clienti devono essere in grado di ottenere automaticamente gli aggiornamenti da Azure Marketplace quando si pubblica un aggiornamento. Se non vogliono aggiornare, devono essere in grado di rimanere su una versione specifica dell'immagine. È possibile farlo aggiungendo nuovi tag immagine ogni volta che si effettua un aggiornamento all'immagine.

### <a name="image-tag"></a>Tag dell'immagine

Questo campo deve includere un tag **più recente** che punta alla versione più recente dell'immagine su tutte le piattaforme supportate. Deve inoltre includere un tag di versione (ad esempio, a partire da xx.xx.xx, dove xx è un numero). I clienti devono usare [i tag manifesto](https://github.com/estesp/manifest-tool) per scegliere come target più piattaforme. È necessario aggiungere anche tutti i tag referenziati da un tag di manifesto per poterli caricare.

Tutti i tag del manifesto (ad eccezione **-** del tag più recente) devono iniziare con X.Y o X.Y. Ad esempio, se un tag **più recente** punta a 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, questi sei tag devono essere aggiunti a questo campo. Per informazioni [dettagliate, vedere Preparare gli asset tecnici del contenitore di Azure.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

> [!NOTE]
> Ricordarsi di aggiungere un tag di test all'immagine, in modo da poterla identificare durante i test.

## <a name="review-and-publish"></a>Revisione e pubblicazione

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, puoi inviarla per la revisione e la pubblicazione.

Nell'angolo superiore destro del portale selezionare **Revisione e** **pubblicazione**.

Nella pagina di revisione è possibile:

- Vedi lo stato di completamento per ogni sezione dell'offerta. Non puoi pubblicare finché tutte le sezioni dell'offerta non vengono contrassegnate come complete.
  - **Non avviato:** non è stato avviato e deve essere completato.
  - **Incompleto:** contiene errori che devono essere corretti o richiede di fornire ulteriori informazioni. Per informazioni, vedere le sezioni precedenti in questo documento.
  - **Completa:** include tutti i dati richiesti senza errori. Tutte le sezioni dell'offerta devono essere complete prima di poter inviare l'offerta.
- Fornisci le istruzioni di test al team di certificazione per assicurarti che la tua offerta sia testata correttamente. Inoltre, fornisci eventuali note supplementari utili per comprendere la tua offerta.

Per inviare l'offerta per la pubblicazione, selezionare **Pubblica**.

Ti invieremo un'email per comunicarti quando è disponibile una versione di anteprima dell'offerta per la revisione e l'approvazione.

Per pubblicare l'offerta al pubblico (o se un'offerta privata, a un pubblico privato), vai al Centro per i partner e seleziona **Go-live**.

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
