---
title: Creare un'offerta di macchine virtuali di Azure - Azure MarketplaceCreate an Azure virtual machine offer - Azure Marketplace
description: Informazioni su come creare un'offerta di macchine virtuali nel mercato commerciale.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d598f741c5add58a89aa2b7aa01802a7e35f9a19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869068"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Creare un'offerta di macchina virtuale di AzureCreate an Azure virtual machine offer

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte di vm di Azure dal portale Cloud Partner al Centro per i partner. Fino alla migrazione delle offerte, continuare a seguire le istruzioni in [Creare l'offerta di macchine virtuali](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) nel portale Cloud Partner per gestire le offerte.

Questo articolo descrive come creare e pubblicare un'offerta di macchine virtuali di Azure in [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Si occupa di macchine virtuali basate su Windows e Linux che contengono un sistema operativo, un disco rigido virtuale (VHD) e fino a 16 dischi dati.

## <a name="introduction"></a>Introduzione

### <a name="publishing-benefits"></a>Vantaggi della pubblicazione

La pubblicazione in Azure Marketplace offre i vantaggi seguenti:Publishing to Azure Marketplace has the following benefits:

- Promuovi la tua azienda utilizzando il marchio Microsoft
- Raggiungi oltre 100 milioni di utenti di Office 365 e Dynamics 365 e più di 200.000 organizzazioni tramite Azure Marketplace
- Ottieni lead di alta qualità da questi mercati
- Ottieni i tuoi servizi promossi dai team di settore e televendita Microsoft

### <a name="before-you-begin"></a>Prima di iniziare

Se non è ancora stato fatto, vedere la guida alla pubblicazione dell'offerta di macchine virtuali e il materiale della macchina virtuale di Azure:If you haven't done so yet, review the [Virtual machine offer publishing guide](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) and this Azure virtual machine material:

- Guide introduttive
  - [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/)
  - [Modelli di avvio rapido di Azure in GitHub](https://github.com/azure/azure-quickstart-templates)
- Esercitazioni
  - [Macchine virtuali di Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Macchine virtuali di Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Esempi
  - [Azure CLI Samples for Linux VMs](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell for Linux VMs](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Azure CLI Samples for Windows VMs](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell for Windows VMs](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Fondamenti nella conoscenza tecnica

La progettazione, la creazione e il test di questi asset richiedono tempo e richiedono conoscenze tecniche sia della piattaforma Azure che delle tecnologie usate per creare l'offerta.

Il team di progettazione deve comprendere le seguenti tecnologie Microsoft:

- Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
- Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
- Conoscenza delle macchine virtuali di [Azure,](https://azure.microsoft.com/services/virtual-machines/)Archiviazione di Azure e [rete di AzureWorking](https://azure.microsoft.com/services/?filter=networking#networking) knowledge of Azure Virtual Machines , [Azure Storage,](https://azure.microsoft.com/services/?filter=storage#storage)and Azure Networking

## <a name="create-an-azure-virtual-machine-offer"></a>Creare un'offerta di macchina virtuale di AzureCreate an Azure virtual machine offer

Prima di poter creare un'offerta di macchine virtuali di Azure, è necessario disporre di un account di mercato commerciale nel Centro per i partner. Se non ne hai ancora creato uno, vedi [Creare un account marketplace commerciale nel Centro per i partner.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

1. Accedere al [Centro per](https://partner.microsoft.com/dashboard/home)i partner , quindi nel menu in alto selezionare **Dashboard**.
2. Nella barra di spostamento sinistra selezionare **Mercato commerciale**, quindi **Panoramica**.
3. Nella pagina **Panoramica** selezionare **Nuova offerta**, quindi **Macchina virtuale di Azure**. Viene visualizzata la finestra di dialogo **Nuova offerta.**

    ![Illustra la pagina Panoramica nel Centro per i partner con il pulsante Nuova offerta e l'offerta Macchina virtuale di Azure selezionata.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>ID e alias dell'offerta

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nel tuo account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e in Azure PowerShell e l'interfaccia della riga di comando di Azure, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi, ed è limitato a 50 caratteri. Ad esempio, se si immette **test-offer-1** in `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`questo caso, l'indirizzo Web dell'offerta sarà .
- L'ID offerta non può essere modificato dopo aver selezionato **Crea**.

Immettere un **alias di offerta**. Questo è il nome utilizzato per l'offerta nel Centro per i partner. Questo nome non viene utilizzato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="test-drive"></a>Test drive

Imposta una dimostrazione (test drive) che consenta ai clienti di provare la tua offerta prima di acquistarla. Per creare un ambiente dimostrativo che consenta ai clienti di provare la tua offerta per un determinato periodo di tempo, consulta [Test Drive la tua offerta nel mercato commerciale.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Per abilitare un'unità di test, selezionare la casella di controllo **Abilita un'unità** di test. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Risorse aggiuntive per il test drive:

- [Procedure consigliate tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Procedure consigliate di marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Panoramica dei test Drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (assicurarsi che il blocco popup sia disattivato).

### <a name="lead-management"></a>Lead management (Gestione di clienti potenziali)

Quando pubblichi l'offerta nel marketplace commerciale con il Centro per i partner, connettila al sistema CRM (Customer Relationship Management). Ciò consente di ricevere le informazioni di contatto del cliente non appena qualcuno esprime interesse o utilizza il prodotto. La connessione a un CRM è necessaria se si abilita **Test Drive** (vedere la sezione precedente), altrimenti è facoltativo.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il Centro per i partner supporta i seguenti sistemi CRM:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per il coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è elencato in precedenza, usare [Tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o Endpoint [https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei lead dei clienti. Esportare quindi i dati nel sistema CRM.

2. Collegare l'offerta alla destinazione lead durante la pubblicazione nel Centro per i partner.
3. Verificare che la connessione alla destinazione del lead sia configurata correttamente. Dopo averla pubblicata nel Centro per i partner, convalideremo la connessione e ti invieremo un responsabile di test. Durante l'anteprima dell'offerta prima che venga messa in diretta, è anche possibile testare la connessione lead provando a distribuire l'offerta manualmente nell'ambiente di anteprima.
4. Assicurati che la connessione alla destinazione del lead rimanga aggiornata in modo da non perdere alcun lead.

Selezionare **Salva bozza** prima di continuare.

## <a name="properties"></a>Proprietà

Questa pagina ti consente di definire le categorie e i settori utilizzati per raggruppare la tua offerta sul marketplace, la versione dell'app e i contratti legali che supportano la tua offerta.

### <a name="categories"></a>Categorie

Selezionare un minimo di una e un massimo di cinque categorie. Queste categorie vengono utilizzate per inserire l'offerta nelle aree di ricerca del marketplace appropriate. Nella descrizione dell'offerta, spiega come la tua offerta supporta queste categorie. Le offerte di macchine virtuali vengono visualizzate nella categoria **Calcolo** in Azure Marketplace.Virtual machine offers appear under the Compute category in Azure Marketplace.

### <a name="legal"></a>Note legali

È necessario fornire i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- Usa i tuoi termini e condizioni
- Utilizzare il contratto standard per il marketplace commerciale Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Usa i tuoi termini e condizioni

Per fornire termini e condizioni personalizzati, inserisci fino a 10.000 caratteri di testo nella casella **Termini e condizioni.** Se hai bisogno di una descrizione più lunga, inserisci un singolo indirizzo web che punti al punto in cui sono disponibili i termini e le condizioni. Verrà visualizzato ai clienti come collegamento attivo.

I clienti devono accettare questi termini prima di poter provare la tua offerta.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Utilizzare il contratto standard per il marketplace commerciale Microsoft

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità legale per i fornitori di software, Microsoft offre un contratto standard per il mercato commerciale. Quando offri il tuo software ai sensi del Contratto Standard, i clienti devono solo leggerlo e accettarlo una volta e non devi creare termini e condizioni personalizzati.

Utilizzare il contratto standard selezionando la casella di controllo **Usa contratto standard per** il mercato commerciale di Microsoft e quindi **Accetta** nella finestra popup (potrebbe essere necessario scorrere verso l'alto per visualizzarlo).

![Illustra la pagina Panoramica nel Centro per i partner con il pulsante Nuova offerta e l'offerta di servizi di consulenza selezionata.](media/use-standard-contract.png)

> [!NOTE]
> Dopo aver pubblicato un'offerta utilizzando il contratto standard per il mercato commerciale, non è possibile utilizzare i termini e le condizioni personalizzati. Offri la tua soluzione in base al Contratto Standard **o** ai tuoi termini e condizioni.

Per ulteriori informazioni sul contratto standard, vedere Contratto standard per il [marketplace commerciale](https://docs.microsoft.com/azure/marketplace/standard-contract)Microsoft . È possibile scaricare il [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) in formato PDF (assicurarsi che il blocco popup sia disattivato).

##### <a name="standard-contract-amendments"></a>Modifiche del contratto standard

Le modifiche del contratto standard consentono di selezionare i termini del contratto standard per semplicità e di creare i termini per il prodotto o l'azienda. I clienti devono rivedere le modifiche apportate al contratto solo se hanno già esaminato e accettato il contratto standard Microsoft. Sono disponibili due tipi di emendamenti: universale e personalizzato.

**Emendamenti universali** – Questi vengono applicati universalmente al Contratto Standard per tutti i clienti. Vengono mostrati a tutti i clienti dell'offerta nel flusso di acquisto. I clienti devono accettare i termini del Contratto Standard e gli emendamenti prima di poter utilizzare l'offerta. È possibile fornire un unico emendamento universale per ogni offerta. È possibile immettere un numero illimitato di caratteri in questa casella. Questi termini vengono visualizzati ai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

**Modifiche personalizzate:** si tratta di modifiche speciali al contratto standard destinate a clienti specifici tramite ID tenant di Azure.Custom Amendments – These are special amendments to the Standard Contract that are targeted to specific customers through Azure tenant IDs. È possibile scegliere il tenant di destinazione. Solo ai clienti del tenant verranno presentati i termini di modifica personalizzati nel flusso di acquisto dell'offerta. I clienti devono accettare i termini del Contratto Standard e gli emendamenti prima di poter utilizzare l'offerta.

Per iniziare, **selezionare Aggiungi termini di modifica personalizzati (massimo 10)**. Puoi fornire fino a dieci termini di modifica personalizzati per offerta.

- **Termini di modifica personalizzati:** immettere i termini di modifica personalizzati nella casella Termini di modifica personalizzati. È possibile immettere un numero illimitato di caratteri. Solo i clienti degli ID tenant specificati per questi termini personalizzati li vedranno nel flusso di acquisto dell'offerta nel portale di Azure.Only customers from the tenant IDs you specify for these custom terms will see these in the offer's purchase flow in the Azure portal.
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una modifica personalizzata, è necessario fornire almeno un ID tenant, che identifica il cliente in Azure.If you add a custom amendment, you must provide at least one tenant ID, which identifies your customer in Azure. il cliente può trovare per voi in Azure sotto, quindi proprietà. Il valore dell'ID directory è l'ID tenant (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e). È inoltre possibile trovare l'ID tenant dell'organizzazione del cliente usando il nome di dominio Web indirizzo in [Che cos'è l'ID tenant di Microsoft Azure e Office 365?](https://www.whatismytenantid.com/).
- **Descrizione** (facoltativo): fornire una descrizione descrittiva dell'ID tenant che consente di identificare il cliente di destinazione con la modifica.

> [!NOTE]
> Questi due tipi di emendamenti si sovrappongono l'uno sull'altro. I clienti mirati con modifiche personalizzate otterranno anche la modifica universale al contratto standard durante l'acquisto.

Selezionare **Salva bozza** prima di continuare.

## <a name="offer-listing"></a>Inserzione dell'offerta

Questa pagina consente di definire i dettagli dell'offerta, ad esempio il nome, la descrizione, i collegamenti e i contatti dell'offerta.

> [!NOTE]
> Il contenuto dell'elenco di offerte (ad esempio la descrizione, i documenti, le schermate e le condizioni per l'utilizzo) non è necessario per essere in inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non in lingua inglese]". Puoi anche fornire un _indirizzo web Link utile_ per offrire contenuti in una lingua diversa da quella utilizzata nel contenuto della scheda Offerta.

### <a name="marketplace-details"></a>Dettagli del Marketplace

#### <a name="name"></a>Nome

Il nome che inserisci qui viene mostrato ai clienti come titolo della tua offerta. Questo campo è precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Il nome:

- Può essere registrato (e puoi includere simboli di marchio e copyright)
- Non può essere lungo più di 50 caratteri
- Non è possibile includere emoji.

#### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Breve descrizione dell'offerta. Può essere lungo fino a 100 caratteri e viene utilizzato nei risultati di ricerca del marketplace.

#### <a name="long-summary"></a>Riassunto lungo

Fornisci una descrizione più lunga della tua offerta. Può essere lungo fino a 256 caratteri e viene utilizzato nei risultati di ricerca del marketplace.

#### <a name="description"></a>Descrizione

Fornisci una descrizione dettagliata della tua offerta, fino a 3.000 caratteri. Questo viene visualizzato ai clienti nella panoramica dell'elenco del mercato commerciale.

Includere una o più delle seguenti opzioni nella descrizione:

- Il valore e i vantaggi principali della tua offerta
- Associazioni di categoria o di settore, o entrambe
- Opportunità di acquisto in-app
- Eventuali divulgazioni obbligatorie

Ecco alcuni suggerimenti per scrivere la tua descrizione:

- Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includere i seguenti elementi:
  - Descrizione dell'offerta.
  - Il tipo di utente che beneficia della tua offerta.
  - Esigenze o problemi del cliente ritommiti dall'offerta.
- Ricorda che le prime frasi potrebbero essere visualizzate nei risultati dei motori di ricerca.
- Non fare affidamento su caratteristiche e funzionalità per vendere la tua offerta. Concentrati invece sul valore offerto dalla tua offerta.
- Utilizzare parole specifiche del settore o basate sui vantaggi.

Per rendere più coinvolgente la descrizione dell'offerta, usa l'editor RTF per formattare la descrizione. L'editor RTF consente di aggiungere numeri, punti elenco, grassetto, corsivo e rientri per rendere la descrizione più leggibile.

![Illustra la pagina Panoramica nel Centro per i partner con il pulsante Nuova offerta e l'offerta di servizi di consulenza selezionata.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Collegamento all'informativa sulla privacy

Immettere l'indirizzo Web (URL) dell'informativa sulla privacy dell'organizzazione. Assicurati che la tua offerta sia conforme alle leggi e ai regolamenti sulla privacy. Devi anche pubblicare una politica sulla privacy valida sul tuo sito web.

### <a name="useful-links"></a>Collegamenti utili

Fornisci documenti online supplementari sulla tua offerta. Per aggiungere un collegamento, selezionare **Aggiungi un collegamento** e quindi completare i seguenti campi:

- **Nome:** i clienti vedranno il nome nella pagina dei dettagli.
- **Collegamento (URL):** immettere un collegamento per la visualizzazione del documento online da parte dei clienti.

### <a name="customer-support-links"></a>Collegamenti all'assistenza clienti

Fornisci il sito web di supporto dove i clienti possono contattare il tuo team di supporto.

- Sito Web di supporto globale di AzureAzure Global support website
- Sito Web di supporto per Azure per enti pubblici Azure per enti pubbliciAzure

### <a name="partner-support-contact"></a>Contatto per l'assistenza clienti

Fornire informazioni di contatto per i partner Microsoft da utilizzare quando i clienti aprono un ticket di supporto. Questo non sarà elencato nel marketplace.

- Nome
- Posta elettronica
- Telefono

### <a name="engineering-contact"></a>Contatto tecnico

Fornire informazioni di contatto per Microsoft da utilizzare in caso di problemi con l'offerta, inclusi problemi di certificazione. Questo non sarà elencato nel marketplace.

- Nome
- Posta elettronica
- Telefono

### <a name="marketplace-media"></a>Media del Marketplace

Fornisci loghi e immagini da utilizzare con la tua offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate causeranno il rifiuto dell'invio.

>[!Note]
>Se si verifica un problema durante il caricamento dei https://upload.xboxlive.com file, assicurarsi che la rete locale non blocchi il servizio utilizzato dal Centro per i partner.

#### <a name="marketplace-logos"></a>Loghi del Marketplace

Fornisci i file PNG del logo della tua offerta nelle seguenti quattro dimensioni in pixel:

- **Piccolo** (48 x 48)
- **Medio** (90 x 90)
- **Grande** (216 x 216)
- **Ampio** (255 x 115)

Tutti e quattro i loghi sono obbligatori e vengono utilizzati in luoghi diversi nella presentazione del marketplace.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungi fino a cinque screenshot che mostrano come funziona la tua offerta. Ogni screenshot deve avere dimensioni 1280 x 720 pixel e in formato PNG. È inoltre necessario aggiungere una didascalia per descrivere lo screenshot.

#### <a name="videos"></a>Video

Aggiungi fino a cinque video che dimostrano la tua offerta. Questi devono essere ospitati su un servizio video esterno. Inserisci il nome, l'indirizzo Web e un'immagine PNG in miniatura del video con dimensioni di 1280 x 720 pixel.

Per altre risorse per la presentazione del marketplace, vedere Procedure consigliate per gli elenchi di offerte di [marketplace.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Selezionare **Salva bozza** prima di continuare.

## <a name="preview"></a>Anteprima

Nella scheda Anteprima, scegli un pubblico di **anteprima** limitato per convalidare la tua offerta prima di pubblicarla dal vivo per il pubblico del marketplace più ampio.

> [!IMPORTANT]
> Dopo aver controllato l'offerta in Anteprima, seleziona **Vai in diretta** per pubblicare l'offerta sul pubblico del marketplace commerciale.

Il gruppo di destinatari di anteprima è identificato dai GUID dell'ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi può essere visto dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina Sottoscrizioni nel portale di Azure.You can find your Azure subscription ID on the **Subscriptions** page in Azure portal.

Aggiungere almeno un ID sottoscrizione di Azure singolarmente (fino a 10) o caricando un file CSV (fino a 100). Aggiungendo questi ID sottoscrizione, è possibile definire chi può visualizzare in anteprima l'offerta prima che venga pubblicata dal vivo. Se la tua offerta è già attiva, puoi comunque definire un pubblico di anteprima per testare le modifiche o gli aggiornamenti dell'offerta.

> [!NOTE]
> Un pubblico di anteprima è diverso da un pubblico privato. Un pubblico di anteprima può accedere alla tua offerta _prima_ che venga pubblicata dal vivo nei marketplace. Possono vedere e convalidare tutti i piani, compresi quelli che saranno disponibili solo per un pubblico privato dopo che la tua offerta è stata completamente pubblicata sul mercato. Un gruppo di destinatari privato (definito nella scheda **Prezzi e disponibilità** del piano) ha accesso esclusivo a un piano specifico.

Selezionare **Salva bozza** prima di passare alla sezione successiva, Panoramica del piano.

## <a name="plan-overview"></a>Panoramica del piano

Puoi fornire diverse opzioni di piano all'interno della stessa offerta nel Centro per i partner. Questi piani sono stati precedentemente indicati come SKU. Un'offerta richiede almeno un piano, che può differire in termini di pubblico di monetizzazione, aree di Azure, funzionalità o immagini VM.

Dopo aver creato i piani, la scheda **Panoramica piano** mostra:

- Nomi dei piani
- Modelli di licenza
- Pubblico (pubblico o privato)
- Stato pubblicazione corrente
- Azioni disponibili

Le azioni disponibili nella panoramica del piano variano a seconda dello stato corrente del piano. Tali impostazioni includono:

- **Elimina bozza:** se lo stato del piano è una bozza
- **Interrompere la vendita del piano** o Sincronizzare il pubblico **privato:** se lo stato del piano viene pubblicato in tempo reale

### <a name="create-new-plan"></a>Crea nuovo piano

Seleziona **: crea un nuovo piano** nella parte superiore. Viene visualizzata la finestra di dialogo **Nuovo piano.**

Nella casella **ID piano** creare un ID piano univoco per ogni piano dell'offerta. Questo ID sarà visibile ai clienti nell'indirizzo Web del prodotto. Utilizzare solo lettere minuscole e numeri, trattini o caratteri di sottolineatura e un massimo di 50 caratteri.

> [!NOTE]
> L'ID piano non può essere modificato dopo aver selezionato **Crea**.

Nella casella **Nome piano** immettere un nome per il piano. I clienti vedono questo nome quando decidono quale piano selezionare all'interno dell'offerta. Creare un nome univoco che indichi chiaramente le differenze di ogni piano. Ad esempio, è possibile utilizzare **Windows Server** con piani **Pay-as-you-go**, **BYOL**, **Advanced**ed **Enterprise**.

Selezionare **Create** (Crea).

### <a name="plan-setup"></a>Pianificare l'installazione

Impostare la configurazione di alto livello per il tipo di piano, se riutilizza la configurazione tecnica di un altro piano e in quali aree di Azure il piano deve essere disponibile. Le selezioni effettuate in questo campo determinano quali campi vengono visualizzati in altre schede per la stessa pianta.

#### <a name="reuse-technical-configuration"></a>Riutilizzare la configurazione tecnica

Se si dispone di più piani dello stesso tipo e i pacchetti sono identici tra di essi, è possibile selezionare **questo piano riutilizza**la configurazione tecnica da un altro piano . Questa opzione consente di selezionare uno degli altri piani dello stesso tipo per questa offerta e di riutilizzarne la configurazione tecnica.

> [!NOTE]
> Quando si riutilizza la configurazione tecnica da un altro piano, l'intera scheda **Configurazione tecnica** scompare da questo piano. I dettagli di configurazione tecnica dell'altro piano, inclusi gli aggiornamenti eseguiti in futuro, verranno utilizzati anche per questo piano. Questa impostazione non può essere modificata dopo la pubblicazione di questo piano.

#### <a name="azure-regions"></a>Aree di Azure

Il piano deve essere reso disponibile in almeno un'area di Azure.Your plan must be made available in at least one Azure region.

Selezionare l'opzione **Azure globale** per rendere il piano disponibile per i clienti in tutte le aree pubbliche di Azure con integrazione del marketplace commerciale. Per informazioni dettagliate, vedere [Disponibilità geografica e supporto valutario](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selezionare l'opzione **Azure per enti pubblici** per rendere disponibile il piano nell'area di Azure per enti [pubblici.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Questa area fornisce accesso controllato ai clienti provenienti da enti federali, statali, locali o tribali statunitensi, nonché ai partner idonei a servirli. L'utente, in qualità di editore, è responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate. Azure per enti pubblici usa data center e reti fisicamente isolati (solo negli Stati Uniti).

Prima della pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), è possibile testare e convalidare il piano nell'ambiente in quanto determinati endpoint potrebbero essere diversi. Per configurare e testare il piano, richiedere un account di valutazione dalla versione di valutazione di [Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo che il piano è stato pubblicato e disponibile in un'area di Azure specifica, non è possibile rimuoverla.

#### <a name="azure-government-certifications"></a>Certificazioni di Azure per enti pubbliciAzure Government certifications

Questa opzione è visibile solo se si seleziona **Azure per enti pubblici** in **Aree**di Azure .

I servizi di Azure per enti pubblici gestiscono i dati soggetti a determinate normative e requisiti governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per dare consapevolezza alle certificazioni per questi programmi, è possibile fornire fino a 100 link che li descrivono. Questi possono essere link al tuo annuncio sul programma direttamente o link a descrizioni della tua conformità con loro sui tuoi siti web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

Selezionare **Salva bozza** prima di continuare.

### <a name="plan-listing"></a>Pianificare l'inserzione

Qui è possibile configurare i dettagli dell'elenco del piano. In questa scheda vengono visualizzate informazioni specifiche che possono differire tra i piani nella stessa offerta.

#### <a name="plan-name"></a>Nome piano

Questo è precompilato con il nome che hai dato al tuo piano quando l'hai creato. Questo nome viene visualizzato nel marketplace come titolo di questo piano ed è limitato a 100 caratteri.

#### <a name="plan-summary"></a>Riepilogo del piano

Fornisci un breve riepilogo del tuo piano (non l'offerta). Questo riepilogo è limitato a 100 caratteri.

#### <a name="plan-description"></a>Descrizione del piano

Descrivi cosa rende unico questo piano software, nonché le differenze tra i piani all'interno della tua offerta. Non descrivere l'offerta, solo il piano. La descrizione del piano può contenere fino a 2.000 caratteri.

Selezionare **Salva bozza** prima di continuare.

### <a name="pricing-and-availability"></a>Prezzi e disponibilità

In questa scheda verranno configurati gli elementi seguenti:

- Mercati questo piano sarà disponibile in
- Il prezzo orario
- Se rendere il piano visibile a tutti o solo a clienti specifici (un pubblico privato)

#### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per ogni località di mercato in cui il piano deve essere disponibile per l'acquisto (gli utenti di questi mercati possono comunque distribuire l'offerta a tutte le aree di Azure selezionate in **[Configurazione piano).](#plan-setup)** Il pulsante **Imposta rimessa** mostra i paesi in cui Microsoft rimette l'imposta sulle vendite e sull'utilizzo per conto dell'utente. La pubblicazione in Cina è limitata ai piani che sono **Gratuiti** o **Bring your own license** (BYOL).

Se hai già impostato i prezzi per il tuo piano in Dollari USA (USD) e aggiungi un'altra posizione di mercato, il prezzo per il nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Controlla sempre il prezzo per ogni mercato prima della pubblicazione. Esaminare i prezzi utilizzando il collegamento **Prezzi di esportazione (xlsx)** dopo aver salvato le modifiche.

Quando si rimuove un mercato, i clienti di tale mercato utilizzando distribuzioni attive non saranno in grado di creare nuove distribuzioni o aumentare le distribuzioni esistenti. Le distribuzioni esistenti non saranno interessate.

#### <a name="pricing"></a>Prezzi

**Modello di licenza:** selezionare un **piano fatturato mensile basato sull'utilizzo** per configurare i prezzi per questo piano o **Portare la propria licenza** per consentire ai clienti di utilizzare questo piano con la licenza esistente.

Per un piano con fatturazione mensile basato sull'utilizzo, utilizzare una delle tre opzioni di immissione dei prezzi seguenti:

- **Per core** – Fornire il prezzo per core in dollari degli Stati Uniti (USD). Calcoleremo il prezzo per dimensione principale e convertiremo in valute locali utilizzando il tasso di cambio corrente.
- **Per dimensione del nucleo:** fornire i prezzi per dimensione di base in USD. Convertiremo i prezzi in valute locali utilizzando il tasso di cambio corrente.
- **Per mercato e dimensione del nucleo:** fornire i prezzi per ogni dimensione del core per tutti i mercati. È possibile importare i prezzi da un foglio di calcolo.

> [!NOTE]
> Salvare le modifiche dei prezzi per consentire l'esportazione dei dati sui prezzi. Dopo la pubblicazione, un prezzo per un mercato nel tuo piano, non può essere modificato in un secondo momento. Assicurati che questi prezzi siano giusti prima della pubblicazione esportando il foglio di calcolo dei prezzi e rivedendo il prezzo in ogni mercato.

#### <a name="free-trial"></a>Versione di prova gratuita

Puoi offrire una prova gratuita di un mese o tre mesi ai tuoi clienti.

#### <a name="visibility"></a>Visibilità

È possibile progettare ogni piano in modo che sia visibile a tutti o solo a un pubblico preselezionato. Assegnare appartenenze a questo gruppo di destinatari con restrizioni usando gli ID sottoscrizione di Azure.Assign memberships s this restricted audience using Azure subscription IDs.

**Pubblico** – Il tuo piano può essere visto da tutti.

**Pubblico privato:** consente di rendere il piano visibile solo a un pubblico preselezionato. Dopo la pubblicazione come piano privato, puoi aggiornare il pubblico o modificarlo in pubblico. Dopo aver resi pubblico un piano, esso deve rimanere pubblico; non è possibile modificarlo nuovamente in privato.

Gruppo di destinatari con restrizioni (ID sottoscrizione di Azure): assegnare il gruppo di destinatari che avrà accesso a questo piano privato usando gli ID sottoscrizione di **Azure.Restricted Audience (Azure subscription IDs)** – Assign the audience that will have access to this private plan using Azure subscription IDs. Facoltativamente, includere una descrizione di ogni ID sottoscrizione di Azure assegnato. Aggiungi fino a 10 ID sottoscrizione manualmente o 20.000 se importa un foglio di calcolo CSV. Gli ID sottoscrizione di Azure sono rappresentati come GUID e le lettere devono essere minuscole.

> [!NOTE]
> Un pubblico privato o con restrizioni è diverso dal pubblico di anteprima definito nella scheda **Anteprima.** Un pubblico di anteprima può accedere alla tua offerta _prima_ della pubblicazione dal vivo nel marketplace. Mentre la scelta del pubblico privato si applica solo a un piano specifico, il pubblico di anteprima può visualizzare tutti i piani (privati o meno) ai fini della convalida.

#### <a name="hide-plan"></a>Nascondi piano

Se la macchina virtuale deve essere usata solo indirettamente quando viene fatto riferimento tramite un altro modello di soluzione o un'altra applicazione gestita, selezionare questa casella per pubblicare la macchina virtuale ma nasconderla ai clienti per cercarla e cercarla direttamente.

> [!NOTE]
> I piani nascosti non supportano i collegamenti di anteprima.

Selezionare **Salva bozza** prima di continuare.

### <a name="technical-configuration"></a>Configurazione tecnica

Fornire le immagini e altre proprietà tecniche associate a questo piano. Per informazioni dettagliate, vedere [Creare un asset tecnico di Azure VM.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

> [!NOTE]
> Questa scheda non viene visualizzata se questo piano è stato configurato per riutilizzare i pacchetti di un altro piano nella scheda **Impostazione piano.**

#### <a name="operating-system"></a>Sistema operativo

- **Famiglia di sistemi operativi** - Selezionare dal sistema operativo **Windows** o **Linux**
- **Release** o **Vendor:** scegliere la versione di Windows o il fornitore Linux
- **Nome descrittivo del** sistema operativo: scegliere un nome descrittivo del sistema operativo. Questo nome è visibile ai clienti

#### <a name="recommended-vm-sizes"></a>Recommended VM Sizes (Dimensioni VM consigliate)

Selezionare fino a sei dimensioni di macchine virtuali consigliate da visualizzare in Azure Marketplace.Select up to six recommended virtual machine sizes to display in Azure Marketplace.

#### <a name="open-ports"></a>Aprire le porte

Aprire porte pubbliche o private in una macchina virtuale distribuita.

#### <a name="storage-option-for-deployment"></a>Opzione di archiviazione per la distribuzioneStorage option for deployment

Opzione di distribuzione del **disco:** selezionare il tipo di distribuzione su disco che gli utenti possono usare quando si usa la macchina virtuale. Microsoft consiglia di limitare la distribuzione solo alla distribuzione del disco gestito.

#### <a name="properties"></a>Proprietà

**Supporto della rete accelerata:** selezionare questa opzione se la macchina virtuale supporta la [rete accelerata.](https://go.microsoft.com/fwlink/?linkid=2124513)

#### <a name="vm-images"></a>VM Images (Immagini di VM)

Specificare una versione su disco e l'URI di accesso condiviso per le immagini della macchina virtuale. Aggiungere fino a 16 dischi dati per ogni immagine della macchina virtuale. Fornisci solo una nuova versione dell'immagine per piano in un determinato invio. Dopo la pubblicazione di un'immagine, non è possibile modificarla, ma è possibile eliminarla. L'eliminazione di una versione impedirà agli utenti nuovi ed esistenti di distribuire una nuova istanza della versione eliminata.

- **La versione disco** è la versione dell'immagine che stai fornendo.
- **L'URI di** accesso condiviso è il percorso in Archiviazione di Azure in cui è stato archiviato il disco rigido virtuale del sistema operativo.
- Le immagini del disco dati sono anche URI di accesso sas VHD archiviati nell'archiviazione di Azure.Data disk images are also VHD SAS URIs stored in their Azure storage.
- Aggiungere una sola immagine per invio in un piano.

Indipendentemente dal sistema operativo utilizzato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. I clienti non possono rimuovere dischi che fanno parte di un'immagine al momento della distribuzione, ma possono sempre aggiungere dischi durante o dopo la distribuzione.

Selezionare **Salva bozza** prima di continuare e tornare a **Panoramica piano**.

## <a name="resell-through-csps"></a>Rivendere tramite CSP

Espandi la copertura della tua offerta rendendola disponibile ai partner del programma [Cloud Solution Provider](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Tutti i piani Bring Your Own License (BYOL) sono automaticamente opt-in; puoi scegliere di scegliere i tuoi piani non BYOL.

Selezionare **Salva bozza** prima di continuare.

## <a name="test-drive"></a>Test drive

Imposta una dimostrazione (test drive) che consenta ai clienti di provare la tua offerta prima di acquistarla. Per creare un ambiente dimostrativo che consenta ai clienti di provare la tua offerta per un determinato periodo di tempo, consulta [Test Drive la tua offerta nel mercato commerciale.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Per abilitare un'unità di test, selezionare la casella di controllo Abilita un'unità di test nella scheda [Impostazione offerta.](#test-drive) Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Risorse aggiuntive per il test drive:

- Procedure consigliate tecniche
- Procedure consigliate di marketing
- Panoramica di Test Drives (PDF; assicurati che il blocco popup sia disattivato).

Selezionare **Salva bozza** prima di continuare.

## <a name="review-and-publish"></a>Revisione e pubblicazione

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, puoi inviarla per la revisione e la pubblicazione.

Nell'angolo superiore destro del portale selezionare **Revisione e pubblicazione**.

In questa pagina è possibile:

- Vedi lo stato di completamento per ogni sezione dell'offerta.
  - **Non avviato:** la sezione non è stata avviata e deve essere completata.
  - **Incompleto:** nella sezione sono riportati gli errori che devono essere corretti o è necessario fornire ulteriori informazioni. Vedere le sezioni più indietro in questo documento per istruzioni su come aggiornare questa sezione.
  - **Completo:** la sezione è completa e non sono presenti errori. Tutte le sezioni dell'offerta devono essere complete prima di poter inviare l'offerta.
- Note per la **certificazione:** fornisci le istruzioni di test al team di certificazione per assicurarti che l'app venga testata correttamente. Fornisci eventuali note aggiuntive utili per comprendere la tua app.

Per inviare l'offerta per la pubblicazione, selezionare **Rivedi e pubblica**.

Ti invieremo un'email per comunicarti quando è disponibile una versione di anteprima dell'offerta per la revisione e l'approvazione. Per pubblicare l'offerta al pubblico (o se un'offerta privata, per un pubblico privato), vai al Centro per i partner, individua la pagina **Panoramica** dell'offerta e seleziona **Go-live**. Lo stato dell'offerta verrà visualizzato nella parte superiore della pagina quando è in corso la pubblicazione.

### <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

La fase di **convalida Manuale** del processo di pubblicazione rappresenta un'ampia revisione dell'offerta e delle risorse tecniche associate. Se questo processo rileva gli errori con la tua offerta, riceverai un rapporto di certificazione che descrive in dettaglio i problemi. Basta apportare le correzioni necessarie e ripubblicare l'offerta.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica dell'offerta** mostra una rappresentazione visiva dei passaggi necessari per pubblicare questa offerta (completata e in corso) e della durata del completamento di ogni passaggio.

Questa pagina include collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è una bozza - [Elimina bozza di offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è in diretta - [Smetti di vendere l'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se non hai completato la firma del publisher - [Annulla pubblicazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Esempi di Marketplace

Questi esempi mostrano come viene visualizzata l'offerta in Azure Marketplace.These examples show how the offer appears in Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Dettagli dell'offerta di Azure MarketplaceAzure Marketplace offer details

![Esempio di schermata dei dettagli dell'offerta di Azure MarketplaceAzure Marketplace offer details screen example](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Risultati della ricerca di Azure MarketplaceAzure Marketplace search results

![Esempio di schermata dei dettagli della ricerca di Azure MarketplaceAzure Marketplace search details screen example](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Dettagli del piano di Azure MarketplaceAzure Marketplace plan details

![Esempio di schermata Dettagli piano di Azure MarketplaceAzure Marketplace plan details screen example](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Dettagli dell'offerta del portale di AzureAzure portal offer details

![Esempio di schermata Dei dettagli dell'offerta del portale di AzureAzure portal offer details screen example](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Risultati della ricerca nel portale di AzureAzure portal search results

![Esempio di schermata dei risultati della ricerca nel portale di AzureAzure portal search results screen example](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Dettagli del piano del portale di AzureAzure portal plan details

![Esempio di schermata Dettagli piano portale di AzureAzure portal plan screen example](media/avm-create6.png)

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel mercato commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
