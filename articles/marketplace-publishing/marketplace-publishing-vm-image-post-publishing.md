<properties
   pageTitle="Gestione di un'immagine di macchina virtuale in Azure Marketplace | Microsoft Azure"
   description="Guida dettagliata su come gestire l'immagine della macchina virtuale in Azure Marketplace dopo la pubblicazione iniziale."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="hascipio;"/>

# Guida di post-produzione per le offerte di macchine virtuali in Azure Marketplace

Questo articolo spiega come aggiornare un'offerta di macchine virtuali attiva in Azure Marketplace. Descrive anche come eseguire il processo di aggiunta di uno o più nuovi SKU a un'offerta esistente e rimuovere un'offerta di macchine virtuali o uno SKU attivo da Azure Marketplace.

**Quando lo SKU è attivo in Azure Marketplace, non è possibile aggiornare i dettagli indicati di seguito:**

- **Identificatore SKU**
- **Identificatore di server di pubblicazione**
- **Identificatore di offerte**
- **Modifica dei prezzi**
- **Modifica del modello di fatturazione**
- **Rimozione di aree di fatturazione**


## 1\. Come aggiornare i dati tecnici di uno SKU

È possibile aggiornare un'immagine di macchina virtuale e ripubblicare l'offerta seguendo i passaggi indicati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **IMMAGINI VM**.
4. Dalla sezione **SKU** della scheda IMMAGINI VM trovare lo SKU da aggiornare, quindi, aggiornare il numero di versione dello SKU. La nuova versione deve avere il formato X.Y.Z, dove X, Y, Z sono numeri interi. Le modifiche della versione devono essere solo incrementali.
5. Nella casella **OS VHD URL (URL VHD del sistema operativo)** aggiornare l'URI di firma di accesso condiviso creato per il VHD del sistema operativo e salvare le modifiche.
6. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
7. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img01.png)

## 2\. Come aggiornare i dati non tecnici di un'offerta o di uno SKU

È possibile aggiornare i dati non tecnici (marketing, legali, di supporto, categorie) dell'offerta o dello SKU attivo in Azure Marketplace.

### 2\.1 Aggiornare la descrizione dell'offerta e dei logo

È possibile aggiornare i dati di un'offerta e ripubblicarla seguendo i passaggi indicati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING**.
4. Fare clic sul pulsante **ENGLISH (US) (INGLESE (USA))**.
5. Dal menu sul lato sinistro fare clic sulla scheda **DETTAGLI**. Sotto la sezione *DESCRIZIONE* della scheda **DETTAGLI** è possibile aggiornare il titolo dell'offerta, fornire il riepilogo, fornire il riepilogo lungo e salvare le modifiche.

    >[AZURE.NOTE] Quando si aggiornano i dettagli dello SKU tenere presente quanto segue. **Non immettere testi duplicati sotto la descrizione dell'offerta e sotto la descrizione dello SKU. Non immettere testi duplicati sotto il titolo dello SKU e sotto il riepilogo lungo dell'offerta. Non immettere testi duplicati sotto il titolo dello SKU e sotto il riepilogo dell'offerta.**

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.png)

6. Sotto la sezione *LOGOS (LOGO)* della scheda **DETTAGLI** è possibile aggiornare i logo. Verificare, tuttavia, che i logo seguano le [linee guida di Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (vedere la sezione Passaggio 1: Fornire contenuti di marketing di Marketplace -> Dettagli -> Linee guida per il Logo di Azure Marketplace).

    >[AZURE.NOTE] L'icona alta è facoltativa. È possibile scegliere di non caricare un'icona alta. Tuttavia, una volta caricata l'icona alta, non è possibile eliminarla dal portale di pubblicazione. In tal caso, è necessario seguire le [linee guida sull'icona alta ](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (vedere la sezione Passaggio 1: Fornire contenuti di marketing di Marketplace -> Dettagli -> Indicazioni aggiuntive per il banner del logo alto).

7. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md).
8. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.1.png)

### 2\.2. Aggiornare la descrizione dello SKU

È possibile aggiornare i dati di uno SKU e ripubblicarlo seguendo i passaggi indicati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING**.
4. Fare clic sul pulsante **ENGLISH (US) (INGLESE (USA))**.
5. Dal menu sul lato sinistro fare clic sulla scheda **PIANI**. Sotto la sezione *SKU* della scheda **PIANI** è possibile aggiornare il titolo, il riepilogo e le informazioni di descrizione dello SKU, quindi salvare le modifiche.

    >[AZURE.NOTE] Quando si aggiornano i dettagli dello SKU tenere presente quanto segue. **Non immettere testi duplicati sotto la descrizione dell'offerta e sotto la descrizione dello SKU. Non immettere testi duplicati sotto il titolo dello SKU e sotto il riepilogo lungo dell'offerta. Non immettere testi duplicati sotto il titolo dello SKU e sotto il riepilogo dell'offerta.**

6. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo collegamento
7. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.2.png)

### 2\.3 Modificare i collegamenti esistenti o aggiungere nuovi collegamenti

È possibile modificare i collegamenti esistenti o aggiungere nuovi collegamenti e quindi ripubblicare l'offerta seguendo la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING**.
4. Fare clic sul pulsante **ENGLISH (US) (INGLESE (USA))**.
5. Dal menu sul lato sinistro fare clic sulla scheda **COLLEGAMENTI**.
6. Se si vuole aggiungere un nuovo collegamento, sotto la sezione *COLLEGAMENTI* fare clic sul pulsante **AGGIUNGI COLLEGAMENTO**. Viene visualizzata la finestra di dialogo *"Aggiungi collegamento"*. In questa finestra di dialogo è possibile aggiungere il collegamento relativo al titolo e ai campi dell'URL e salvare le modifiche. È possibile immettere qualsiasi collegamento che contiene informazioni utili per i clienti.
7. Se si vuole aggiornare o eliminare un collegamento esistente, selezionare il collegamento appropriato e fare clic sul pulsante Modifica o Elimina.

    >[AZURE.NOTE] Assicurarsi che i collegamenti immessi in questa sezione funzionino correttamente perché vengono convalidati durante il processo di richiesta di produzione.

8. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md).
9. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.3.png)

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### 2\.4 Modificare un'immagine di esempio esistente o aggiungere una nuova immagine di esempio

È possibile modificare le immagini di esempio esistenti o aggiungere nuove immagini di esempio e quindi ripubblicare l'offerta seguendo la procedura seguente:

>[AZURE.NOTE] Solo un'immagine di esempio viene visualizzata in [https://portal.azure.com](https://portal.azure.com).

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING**.
4. Fare clic sul pulsante **ENGLISH (US) (INGLESE (USA))**.
5. Dal menu sul lato sinistro fare clic sulla scheda **SAMPLE IMAGES (IMMAGINI DI ESEMPIO)**.
6. Se si vuole aggiungere una nuova immagine di esempio, nella sezione *Sample Images (Immagini di esempio)* fare clic sul pulsante **UPLOAD A NEW IMAGE (CARICA UNA NUOVA IMMAGINE)** e quindi salvare le modifiche.

    >[AZURE.NOTE] L’inclusione di un'immagine di esempio è un passaggio facoltativo.

7. Se si vuole aggiornare o eliminare un'immagine di esempio esistente, trovare l'immagine di esempio appropriata e quindi fare clic sul pulsante **SOSTITUISCI IMMAGINE** o sul pulsante Elimina.

8. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md).
9. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.4.png)

### 2\.5 Aggiornare il contenuto legale

È possibile aggiornare il contenuto legale e ripubblicare l'offerta seguendo i passaggi indicati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING**.
4. Fare clic sul pulsante **ENGLISH (US) (INGLESE (USA))**.
5. Dal menu sul lato sinistro fare clic sulla scheda **NOTE LEGALI**. Sotto la sezione *Note legali* è possibile aggiornare i criteri o le condizioni di utilizzo. Immettere o incollare i criteri o le condizioni nella casella di testo *Condizioni per l'utilizzo* e salvare le modifiche.
6. Il limite di caratteri per le condizioni per l'utilizzo è 1.000.000 caratteri.
7. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
8. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.5.png)

### 2\.6 Aggiornare le informazioni di supporto

È possibile aggiornare le informazioni di supporto e ripubblicare l'offerta seguendo i passaggi indicati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **SUPPORTO**.
4. Sotto la sezione *Engineering Contact (Contatto tecnico)* della scheda **SUPPORTO** è possibile aggiornare i dettagli di contatto. Questi dettagli di contatto vengono usati per comunicazioni interne solo tra partner e Microsoft.
5. Sotto la sezione *Supporto tecnico* della scheda **SUPPORTO** è possibile aggiornare i dettagli del supporto tecnico in dettaglio come **Nome, Indirizzo di posta elettronica, Telefono** e **URL supporto**. Questi dettagli di contatto vengono usati per comunicazioni interne solo tra partner e Microsoft.

    >[AZURE.NOTE] Se si vuole fornire solo il supporto di posta elettronica, specificare un numero di telefono fittizio nella sezione **Supporto tecnico**. In questo caso, verrà usato l'indirizzo di posta elettronica fornito.

6. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
7. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

### 2\.7 Aggiornare le categorie

È possibile aggiornare la sezione delle categorie per un'offerta e ripubblicare l'offerta seguendo i passaggi indicati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **CATEGORIE**.
4. Sotto la sezione *Categorie* è possibile aggiornare le categorie per l'offerta e salvare le modifiche. È possibile selezionare fino a cinque categorie per la raccolta di Azure Marketplace.
5. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
6. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

## 3\. Come aggiungere un nuovo SKU in un'offerta attiva

È possibile aggiungere un nuovo SKU in un'offerta attiva seguendo i passaggi indicati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **SKU**, quindi fare clic sul pulsante **ADD A SKU (AGGIUNGI UNO SKU)**. Viene visualizzata una nuova finestra di dialogo. Immettere un identificatore dello SKU in lettere minuscole. Selezionare la casella di controllo relativa al modello di fatturazione BYOL (Bring Your Own License) se si vuole pubblicare il nuovo SKU con tale modello. In caso contrario, deselezionare la casella di controllo per BYOL, quindi selezionare il segno di spunta nella finestra di dialogo per creare un nuovo SKU. Se si è scelto di non usare il modello di fatturazione BYOL per il nuovo SKU, il modello di fatturazione verrà automaticamente impostato su Ogni ora per il nuovo SKU. Se si vuole abilitare la versione di valutazione gratuita di 30 giorni per il modello di fatturazione oraria, fare clic sull'opzione "Un mese" per "Is a free a free trial available?" (È disponibile una versione di valutazione gratuita?). Diversamente, selezionare "NESSUNA VERSIONE DI PROVA" [nota: l'opzione "Is a free a free trial available?" (È disponibile una versione di valutazione gratuita?) viene visualizzata solo se NON è stato selezionato BYOL nella finestra di dialogo durante la creazione del nuovo SKU.]

    >[AZURE.IMPORTANT] L'opzione "Hide this SKU from the Marketplace because it should always be bought via a solution template" (Nascondi questo SKU da Marketplace perché deve essere sempre acquistato usando un modello di soluzione) deve essere contrassegnata come "SÌ" SOLO se è stata ottenuta l'approvazione per la pubblicazione di un'offerta di modello di in Azure Marketplace. Diversamente, l'opzione deve essere sempre contrassegnata come "NO".

4. Dal menu sul lato sinistro fare clic sulla scheda **IMMAGINI VM** e trovare il nuovo SKU che è stato creato.
5. Per impostare il nuovo SKU, vedere il PASSAGGIO 5 di questo [collegamento](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image).
6. Per aggiungere il materiale di marketing per il nuovo SKU, vedere la sezione Passaggio 1: Fornire contenuti di marketing di Marketplace -> Dettagli -> punti da 2 a 5 di questo [collegamento](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Per aggiungere le informazioni sui prezzi per il nuovo SKU, vedere la sezione 2.1. Impostare i prezzi delle VM di questo [collegamento](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Dopo aver apportato le modifiche, passare alla scheda **PUBBLICA** e fare clic sul pulsante **PUSH TO STAGING (ESEGUI PUSH A STAGING)**. Per indicazioni dettagliate sul test dell'offerta nell'ambiente di staging, vedere questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
9. Dopo aver testato l'offerta in staging, passare alla scheda **PUBBLICA** nel portale di pubblicazione e fare clic sul pulsante **REQUEST APPROVAL TO PUSH TO PRODUCTION (RICHIEDI APPROVAZIONE PER ESEGUIRE IL PUSH IN PRODUZIONE)** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img03.1-1.png)

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img03.1-2.png)

## 4\. Come eliminare un'offerta o uno SKU attivo da Azure Marketplace

Esistono diversi aspetti che devono essere presi in considerazione nel caso di una richiesta di rimozione di un'offerta attiva. Seguire la procedura seguente per ottenere informazioni dal team di supporto per rimuovere un'offerta/uno SKU attivo da Azure Marketplace:

1.	Generare un ticket di supporto usando questo [collegamento](https://support.microsoft.com/it-IT/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=it-IT&supportregion=it-IT&pesid=15635&ccsid=635993707583706681)
2.	Selezionare il tipo di problema come **"Gestione delle offerte"** e selezionare una categoria come **"Modifying an offer and/or SKU already in production" (Modifica di un'offerta e/o di uno SKU già in produzione)**
3.	Inviare la richiesta

Il team di supporto guiderà l'utente nel processo di eliminazione dell'offerta o dello SKU.

>[AZURE.NOTE] È sempre possibile eliminare l'offerta mentre è nello stato bozza (cioè non in STAGING o PRODUZIONE) facendo clic sul pulsante **ELIMINA BOZZA** sotto la scheda **CRONOLOGIA**.

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)
- [Informazioni sui report di Insights per i venditori](marketplace-publishing-report-seller-insights.md)
- [Informazioni sui report sui proventi](marketplace-publishing-report-payout.md)
- [Come modificare l'incentivo per i rivenditori per i Cloud Solution Provider](marketplace-publishing-csp-incentive.md)
- [Risoluzione dei problemi di pubblicazione comuni in Marketplace](marketplace-publishing-support-common-issues.md)
- [Ottenere supporto come editore](marketplace-publishing-get-publisher-support.md)
- [Creazione di un'immagine di macchina virtuale in locale](marketplace-publishing-vm-image-creation-on-premise.md)
- [Creare una macchina virtuale con Windows nel portale di anteprima di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

<!---HONumber=AcomDC_0608_2016-->