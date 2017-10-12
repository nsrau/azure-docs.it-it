---
title: Gestione di un'immagine di macchina virtuale in Azure Marketplace | Microsoft Docs
description: Guida dettagliata su come gestire l'immagine della macchina virtuale in Azure Marketplace dopo la pubblicazione iniziale
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ms.openlocfilehash: e1f90650e71345957c2d353774cb8bef62c1868b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guida di post-produzione per le offerte di macchine virtuali in Azure Marketplace
Questo articolo spiega come aggiornare un'offerta di macchine virtuali attiva in Azure Marketplace. L'articolo illustra il processo di aggiunta di uno o più nuovi SKU a un'offerta esistente. Illustra anche il processo di rimozione dell'offerta attiva di una macchina virtuale o di una SKU dal Marketplace.

Dopo che un'offerta/uno SKU è passato al [Portale di Azure](http://portal.azure.com), non è possibile modificare le seguenti caselle di testo:

* **Offer Identifier** (Identificatore offerta): nel portale di pubblicazione, passare a **Macchine virtuali** e selezionare l'offerta. Quindi fare clic su **Immagini VM** > **Offer Identifier** (Identificatore offerta).
* **SKU Identifier** (Identificatore SKU): nel portale di pubblicazione, passare a **Macchine virtuali** e selezionare l'offerta. Quindi fare clic su **SKU** > **Aggiungere uno SKU**.
* **Publisher Namespace** (Spazio dei nomi dell'entità di pubblicazione): nel portale di pubblicazione, passare a **macchine virtuali** > **Procedura dettagliata** > **Informazioni sull'azienda**, (disponibile in "Step 2 Register Your Company" (Passaggio 2 Registrazione dell'azienda)) > **Publisher Namespace** (Spazio dei nomi dell'entità di pubblicazione)  > **Spazio dei nomi**.

Dopo che un'offerta/uno SKU è elencato nel [Marketplace](http://azure.microsoft.com/marketplace), non è possibile modificare le seguenti caselle di testo:

* **Offer Identifier** (Identificatore offerta): nel portale di pubblicazione, passare a **Macchine virtuali** e selezionare l'offerta. Quindi fare clic su **Immagini VM** > **Offer Identifier** (Identificatore offerta).
* **SKU Identifier** (Identificatore SKU): nel portale di pubblicazione, passare a **Macchine virtuali** e selezionare l'offerta. Quindi fare clic su **SKU** > **Aggiungere uno SKU**.
* **Publisher Namespace (Spazio dei nomi dell'entità di pubblicazione):** Nel Portale di pubblicazione, passare a **macchine virtuali** > **scheda Procedura dettagliata** > **Tell Us About Your Company** (Informazioni sull'azienda), (disponibile nel “Passaggio 2 Registrazione") **Publisher Namespace** > **Spazio dei nomi**.
* **Porte**: nel portale di pubblicazione, passare a **Macchine virtuali** e selezionare l'offerta. Quindi fare clic su **Immagini VM** > **Porte aperte**.
* **Modifica dei prezzi degli SKU elencati**
* **Modifica del modello di fatturazione degli SKU elencati**
* **Rimozione delle aree di fatturazione degli SKU elencati**
* **Modifica del numero di dischi dati degli SKU elencati**

## <a name="update-the-technical-details-of-a-sku"></a>Aggiornare i dati tecnici di uno SKU
Per aggiungere una nuova versione allo SKU elencato e ripubblicare l'offerta, seguire questi passaggi:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **Immagini VM**.
4. Dalla sezione **SKU** trovare lo SKU da aggiornare.
5. Aggiungere un nuovo numero di versione dello SKU e fare clic sul pulsante **+**. La nuova versione deve avere un formato X.Y.Z, dove X, Y e Z sono numeri interi. Le modifiche della versione devono essere solo incrementali.
6. Nella casella **OS VHD URL** (URL DEL DISCO RIGIDO VIRTUALE DEL SISTEMA OPERATIVO), inserire l'URI di firma di accesso condiviso creato per il VHD del sistema operativo e salvare le modifiche.

   > [!IMPORTANT]
   > Non è possibile incrementare o decrementare il numero dei dischi dati di uno SKU elencato. In questo caso è necessario creare un nuovo SKU. Per istruzioni dettagliare vedere la sezione [Come aggiungere un nuovo SKU in un'offerta elencata](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
8. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![VM Images (Immagini di VM)](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Aggiornare i dati non tecnici di un'offerta o di uno SKU
È possibile aggiornare i dati non tecnici (marketing, legali, di supporto, categorie) dell'offerta o dello SKU attivo nel Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Aggiornare la descrizione dell'offerta e i loghi
Per aggiornare i dettagli dell'offerta e ripubblicare l'offerta, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **MARKETING**.
4. Fare clic su **English (US)**.
5. Fare clic sulla scheda **DETTAGLI**. Nella sezione **Descrizione** aggiornare l'offerta **TITOLO**, **RIEPILOGO**, e **LONG SUMMARY** (RIEPILOGO LUNGO) e salvare le modifiche.

   > [!NOTE]
   > Quando si aggiornano i dettagli dello SKU, tenere presente queste restrizioni: 
   * Non immettere testi duplicati per la descrizione dell'offerta e la descrizione dello SKU.
   * Non immettere testi duplicati per il titolo dello SKU e per il riepilogo lungo dell'offerta. 
   * Non immettere testi duplicati per il titolo dello SKU e per il riepilogo dell'offerta.
   >
   >

    ![Dettagli](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. Nella sezione **LOGO** della scheda **DETTAGLI** aggiornare i loghi. Assicurarsi che i loghi siano conformi alle [linee guida di Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > L'icona alta è facoltativa. È possibile scegliere di non caricare un'icona alta. Tuttavia, dopo aver caricato un'icona alta, non è possibile eliminarla dal portale di pubblicazione. Seguire le [linee guida sull'icona alta](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
8. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![Loghi](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Aggiornare la descrizione dello SKU
Per aggiornare i dettagli dello SKU e ripubblicare l'offerta, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **MARKETING**.
4. Fare clic su **English (US)**.
5. Fare clic sulla scheda **PIANI**. Nella sezione **SKU** aggiornare il **TITOLO**, il **RIEPILOGO** e la **DESCRIZIONE** dello SKU e salvare le modifiche.

   > [!NOTE]
   > Quando si aggiornano i dettagli dello SKU, tenere presente queste restrizioni: 
   * Non immettere testi duplicati per la descrizione dell'offerta e la descrizione dello SKU. 
   * Non immettere testi duplicati per il titolo dello SKU e per il riepilogo lungo dell'offerta. 
   * Non immettere testi duplicati per il titolo dello SKU e per il riepilogo dell'offerta.
   >
   >
6. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
7. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![Piani](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Modificare i collegamenti esistenti o aggiungere nuovi collegamenti
Per modificare i collegamenti esistenti o aggiungere nuovi collegamenti e quindi ripubblicare l'offerta, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **MARKETING**.
4. Fare clic su **English (US)**.
5. Fare clic sulla scheda **COLLEGAMENTI**.
6. Aggiungere un nuovo collegamento, nella sezione **Collegamenti** fare clic su **+ ADD LINK** (+ AGGIUNGI COLLEGAMENTO). Nella finestra di dialogo **Aggiungi collegamento** inserire il **TITOLO** e l'**URL** del collegamento e salvare le modifiche. È possibile immettere qualsiasi collegamento che contiene informazioni utili per i clienti.
7. Per aggiornare o eliminare un collegamento esistente, selezionare il collegamento e fare clic sul pulsante **Modifica** o **Elimina**.

   > [!NOTE]
   > Assicurarsi che i collegamenti immessi in questa sezione funzionino correttamente, perché questi collegamenti vengono convalidati durante il processo di richiesta di produzione.
   >
   >
8. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
9. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![Collegamenti](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Aggiungi collegamento](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Modificare un'immagine di esempio esistente o aggiungere una nuova immagine di esempio
Per modificare un'immagine di esempio esistente o aggiungere nuove immagini di esempio e quindi ripubblicare l'offerta, seguire la procedura seguente:

> [!NOTE]
> Solo un'immagine di esempio viene visualizzata nel [Portale di Azure](https://portal.azure.com).
>
>

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **MARKETING**.
4. Fare clic su **English (US)**.
5. Fare clic sulla scheda **IMMAGINI DI ESEMPIO**.
6. Per aggiungere una nuova immagine di esempio, nella sezione **Immagini di esempio** fare clic su **UPLOAD A NEW IMAGE** (CARICA UNA NUOVA IMMAGINE) e salvare le modifiche.

   > [!NOTE]
   > L'inclusione di un'immagine di esempio è facoltativa.
   >
7. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
8. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![Immagini di esempio](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Aggiornare il contenuto legale
Per aggiornare il contenuto legale e ripubblicare l'offerta, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **MARKETING**.
4. Fare clic su **English (US)**.
5. Fare clic sulla scheda **NOTE LEGALI**. Nella sezione **Note legali** aggiornare i criteri/le condizioni per l'utilizzo. Immettere o incollare i criteri o le condizioni nella casella di testo **CONDIZIONI PER L'UTILIZZO** e salvare le modifiche.
6. Il limite di caratteri per le condizioni per l'utilizzo è 1 milione.
7. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
8. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![Note legali](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Aggiornare le informazioni di supporto
Per aggiornare le informazioni di supporto e ripubblicare l'offerta, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **SUPPORTO**.
4. Nella sezione **Engineering Contact** (Contattare un tecnico) aggiornare i dettagli di contatto dei tecnici. Questi dettagli di contatto vengono usati per comunicazioni interne solo tra partner e Microsoft.
5. Nella sezione **Supporto tecnico** aggiornare i dettagli di contatto del supporto e **SUPPORT URL** (URL DEL SUPPORTO). Questi dettagli di contatto vengono usati per comunicazioni interne solo tra partner e Microsoft.

   > [!NOTE]
   > Se si vuole fornire solo il supporto tramite posta elettronica, inserire un numero di telefono fittizio nella sezione **Supporto tecnico**. In questo caso, viene invece usato l'indirizzo di posta elettronica fornito.
   >
   >
6. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
7. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![Supporto](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Aggiornare le categorie
Per aggiornare la sezione delle categorie per un'offerta e ripubblicare l'offerta, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **CATEGORIE**.
4. Nella sezione **Categorie** aggiornare le categorie per l'offerta e salvare le modifiche. È possibile scegliere fino a cinque categorie per la raccolta di Azure Marketplace.
5. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
6. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![Categorie](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Aggiungere un nuovo SKU in un'offerta elencata
Per aggiungere un nuovo SKU nell'offerta attiva, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **SKU**. Fare quindi clic su **Add a SKU** (Aggiungi SKU). 
4. Nella finestra di dialogo immettere un **Identificatore di SKU** in minuscolo. Selezionare la casella di controllo **Bring your own license (BYOL) billing model** (Modello di fatturazione Bring your own license (BYOL)) se si desidera pubblicare il nuovo SKU con tale modello. In caso contrario, deselezionare la casella di controllo. Selezionare il segno di spunta per creare un nuovo SKU. Se è stato scelto il modello di fatturazione BYOL, il modello di fatturazione viene impostato automaticamente ad ogni ora. Se si desidera la versione di prova gratuita di 30 giorni per il modello di fatturazione oraria, selezionare **Un mese** in **Is a free trial available?** (È disponibile una versione di valutazione gratuita?). In caso contrario, selezionare **Nessuna versione di prova**. (**Is a free trial available?** (È disponibile una versione di valutazione gratuita?) viene visualizzato solo se non è stato selezionato il metodo BYOL durante la creazione del nuovo SKU).

   > [!IMPORTANT]
   > **Hide this SKU from the Marketplace because it should always be bought via a solution template** (Nascondi questo SKU da Marketplace perché deve essere sempre acquistato usando un modello di soluzione) deve essere contrassegnata come **Sì***solo* se è stata ottenuta l'approvazione per la pubblicazione di un modello di soluzione. Diversamente, l'opzione deve essere sempre **No**.
   >
   >
4. Nel menu a sinistra fare clic sulla scheda **Immagini VM** e trovare il nuovo SKU che è stato creato.
5. Per impostare il nuovo SKU, vedere [Ottenere la certificazione per l'immagine di macchina virtuale](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) per informazioni aggiuntive.
6. Per aggiungere materiale di marketing per il nuovo SKU, vedere [Fornire contenuti di marketing di Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Per aggiungere informazioni sui prezzi per il nuovo SKU, vedere [Impostare i prezzi](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
9. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

    ![SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Aggiungere uno SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Modificare il numero dei dischi dati per uno SKU elencato
Non è possibile incrementare o decrementare il numero dei dischi dati di uno SKU elencato. In questo caso è necessario creare un nuovo SKU. Per istruzioni dettagliare vedere la sezione [Come aggiungere un nuovo SKU in un'offerta elencata](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Eliminare un'offerta elencata da Marketplace
Devono essere presi in considerazione diversi aspetti nel caso di una richiesta di rimozione di un'offerta attiva. Per ottenere informazioni dal team di supporto su come rimuovere un'offerta elencata dal Marketplace, seguire la procedura seguente:

1. Generare un ticket di supporto nella pagina [Create an incident](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) (Creare un evento imprevisto).

2. Selezionare il **Tipo di problema** nell'elenco **Gestione delle offerte** e selezionare una **Categoria** come **Modifying an offer and/or SKU already in production** (Modifica di un'offerta e/o di uno SKU già in produzione).
3. Inviare la richiesta.

Il team di supporto guida l'utente nel processo di eliminazione dell'offerta o dello SKU.

> [!NOTE]
> È sempre possibile eliminare l'offerta mentre è in uno stato di Bozza (ma non quando è in gestione temporanea o in produzione). Nella scheda **Cronologia** fare clic su **Elimina bozza**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Eliminare uno SKU elencato dal Marketplace
Per eliminare uno SKU elencato dal Marketplace, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).

2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel riquadro a sinistra fare clic sulla scheda **SKU**.
4. Selezionare lo SKU che si desidera eliminare e fare clic sul pulsante **Elimina**.
5. Passare alla scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiesta di approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.
6. Dopo che l'offerta è stata nuovamente pubblicata nel Marketplace, lo SKU viene eliminato dal Marketplace e dal portale di Azure.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Eliminare la versione corrente di uno SKU elencato dal Marketplace
Per eliminare la versione corrente di uno SKU elencato dal Marketplace, seguire la procedura seguente: 

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).

2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **Immagini VM**.
4. Selezionare lo SKU di cui eliminare la versione corrente e fare clic sul pulsante **Elimina**.
5. Passare alla scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiesta di approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.
6. Dopo che l'offerta è stata nuovamente pubblicata nel Marketplace, la versione corrente dello SKU elencato sarà eliminata dal Marketplace e dal portale di Azure. Verrà quindi eseguito il rollback dello SKU alla versione precedente.

## <a name="revert-the-listing-price-to-production-values"></a>Ripristinare il prezzo elencato ai valori di produzione
Per ripristinare il prezzo elencato ai valori di produzione, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **Prezzi**.
4. Selezionare un'area per la quale ripristinare i prezzi.

    ![Aree dei prezzi](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Per gli SKU con un modello di prezzi orario, reimpostare i prezzi per tutte le memorie centrali presenti in produzione per l'area selezionata. Per gli SKU con un modello di fatturazione BYOL, rendere disponibile lo SKU nell'area selezionando la casella di controllo dello SKU nella sezione **EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY** (DISPONIBILITÀ DELLO SKU CON LICENZA ESTERNA (BYOL)).

    ![Modelli di prezzi](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Fare clic su **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATE** (IMPOSTA AUTOMATICAMENTE I PREZZI PER GLI ALTRI MERCATI IN BASE A QUELLI DEGLI STATI UNITI).

   > [!NOTE]
   > L'etichetta del pulsante può essere diversa a seconda dell'area selezionata. Poiché sono stati selezionati gli Stati Uniti, il pulsante riporta l'etichetta **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATE** (IMPOSTA AUTOMATICAMENTE I PREZZI PER GLI ALTRI MERCATI IN BASE A QUELLI DEGLI STATI UNITI).
   >
   >

    ![Impostare automaticamente i prezzi](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Nella pagina 1 della procedura guidata per applicare automaticamente i prezzi, scegliere il mercato di base e fare clic sul pulsante **freccia**.

    ![Mercato di base](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. Nella pagina 2, scegliere i misuratori e i piani di servizio (Core), e fare clic sul pulsante **freccia**.

    ![Misuratori e piani di servizio](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Nella pagina 3 fare clic su **Toggle All** (Attiva/disattiva tutte) per selezionare tutte le aree. In alternativa, è possibile selezionare manualmente le caselle di controllo per aree specifiche. Fare clic sul pulsante **freccia**.

    ![Scegliere i mercati](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. Nella pagina 4 controllare i tassi di cambio e fare clic su **Fine**. La procedura guidata ripristina i prezzi in base alle selezioni.

11. Nella scheda **PREZZI** fare clic su **VIEW SUMMARY AND CHANGES** (VISUALIZZA RIEPILOGO E MODIFICHE).
    Per **View Version** (Visualizza versione) selezionare **Draft** (Bozza) e per **Compare with** (Confronta con) selezionare **Production** (Produzione). Se non viene visualizzata alcuna differenza di prezzo, i prezzi sono stati ripristinati ai valori di produzione.

    ![Visualizza riepilogo e modifiche](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
13. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Ripristinare il modello di prezzi ai valori di produzione
Per ripristinare il modello dei prezzi ai valori di produzione, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).

2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **SKU**.
4. Fare clic sul pulsante **Modifica** per cambiare il modello di fatturazione. Nella finestra che si apre, selezionare o deselezionare la casella di controllo **Billing and licensing is done externally from Azure (aka Bring Your Own License)** (La fatturazione e le licenze sono gestite all'esterno di Azure (Bring Your Own License)).

    ![Modificare la fatturazione](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Seguire la procedura "Ripristinare il prezzo elencato ai valori di produzione" in questo articolo.
6. Passare alla scheda **PUBBLICA** e fare clic su **PUSH TO STAGING** (Passa a gestione temporanea). Per indicazioni dettagliate sul test dell'offerta nell'ambiente di gestione temporanea, vedere questo [Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).
7. Dopo aver testato l'offerta in gestione temporanea, vedere la scheda **PUBBLICA** nel portale di pubblicazione. Fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Ripristinare l'impostazione di visibilità di uno SKU elencato al valore di produzione
Per ripristinare l'impostazione di visibilità di uno SKU elencato al valore di produzione, seguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).

2. Passare alla scheda **Macchine virtuali** e selezionare l'offerta.
3. Nel menu a sinistra fare clic sulla scheda **SKU**.
4. Selezionare lo SKU e ripristinare l'impostazione di visibilità dello SKU al valore di produzione.

    ![Visibilità](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Dopo aver terminato le modifiche, fare clic su **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Richiedi approvazione per il push nella produzione) per ripubblicare l'offerta nel Marketplace.

## <a name="see-also"></a>Vedere anche
* [Come pubblicare e gestire un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)
* [Informazioni sui report sui proventi di Azure Marketplace](marketplace-publishing-report-payout.md)
* [Visualizzare e modificare il Cloud Solution Provider "Incentivi rivenditore" in Azure Marketplace](marketplace-publishing-csp-incentive.md)
* [Come risolvere i problemi comuni degli editori in Azure Marketplace](marketplace-publishing-support-common-issues.md)
* [Ottenere supporto come editore](marketplace-publishing-get-publisher-support.md)
* [Sviluppare l'immagine di una macchina virtuale in locale per Azure Marketplace](marketplace-publishing-vm-image-creation-on-premise.md)
* [Creare una macchina virtuale con Windows nel portale di anteprima di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
