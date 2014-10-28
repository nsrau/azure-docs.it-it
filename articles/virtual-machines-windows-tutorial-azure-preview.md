<properties linkid="virtual-machines-windows-tutorial-azure-preview" urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Create a virtual machine running Windows Server in the Azure Preview Portal" metaKeywords="Azure image gallery vm" description="Learn how to create an Azure virtual machine (VM) running Windows Server, using the VM Gallery in the Azure Preview Portal" metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="danlep,kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="danlep,kathydav"/>

# Creare una macchina virtuale che esegue Windows Server nel portale di anteprima di Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Portale di Azure</a><a href="/it-it/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Portale di anteprima di Azure</a></div>

Questa esercitazione illustra come creare una macchina virtuale di Azure che esegue Windows Server usando la raccolta di macchine virtuali presente nel portale di anteprima di Azure. Nella raccolta è disponibile un'ampia gamma di immagini, ad esempio immagini di sistemi operativi Windows, di sistemi operativi basati su Linux e di applicazioni.

> [WACOM.NOTE] Per completare questa esercitazione, non è necessario essere esperti di macchine virtuali di Azure. È invece richiesto un account di Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere [Creazione di un account Azure][Creazione di un account Azure].

In questa esercitazione si apprenderà come:

-   [Come creare la macchina virtuale][Come creare la macchina virtuale]
-   [Come accedere alla macchina virtuale dopo averla creata][Come accedere alla macchina virtuale dopo averla creata]

Per altre informazioni, vedere [Macchine virtuali][Macchine virtuali].

## <span id="createvirtualmachine"></span> </a>Come creare la macchina virtuale

Questa sezione mostra come usare il portale di anteprima per creare una macchina virtuale che esegue Windows Server. È possibile usare le impostazioni predefinite per quasi tutto il processo di configurazione e creare la macchina virtuale in pochi minuti.

1.  Accedere al [portale di anteprima di Azure][portale di anteprima di Azure]. Se non si dispone di una sottoscrizione, usare la [versione di valutazione gratuita][versione di valutazione gratuita].

2.  Nel menu Hub fare clic su **Nuovo**.

    ![Selezionare Nuovo nella barra dei comandi][Selezionare Nuovo nella barra dei comandi]

3.  In **Nuovo** fare clic su **Tutto**, quindi in **Raccolta** fare clic su **Macchine virtuali**. Fare clic su **Server 2012 R2 Datacenter**. In questa pagina fare clic su **Crea**.

    ![Selezionare un'immagine di macchina virtuale dalla raccolta][Selezionare un'immagine di macchina virtuale dalla raccolta]

4.  Nel pannello **Crea macchina virtuale** inserire il **Nome host** scelto per la macchina virtuale, il **Nome utente** amministrativo e una **Password** complessa.

    ![Configurare il nome host e le credenziali di accesso][Configurare il nome host e le credenziali di accesso]

    > [WACOM.NOTE] **Nome utente** indica l'account amministrativo da usare per la gestione del server. Creare una password univoca per questo account e annotarla per evitare di dimenticarla. **Il nome utente e la password serviranno per accedere alla macchina virtuale**.

5.  Per usare le impostazioni predefinite per le restanti opzioni della macchina virtuale e avviare il processo di creazione, fare clic su **Crea**. Se invece si preferisce, prima di fare clic su **Crea**, è possibile esplorare le impostazioni di **Configurazione facoltativa**. Ad esempio, è possibile configurare la diagnostica facoltativa per la macchina virtuale in modo da tenere traccia successivamente delle numerose metriche sulla macchina virtuale. Fare clic su **Configurazione facoltativa**, su **Diagnostica** e impostare lo **Stato** su **On**.

    ![Attivare la diagnostica per la macchina virtuale][Attivare la diagnostica per la macchina virtuale]

    > [WACOM.NOTE] Se si attiva Diagnostica Azure, Azure archivierà i dati di diagnostica in un account di archiviazione di Azure con conseguenti costi di archiviazione aggiuntivi.

6.  Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento in **Notifiche** nel menu Hub. Una volta creata, la macchina virtuale verrà visualizzata nella Schermata iniziale.

    ![Macchina virtuale visualizzata nella Schermata iniziale][Macchina virtuale visualizzata nella Schermata iniziale]

## <span id="logon"></span> </a>Come accedere alla macchina virtuale dopo averla creata

Questa sezione descrive come accedere alla macchina virtuale per gestirne le impostazioni e le applicazioni che verranno eseguite al suo interno.

> [WACOM.NOTE] Per i requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH][Connettersi a una macchina virtuale di Azure con RDP o SSH].

1.  Accedere al [portale di anteprima di Azure][portale di anteprima di Azure] se questa operazione non è già stata eseguita.

2.  Fare clic sulla macchina virtuale nella Schermata iniziale. Per trovarla, fare clic su **Sfoglia**, quindi su **Macchine virtuali**.

    ![Cercare la macchina virtuale][Cercare la macchina virtuale]

3.  Nel pannello della macchina virtuale fare clic su **Connetti** in alto.

    ![Accesso alla macchina virtuale][Accesso alla macchina virtuale]

4.  Fare clic su **Apri** per usare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale.

5.  Fare clic su **Connect** per procedere con la connessione.

    ![Procedere alla connessione][Procedere alla connessione]

6.  Digitare il nome utente e la password dell'account amministrativo della macchina virtuale, quindi fare clic su **OK**.

7.  Fare clic su **Yes** per verificare l'identità della macchina virtuale.

    ![Verificare l'identità della macchina virtuale][Verificare l'identità della macchina virtuale]

    È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

## Passaggi successivi

Per altre informazioni sulla configurazione di macchine virtuali Windows in Azure, vedere gli articoli seguenti:

[Come connettere macchine virtuali in un servizio cloud][Come connettere macchine virtuali in un servizio cloud]

[Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server][Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server]

[Connettere un disco dati a una macchina virtuale][Connettere un disco dati a una macchina virtuale]

[Gestione della disponibilità delle macchine virtuali][Gestione della disponibilità delle macchine virtuali]

  [Portale di Azure]: /it-it/documentation/articles/virtual-machines-windows-tutorial/ "Azure Portal"
  [Portale di anteprima di Azure]: /it-it/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure Preview Portal"
  [Creazione di un account Azure]: http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/
  [Come creare la macchina virtuale]: #createvirtualmachine
  [Come accedere alla macchina virtuale dopo averla creata]: #logon
  [Macchine virtuali]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [portale di anteprima di Azure]: https://portal.azure.com
  [versione di valutazione gratuita]: http://www.windowsazure.com/it-it/pricing/free-trial/
  [Selezionare Nuovo nella barra dei comandi]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [Selezionare un'immagine di macchina virtuale dalla raccolta]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [Configurare il nome host e le credenziali di accesso]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [Attivare la diagnostica per la macchina virtuale]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [Macchina virtuale visualizzata nella Schermata iniziale]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [Connettersi a una macchina virtuale di Azure con RDP o SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Cercare la macchina virtuale]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [Accesso alla macchina virtuale]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [Procedere alla connessione]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Verificare l'identità della macchina virtuale]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [Come connettere macchine virtuali in un servizio cloud]: http://www.windowsazure.com/it-it/documentation/articles/cloud-services-connect-virtual-machine/
  [Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server]: http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Connettere un disco dati a una macchina virtuale]: http://www.windowsazure.com/it-it/documentation/articles/storage-windows-attach-disk/
  [Gestione della disponibilità delle macchine virtuali]: http://www.windowsazure.com/it-it/documentation/articles/manage-availability-virtual-machines/
