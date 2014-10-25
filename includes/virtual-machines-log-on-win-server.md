<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn how to log on to a virtual machine running Windows Server 2008 R2 by using the Azure Management Portal." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] Per i requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH][Connettersi a una macchina virtuale di Azure con RDP o SSH].

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] se questa operazione non è già stata eseguita.

2.  Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale appropriata.

3.  Nella barra dei comandi fare clic su **Connect**.

    ![Accesso alla macchina virtuale][Accesso alla macchina virtuale]

4.  Fare clic su **Apri** per usare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale.

5.  Fare clic su **Connect** per procedere con la connessione.

    ![Procedere alla connessione][Procedere alla connessione]

6.  Digitare il nome utente e la password dell'account amministrativo della macchina virtuale, quindi fare clic su **OK**.

7.  Fare clic su **Yes** per verificare l'identità della macchina virtuale.

    ![Verificare l'identità della macchina virtuale][Verificare l'identità della macchina virtuale]

    È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

  [Connettersi a una macchina virtuale di Azure con RDP o SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Accesso alla macchina virtuale]: ./media/virtual-machines-log-on-win-server/connectwindows.png
  [Procedere alla connessione]: ./media/virtual-machines-log-on-win-server/connectpublisher.png
  [Verificare l'identità della macchina virtuale]: ./media/virtual-machines-log-on-win-server/connectverify.png
