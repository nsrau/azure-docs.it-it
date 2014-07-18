<properties  linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn how to log on to a virtual machine running Windows Server 2008 R2 by using the Azure Management Portal." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

# Come accedere a una macchina virtuale che esegue Windows Server

Per avviare una Connessione Desktop remoto per una macchina virtuale che esegue il sistema operativo Windows Server, viene utilizzato il pulsante Connect del portale di gestione.

1.  Accedere al [portale di gestione di Azure][1] se questa operazione non è già stata eseguita.

2.  Fare clic su **Virtual Machines** e quindi selezionare la macchina virtuale appropriata.

3.  Nella barra dei comandi fare clic su **Connect**.
    
    ![Accesso alla macchina virtuale](./media/virtual-machines-log-on-windows-server/connectwindows.png)

4.  Fare clic su **Open** per utilizzare il file di protocollo di Desktop remoto creato automaticamente per la macchina virtuale.

5.  Fare clic su **Connect** per procedere con la connessione.
    
    ![Procedere alla connessione](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

6.  Digitare il nome utente e la password dell'account amministrativo della macchina virtuale, quindi fare clic su **OK**.

7.  Fare clic su **Yes** per verificare l'identità della macchina virtuale.
    
    ![Verificare l'identità della macchina virtuale](./media/virtual-machines-log-on-windows-server/connectverify.png)
    
    È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.



[1]: http://manage.windowsazure.com