---
title: Creare un'offerta di macchina virtuale di Azure-Azure Marketplace
description: Informazioni su come creare un'offerta di macchina virtuale nel Marketplace commerciale.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d598f741c5add58a89aa2b7aa01802a7e35f9a19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869068"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Creare un'offerta di macchina virtuale di Azure

> [!IMPORTANT]
> Stiamo comunicando la gestione delle offerte della macchina virtuale di Azure dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, continuare a seguire le istruzioni riportate in [creare un'offerta di macchina virtuale](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) in Portale cloud partner per gestire le offerte.

Questo articolo descrive come creare e pubblicare un'offerta di macchina virtuale di Azure in [Azure Marketplace](https://azuremarketplace.microsoft.com/). Consente di indirizzare le macchine virtuali basate su Windows e Linux che contengono un sistema operativo, un disco rigido virtuale (VHD) e un massimo di 16 dischi dati.

## <a name="introduction"></a>Introduzione

### <a name="publishing-benefits"></a>Vantaggi della pubblicazione

La pubblicazione in Azure Marketplace offre i vantaggi seguenti:

- Promuovere la propria azienda usando il marchio Microsoft
- Raggiungi più di 100 milioni utenti di Office 365 e Dynamics 365 e più di 200.000 organizzazioni tramite Azure Marketplace
- Ottieni lead di alta qualità da questi Marketplace
- Ottenere i servizi promossi dai team Microsoft Field e Telesales

### <a name="before-you-begin"></a>Prima di iniziare

Se non è ancora stato fatto, vedere la [Guida alla pubblicazione dell'offerta di macchina virtuale](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) e il materiale della macchina virtuale di Azure:

- Guide introduttive
  - [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/)
  - [Modelli di avvio rapido di Azure in GitHub](https://github.com/azure/azure-quickstart-templates)
- Esercitazioni
  - [Macchine virtuali di Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Macchine virtuali di Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Esempi
  - [Esempi dell'interfaccia della riga di comando di Azure per VM Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell per macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Esempi dell'interfaccia della riga di comando di Azure per VM Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell per macchine virtuali Windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Nozioni fondamentali sulla conoscenza tecnica

La progettazione, la compilazione e il test di questi asset richiedono tempo e richiedono conoscenze tecniche sulla piattaforma Azure e sulle tecnologie usate per creare l'offerta.

Il team di progettazione deve comprendere le tecnologie Microsoft seguenti:

- Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
- Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
- Conoscenza del funzionamento di [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage#storage)e rete di [Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Creare un'offerta di macchina virtuale di Azure

Prima di poter creare un'offerta di macchina virtuale di Azure, è necessario disporre di un account Marketplace commerciale nel centro per i partner. Se non ne è ancora stato creato uno, vedere [creare un account Marketplace commerciale nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/home), quindi scegliere **Dashboard**dal menu in alto.
2. Nella barra di navigazione a sinistra selezionare **Marketplace commerciale**, quindi **Panoramica**.
3. Nella pagina **Panoramica** selezionare **+ nuova offerta**, quindi **macchina virtuale di Azure**. Verrà visualizzata la finestra di dialogo **nuova offerta** .

    ![Viene illustrata la pagina panoramica nel centro per i partner con il pulsante nuova offerta e l'offerta di macchina virtuale di Azure selezionata.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>ID offerta e alias

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta Marketplace e in Azure PowerShell e nell'interfaccia della riga di comando di Azure, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1** qui, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un **alias dell'offerta**. Questo è il nome usato per l'offerta nel centro per i partner. Questo nome non viene usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati per i clienti.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Installazione dell'offerta

### <a name="test-drive"></a>Test drive

Configurare una dimostrazione (test drive) che consente ai clienti di provare l'offerta prima di acquistarla. Per creare un ambiente dimostrativo che consente ai clienti di provare l'offerta per un periodo di tempo fisso, vedere [test drive dell'offerta nel Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Per abilitare una test drive, selezionare la casella di controllo **Abilita una test drive** . Per rimuovere test drive dall'offerta, deselezionare questa casella di controllo.

Risorse test drive aggiuntive:

- [Procedure consigliate tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Procedure consigliate di marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Panoramica di test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (assicurarsi che il blocco popup sia disattivato).

### <a name="lead-management"></a>Lead management (Gestione di clienti potenziali)

Quando si pubblica l'offerta nel Marketplace commerciale con il centro per i partner, connetterla al sistema CRM (Customer Relationship Management). In questo modo è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime l'interesse o usa il prodotto. La connessione a un CRM è obbligatoria se si abilita **test drive** (vedere la sezione precedente), in caso contrario è facoltativo.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il centro per i partner supporta i sistemi CRM seguenti:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per il coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è elencato sopra, usare la [tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o l' [endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei clienti. Esportare quindi i dati nel sistema CRM.

2. Connetti l'offerta alla destinazione principale durante la pubblicazione nel centro per i partner.
3. Verificare che la connessione alla destinazione principale sia configurata correttamente. Dopo la pubblicazione nel centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'anteprima dell'offerta prima che venga attivata, è anche possibile testare la connessione del lead provando a distribuire l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione principale rimanga aggiornata in modo da non perdere i lead.

Selezionare **Salva bozza** prima di continuare.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel Marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="categories"></a>Categorie

Selezionare un minimo di uno e un massimo di cinque categorie. Queste categorie vengono usate per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Nella descrizione dell'offerta, spiegare in che modo l'offerta supporta queste categorie. Le offerte della macchina virtuale vengono visualizzate nella categoria **calcolo** di Azure Marketplace.

### <a name="legal"></a>Note legali

È necessario specificare i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- Usare i termini e le condizioni personali
- Usa il contratto standard per Microsoft Commercial Marketplace

#### <a name="use-your-own-terms-and-conditions"></a>Usare i termini e le condizioni personali

Per specificare i termini e le condizioni personalizzati, immettere fino a 10.000 caratteri di testo nella casella **termini e condizioni** . Se è necessaria una descrizione più lunga, immettere un singolo indirizzo Web che punti alla posizione in cui si trovano i termini e le condizioni. Verrà visualizzato come collegamento attivo ai clienti.

I clienti devono accettare questi termini prima di poter provare l'offerta.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Usa il contratto standard per Microsoft Commercial Marketplace

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard per il Marketplace commerciale. Quando si offre il software nel contratto standard, i clienti devono leggere e accettare una sola volta e non è necessario creare termini e condizioni personalizzati.

Usare il contratto standard selezionando la casella di controllo **USA contratto standard per il Marketplace commerciale di Microsoft** e quindi **accetta** nella finestra popup (potrebbe essere necessario scorrere verso l'alto per visualizzarla).

![Viene illustrata la pagina Panoramica del centro per i partner con il pulsante nuova offerta e l'offerta di servizio di consulenza selezionata.](media/use-standard-contract.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il Marketplace commerciale, non è possibile usare i termini e le condizioni personalizzati. È possibile offrire la propria soluzione nel contratto standard **o** in base a termini e condizioni.

Per ulteriori informazioni sul contratto standard, vedere contratto standard per Microsoft [Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract). È possibile scaricare il [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) come PDF (assicurarsi che il blocco popup sia disattivato).

##### <a name="standard-contract-amendments"></a>Modifiche contrattuali standard

Gli emendamenti contrattuali standard consentono di selezionare le condizioni di contratto standard per semplicità e di creare i termini per il prodotto o l'azienda. I clienti devono solo esaminare le modifiche apportate al contratto se hanno già esaminato e accettato il contratto standard Microsoft. Sono disponibili due tipi di modifiche: universale e personalizzato.

**Emendamenti universali** : vengono applicati in modo universale al contratto standard per tutti i clienti. Vengono visualizzate a ogni cliente dell'offerta nel flusso di acquisto. Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche. È possibile specificare una singola modifica universale per ogni offerta. In questa casella è possibile immettere un numero illimitato di caratteri. Questi termini vengono visualizzati per i clienti in AppSource, Azure Marketplace e/o portale di Azure durante l'individuazione e il flusso di acquisto.

**Modifiche personalizzate** : si tratta di modifiche speciali al contratto standard destinate a specifici clienti tramite gli ID tenant di Azure. È possibile scegliere il tenant di destinazione. Solo i clienti del tenant verranno presentati con le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta. Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

Per iniziare, selezionare **Aggiungi condizioni di modifica personalizzate (max 10)**. Puoi fornire fino a dieci condizioni di modifica personalizzate per ogni offerta.

- **Condizioni di modifica personalizzate** : immettere le condizioni di modifica personalizzate nella casella condizioni di modifica personalizzate. È possibile immettere un numero illimitato di caratteri. Solo i clienti degli ID tenant specificati per questi termini personalizzati li visualizzeranno nel flusso di acquisto dell'offerta nel portale di Azure.
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una rettifica personalizzata, è necessario specificare almeno un ID tenant che identifichi il cliente in Azure. il cliente può trovarsi in Azure con, quindi proprietà. Il valore di ID directory è l'ID tenant (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile trovare l'ID tenant dell'organizzazione del cliente usando l'indirizzo Web del nome di dominio in cui [si trova il Microsoft Azure e l'ID tenant di Office 365?](https://www.whatismytenantid.com/).
- **Descrizione** (facoltativa): fornire una descrizione descrittiva dell'ID tenant che consente di identificare il cliente di destinazione della modifica.

> [!NOTE]
> Questi due tipi di modifiche si impilano tra loro. I clienti destinati a modifiche personalizzate riceveranno anche la modifica universale del contratto standard durante l'acquisto.

Selezionare **Salva bozza** prima di continuare.

## <a name="offer-listing"></a>Elenco offerte

Questa pagina consente di definire i dettagli dell'offerta, ad esempio nome dell'offerta, descrizione, collegamenti e contatti.

> [!NOTE]
> Non è necessario che il contenuto dell'offerta (ad esempio la descrizione, i documenti, le schermate e le condizioni per l'utilizzo) sia in inglese, purché la descrizione dell'offerta inizi con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile fornire un _indirizzo Web di collegamento utile_ per offrire contenuti in una lingua diversa da quella usata nell'elenco di contenuti dell'offerta.

### <a name="marketplace-details"></a>Dettagli del Marketplace

#### <a name="name"></a>Name

Il nome immesso qui viene indicato ai clienti come titolo dell'elenco di offerte. Questo campo è già compilato con il testo immesso nella casella **alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Nome:

- Può essere marchiato (ed è possibile includere i marchi e i simboli di copyright)
- Non può essere più lungo di 50 caratteri
- Non può includere emoji.

#### <a name="search-results-summary"></a>Riepilogo risultati ricerca

Breve descrizione dell'offerta. Questo può avere una lunghezza di 100 caratteri e viene usato nei risultati della ricerca nel Marketplace.

#### <a name="long-summary"></a>Riepilogo lungo

Fornire una descrizione più lunga dell'offerta. Questo può avere una lunghezza di 256 caratteri e viene usato nei risultati della ricerca nel Marketplace.

#### <a name="description"></a>Descrizione

Fornire una descrizione dettagliata dell'offerta, fino a 3.000 caratteri. Questa operazione viene visualizzata ai clienti nella panoramica dell'elenco dei Marketplace commerciali.

Includere uno o più dei seguenti elementi nella descrizione:

- Il valore e i vantaggi principali dell'offerta
- Categoria o associazioni di settore o entrambi
- Opportunità di acquisto in-app
- Eventuali divulgazioni richieste

Ecco alcuni suggerimenti per scrivere la descrizione:

- Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includere gli elementi seguenti:
  - Descrizione dell'offerta.
  - Il tipo di utente che trae vantaggio dall'offerta.
  - Esigenze dei clienti o problemi che l'offerta indirizzi.
- Tenere presente che le prime frasi potrebbero essere visualizzate nei risultati del motore di ricerca.
- Non fare affidamento sulle funzionalità e sulle funzionalità per vendere l'offerta. Concentrarsi invece sul valore fornito dall'offerta.
- Usare parole specifiche del settore o basate sui vantaggi.

Per rendere più accattivante la descrizione dell'offerta, usare l'editor di testo RTF per formattare la descrizione. L'editor di testo RTF consente di aggiungere numeri, elenchi puntati, grassetto, corsivo e rientri per rendere la descrizione più leggibile.

![Viene illustrata la pagina Panoramica del centro per i partner con il pulsante nuova offerta e l'offerta di servizio di consulenza selezionata.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Collegamento all'informativa sulla privacy

Immettere l'indirizzo Web (URL) dell'informativa sulla privacy dell'organizzazione. Assicurarsi che l'offerta sia conforme alle leggi e alle normative sulla privacy. È inoltre necessario inserire un criterio di privacy valido nel sito Web.

### <a name="useful-links"></a>Collegamenti utili

Fornire documenti online supplementari sull'offerta. Per aggiungere un collegamento, selezionare **+ Aggiungi un collegamento** e quindi completare i campi seguenti:

- **Nome** : i clienti visualizzeranno il nome nella pagina dei dettagli.
- **Collegamento (URL)** : immettere un collegamento per i clienti per visualizzare il documento online.

### <a name="customer-support-links"></a>Collegamenti al supporto tecnico

Fornire il sito Web di supporto in cui i clienti possono contattare il team di supporto.

- Sito Web del supporto globale di Azure
- Sito Web del supporto di Azure per enti pubblici

### <a name="partner-support-contact"></a>Contatto per il supporto partner

Fornire le informazioni di contatto per i partner Microsoft da usare quando i clienti aprono un ticket di supporto. Questa operazione non verrà elencata nel Marketplace.

- Name
- Posta elettronica
- Telefono

### <a name="engineering-contact"></a>Contatto tecnico

Fornire le informazioni di contatto per Microsoft da usare in caso di problemi con l'offerta, inclusi i problemi di certificazione. Questa operazione non verrà elencata nel Marketplace.

- Name
- Posta elettronica
- Telefono

### <a name="marketplace-media"></a>Supporti Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate provocheranno il rifiuto dell'invio.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

#### <a name="marketplace-logos"></a>Logo del Marketplace

Fornire i file PNG del logo dell'offerta con le seguenti quattro dimensioni di pixel:

- **Piccolo** (48 x 48)
- **Media** (90 x 90)
- **Grande** (216 x 216)
- **Wide** (255 x 115)

Tutti e quattro i logo sono obbligatori e vengono usati in posizioni diverse nell'inserzione nel Marketplace.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere fino a cinque schermate che mostrano il funzionamento dell'offerta. Ogni screenshot deve avere una dimensione di 1280 x 720 pixel e in formato PNG. È anche necessario aggiungere una didascalia per descrivere la schermata.

#### <a name="videos"></a>Video

È possibile aggiungere fino a cinque video che illustrano l'offerta. Questi devono essere ospitati in un servizio video esterno. Immettere il nome di ogni video, l'indirizzo Web e un'immagine PNG di anteprima del video con dimensioni pari a 1280 x 720 pixel.

Per informazioni aggiuntive sul Marketplace, vedere le [procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Selezionare **Salva bozza** prima di continuare.

## <a name="preview"></a>Anteprima

Nella scheda Anteprima scegliere un **pubblico di anteprima** limitato per convalidare l'offerta prima di pubblicarla in un gruppo più ampio di destinatari del Marketplace.

> [!IMPORTANT]
> Dopo aver verificato l'offerta in anteprima, selezionare **Go Live** per pubblicare l'offerta nel pubblico del Marketplace commerciale.

I destinatari dell'anteprima sono identificati dai GUID di ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ogni utente. Nessuno di questi campi può essere visualizzato dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina **sottoscrizioni** in portale di Azure.

Aggiungere almeno un ID sottoscrizione di Azure, singolarmente (fino a 10) o caricando un file CSV (fino a 100). Aggiungendo questi ID sottoscrizione, si definiscono gli utenti che possono visualizzare l'anteprima dell'offerta prima della pubblicazione in tempo reale. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per il test delle modifiche dell'offerta o degli aggiornamenti all'offerta.

> [!NOTE]
> Un pubblico di anteprima è diverso da un pubblico privato. Un pubblico di anteprima può accedere all'offerta _prima_ che venga pubblicata nel Marketplace. Possono visualizzare e convalidare tutti i piani, inclusi quelli che saranno disponibili solo per un pubblico privato dopo che l'offerta è stata completamente pubblicata nel Marketplace. Un pubblico privato (definito nella scheda piano **prezzi e disponibilità** ) dispone di accesso esclusivo a un piano specifico.

Selezionare **Salva bozza** prima di passare alla sezione successiva, piano panoramica.

## <a name="plan-overview"></a>Panoramica del piano

È possibile fornire opzioni di piano diverse all'interno della stessa offerta nel centro per i partner. Questi piani erano definiti in precedenza come SKU. Per un'offerta è necessario almeno un piano, che può variare in termini di audience di monetizzazione, aree di Azure, funzionalità o immagini di VM.

Dopo aver creato i piani, la scheda **panoramica piano** Mostra:

- Nomi dei piani
- Modelli di licenza
- Destinatari (pubblico o privato)
- Stato di pubblicazione corrente
- Azioni disponibili

Le azioni disponibili nella panoramica del piano variano a seconda dello stato corrente del piano. Tali impostazioni includono:

- **Elimina bozza** : se lo stato del piano è una bozza
- **Interrompi vendita piano** o **Sincronizza destinatari privati** : se lo stato del piano è pubblicato in tempo reale

### <a name="create-new-plan"></a>Crea nuovo piano

Selezionare **+ Crea nuovo piano** nella parte superiore. Verrà visualizzata la finestra di dialogo **nuovo piano** .

Nella casella **ID piano** creare un ID piano univoco per ogni piano di questa offerta. Questo ID sarà visibile ai clienti nell'indirizzo Web del prodotto. Usare solo lettere minuscole e numeri, trattini o caratteri di sottolineatura e un massimo di 50 caratteri.

> [!NOTE]
> Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.

Nella casella **nome piano** immettere un nome per il piano. I clienti visualizzano questo nome per decidere quale piano selezionare nell'offerta. Creare un nome univoco che indichi chiaramente le differenze di ogni piano. Ad esempio, è possibile usare **Windows Server** con i piani con **pagamento in base**al consumo, **BYOL**, **Advanced**ed **Enterprise**.

Selezionare **Crea**.

### <a name="plan-setup"></a>Pianificare l'installazione

Impostare la configurazione di alto livello per il tipo di piano, se si riutilizza la configurazione tecnica da un altro piano e in quali aree di Azure il piano dovrebbe essere disponibile. Le selezioni selezionate consentono di determinare quali campi vengono visualizzati in altre schede per lo stesso piano.

#### <a name="reuse-technical-configuration"></a>Riutilizza configurazione tecnica

Se si dispone di più di un piano dello stesso tipo e i pacchetti sono identici, è possibile selezionare **questo piano riutilizza la configurazione tecnica da un altro piano**. Questa opzione consente di selezionare uno degli altri piani dello stesso tipo per l'offerta e di riutilizzare la relativa configurazione tecnica.

> [!NOTE]
> Quando si riutilizza la configurazione tecnica da un altro piano, l'intera scheda **configurazione tecnica** scompare da questo piano. I dettagli relativi alla configurazione tecnica dell'altro piano, inclusi gli eventuali aggiornamenti apportati in futuro, verranno utilizzati anche per questo piano. Questa impostazione non può essere modificata dopo la pubblicazione del piano.

#### <a name="azure-regions"></a>Aree di Azure

Il piano deve essere reso disponibile in almeno un'area di Azure.

Selezionare l'opzione **globale di Azure** per rendere il piano disponibile per i clienti in tutte le aree di Azure pubbliche che hanno l'integrazione con il Marketplace commerciale. Per informazioni dettagliate, vedere [disponibilità geografica e supporto della valuta](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selezionare l'opzione **Azure per enti pubblici** per rendere il piano disponibile nell'area di [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) . Questa area garantisce l'accesso controllato per i clienti da entità federali, statali, locali o tribali degli Stati Uniti, oltre ai partner idonei per la loro gestione. L'autore è responsabile di tutti i controlli di conformità, le misure di sicurezza e le procedure consigliate. Azure per enti pubblici USA Data Center e reti fisicamente isolate (situate solo negli Stati Uniti).

Prima di eseguire la pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), testare e convalidare il piano nell'ambiente in quanto determinati endpoint potrebbero essere diversi. Per configurare e testare il piano, richiedere un account di valutazione da [Microsoft Azure per enti pubblici versione di valutazione](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo che il piano è stato pubblicato e disponibile in un'area di Azure specifica, non è possibile rimuovere tale area.

#### <a name="azure-government-certifications"></a>Certificazioni di Azure per enti pubblici

Questa opzione è visibile solo se si seleziona **Azure per enti pubblici** in **aree di Azure**.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e normative governative. Ad esempio, FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per sensibilizzare le certificazioni per questi programmi, è possibile fornire fino a 100 collegamenti che li descrivono. Questi possono essere collegamenti all'inserzione diretta del programma o collegamenti a descrizioni della conformità con essi nei propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

Selezionare **Salva bozza** prima di continuare.

### <a name="plan-listing"></a>Elenco di piani

Qui è possibile configurare i dettagli dell'elenco del piano. In questa scheda vengono visualizzate informazioni specifiche che possono variare tra i piani nella stessa offerta.

#### <a name="plan-name"></a>Nome piano

Questa operazione è già compilata con il nome assegnato al piano al momento della creazione. Questo nome viene visualizzato nel Marketplace come titolo del piano ed è limitato a 100 caratteri.

#### <a name="plan-summary"></a>Riepilogo del piano

Fornire un breve riepilogo del piano (non l'offerta). Questo riepilogo è limitato a 100 caratteri.

#### <a name="plan-description"></a>Descrizione del piano

Viene descritto cosa rende univoco questo piano software, nonché le differenze tra i piani all'interno dell'offerta. Non descrivere l'offerta, ma solo il piano. La descrizione del piano può contenere un massimo di 2.000 caratteri.

Selezionare **Salva bozza** prima di continuare.

### <a name="pricing-and-availability"></a>Prezzi e disponibilità

In questa scheda verranno configurati gli elementi seguenti:

- Mercati in cui sarà disponibile questo piano
- Prezzo all'ora
- Indica se rendere visibile il piano a tutti o solo a specifici clienti (destinatari privati)

#### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per ogni località di mercato in cui il piano deve essere disponibile per l'acquisto. gli utenti di questi mercati possono comunque distribuire l'offerta in tutte le aree di Azure selezionate nell' **[installazione del piano](#plan-setup)**. Il pulsante **Tax remitted** Mostra i paesi in cui Microsoft ha fatturato le vendite e usa le imposte per conto dell'utente. La pubblicazione in Cina è limitata ai piani che possono essere **gratuiti** o **Bring your own License** (BYOL).

Se sono già stati impostati i prezzi per il piano in Stati Uniti dollari (USD) e si aggiunge un altro percorso di mercato, il prezzo per il nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Rivedere sempre il prezzo di ogni mercato prima della pubblicazione. Esaminare i prezzi usando il collegamento **Export prices (xlsx)** dopo aver salvato le modifiche.

Quando si rimuove un mercato, i clienti del mercato che usano le distribuzioni attive non saranno in grado di creare nuove distribuzioni o di aumentare le distribuzioni esistenti. Le distribuzioni esistenti non saranno interessate.

#### <a name="pricing"></a>Prezzi

**Modello di licenza** : selezionare il **piano di fatturazione mensile basato sull'utilizzo** per configurare i prezzi per questo piano o **Bring your own License** per consentire ai clienti di usare questo piano con la licenza esistente.

Per un piano di fatturazione mensile basato sull'utilizzo, utilizzare una delle tre opzioni di immissione dei prezzi seguenti:

- **Per core** : specificare il prezzo per core in Stati Uniti dollari (USD). Verranno calcolati i prezzi per ogni dimensione core e la conversione in valute locali usando il tasso di cambio corrente.
- **Dimensioni per core** : forniscono i prezzi per ogni dimensione core in USD. I prezzi verranno convertiti in valute locali usando il tasso di cambio corrente.
- Per **mercato e dimensioni core** : offrono i prezzi per ogni dimensione di base per tutti i mercati. È possibile importare i prezzi da un foglio di calcolo.

> [!NOTE]
> Salvare le modifiche ai prezzi per consentire l'esportazione dei dati dei prezzi. Una volta pubblicato il prezzo per un mercato del piano, non sarà possibile modificarlo in un secondo momento. Assicurarsi che i prezzi siano giusti prima della pubblicazione esportando il foglio di calcolo dei prezzi e esaminando il prezzo in ogni mercato.

#### <a name="free-trial"></a>Versione di prova gratuita

Puoi offrire una versione di valutazione gratuita di un mese o di tre mesi ai tuoi clienti.

#### <a name="visibility"></a>Visibilità

È possibile progettare ogni piano in modo che sia visibile a tutti o solo a un gruppo di destinatari preselezionato. Assegnare le appartenenze a questo gruppo di destinatari limitati usando gli ID sottoscrizione di Azure.

**Pubblico** : il piano può essere visualizzato da tutti.

**Pubblico privato** : rendere il piano visibile solo a destinatari preselezionati. Una volta pubblicato come piano privato, è possibile aggiornare il gruppo di destinatari o impostarlo come pubblico. Dopo aver reso pubblico un piano, è necessario che rimanga pubblico. non è possibile modificarlo di nuovo in privato.

**Destinatari con restrizioni (ID sottoscrizione di Azure)** : assegnare i destinatari che avranno accesso a questo piano privato usando gli ID sottoscrizione di Azure. Facoltativamente, includere una descrizione di ogni ID sottoscrizione di Azure assegnato. Aggiungere fino a 10 ID sottoscrizione manualmente oppure 20.000 se si importa un foglio di calcolo CSV. Gli ID sottoscrizione di Azure sono rappresentati come GUID e le lettere devono essere minuscole.

> [!NOTE]
> Un pubblico privato o con restrizioni è diverso dai destinatari di anteprima definiti nella scheda **Anteprima** . Un pubblico di anteprima può accedere all'offerta _prima_ della sua pubblicazione nel Marketplace. Sebbene la scelta del pubblico privato venga applicata solo a un piano specifico, i destinatari di anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

#### <a name="hide-plan"></a>Nascondi piano

Se la macchina virtuale deve essere utilizzata solo indirettamente quando viene fatto riferimento tramite un altro modello di soluzione o applicazione gestita, selezionare questa casella per pubblicare la macchina virtuale, ma nasconderla dai clienti cercandola direttamente.

> [!NOTE]
> I piani nascosti non supportano i collegamenti di anteprima.

Selezionare **Salva bozza** prima di continuare.

### <a name="technical-configuration"></a>Configurazione tecnica

Fornire le immagini e altre proprietà tecniche associate a questo piano. Per informazioni dettagliate, vedere [creare un asset tecnico della macchina virtuale di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Questa scheda non viene visualizzata se il piano è stato configurato per riutilizzare i pacchetti di un altro piano nella scheda **Imposta piano** .

#### <a name="operating-system"></a>Sistema operativo

- **Famiglia di sistemi operativi** : selezionare un sistema operativo **Windows** o **Linux**
- **Versione** o **Fornitore** : scegliere la versione di Windows o il fornitore di Linux
- **Nome descrittivo sistema operativo** : scegliere un nome descrittivo del sistema operativo. Questo nome è visibile ai clienti

#### <a name="recommended-vm-sizes"></a>Recommended VM Sizes (Dimensioni VM consigliate)

Selezionare fino a sei dimensioni consigliate per le macchine virtuali da visualizzare in Azure Marketplace.

#### <a name="open-ports"></a>Aprire le porte

Aprire porte pubbliche o private in una macchina virtuale distribuita.

#### <a name="storage-option-for-deployment"></a>Opzione di archiviazione per la distribuzione

**Opzione di distribuzione del disco** : selezionare il tipo di distribuzione del disco utilizzabile dagli utenti quando si usa la macchina virtuale. Microsoft consiglia di limitare la distribuzione solo alla distribuzione del disco gestito.

#### <a name="properties"></a>Proprietà

**Supporto della rete accelerata** : selezionare se la VM supporta la [rete accelerata](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>VM Images (Immagini di VM)

Specificare una versione del disco e l'URI SAS per le immagini di macchina virtuale. Aggiungere fino a 16 dischi dati per ogni immagine di macchina virtuale. Specificare una nuova versione dell'immagine per ogni piano in un determinato invio. Dopo la pubblicazione di un'immagine, non è possibile modificarla, ma è possibile eliminarla. L'eliminazione di una versione impedirà agli utenti nuovi ed esistenti di distribuire una nuova istanza della versione eliminata.

- **Versione del disco** è la versione dell'immagine che si sta fornendo.
- L' **URI SAS** è il percorso di archiviazione di Azure in cui è stato archiviato il VHD del sistema operativo.
- Le immagini del disco dati sono anche URI SAS VHD archiviati nella propria archiviazione di Azure.
- Aggiungere solo un'immagine per ogni invio in un piano.

Indipendentemente dal sistema operativo utilizzato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. I clienti non possono rimuovere dischi che fanno parte di un'immagine al momento della distribuzione, ma possono sempre aggiungere dischi durante o dopo la distribuzione.

Selezionare **Salva bozza** prima di continuare e tornare alla **Panoramica del piano**.

## <a name="resell-through-csps"></a>Rivendi tramite CSP

Espandi la portata dell'offerta rendendola disponibile ai partner nel programma [Cloud Solution Provider](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Tutti i piani Bring your own License (BYOL) vengono scelti automaticamente; è possibile scegliere di optare per i piani non BYOL.

Selezionare **Salva bozza** prima di continuare.

## <a name="test-drive"></a>Test drive

Configurare una dimostrazione (test drive) che consente ai clienti di provare l'offerta prima di acquistarla. Per creare un ambiente dimostrativo che consente ai clienti di provare l'offerta per un periodo di tempo fisso, vedere [test drive dell'offerta nel Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Per abilitare una test drive, selezionare la casella di controllo Abilita un test drive nella scheda [installazione offerta](#test-drive) . Per rimuovere test drive dall'offerta, deselezionare questa casella di controllo.

Risorse test drive aggiuntive:

- Procedure consigliate tecniche
- Procedure consigliate di marketing
- Cenni preliminari sulle unità di test (PDF) assicurarsi che il blocco popup sia disattivato.

Selezionare **Salva bozza** prima di continuare.

## <a name="review-and-publish"></a>Esaminare e pubblicare

Una volta completate tutte le sezioni obbligatorie dell'offerta, è possibile inviarle per la revisione e la pubblicazione.

Nell'angolo superiore destro del portale selezionare **revisione e pubblicazione**.

In questa pagina è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta.
  - **Non avviato** : la sezione non è stata avviata e deve essere completata.
  - **Incompleto** : la sezione contiene errori che devono essere corretti o che è necessario fornire altre informazioni. Per istruzioni su come aggiornare questa sezione, vedere le sezioni precedenti di questo documento.
  - **Completa** : la sezione è completa e non sono presenti errori. Tutte le sezioni dell'offerta devono essere completate prima di poter inviare l'offerta.
- **Note per la certificazione** : fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente. Fornire eventuali note supplementari utili per comprendere l'app.

Per inviare l'offerta per la pubblicazione, selezionare **revisione e pubblicazione**.

Ti invieremo un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Per pubblicare l'offerta al pubblico (o, se un'offerta privata, per un pubblico privato), accedere al centro per i partner, individuare la pagina **Panoramica** dell'offerta e selezionare **Go-Live**. Lo stato dell'offerta verrà visualizzato nella parte superiore della pagina quando la pubblicazione è in corso.

### <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

Il passaggio di **convalida manuale** del processo di pubblicazione rappresenta un'ampia revisione dell'offerta e delle risorse tecniche associate. Se questo processo rileva errori con l'offerta, si riceverà un report di certificazione che descrive in dettaglio i problemi. È sufficiente apportare le correzioni necessarie e ripubblicare l'offerta.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina di **Panoramica dell'offerta** Mostra una rappresentazione visiva dei passaggi necessari per pubblicare questa offerta (completati e in corso) e il tempo necessario per completare ogni passaggio.

Questa pagina include i collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è un'offerta bozza di [eliminazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è Live- [stop selling the offer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima- [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se il server di pubblicazione non è stato completato, [annullare la pubblicazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Esempi di Marketplace

Questi esempi illustrano come viene visualizzata l'offerta in Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Dettagli sull'offerta di Azure Marketplace

![Esempio di schermata dei dettagli dell'offerta di Azure Marketplace](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Risultati della ricerca in Azure Marketplace

![Esempio di schermata dei dettagli di ricerca di Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Dettagli del piano di Azure Marketplace

![Esempio di schermata dei dettagli del piano di Azure Marketplace](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Dettagli dell'offerta portale di Azure

![Esempio di schermata dei dettagli dell'offerta portale di Azure](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Risultati della ricerca portale di Azure

![Esempio di schermata dei risultati della ricerca portale di Azure](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Dettagli piano portale di Azure

![Esempio di schermata dei dettagli del piano portale di Azure](media/avm-create6.png)

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
