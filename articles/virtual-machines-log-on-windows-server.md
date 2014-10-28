<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn to use the Azure Management Portal to log on to a virtual machine running Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="08/05/2014" ms.author="kathydav"></tags>

# Come accedere a una macchina virtuale che esegue Windows Server

Per avviare una connessione Desktop remoto per una macchina virtuale che esegue il sistema operativo Windows Server, usare il pulsante Connetti del portale di gestione.

> [WACOM.NOTE] Se è necessario reimpostare il nome utente o la password, oppure abilitare RDP nella macchina virtuale, è possibile usare l'estensione [VMAccess][VMAccess]. Per informazioni sui requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH][Connettersi a una macchina virtuale di Azure con RDP o SSH].

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] se questa operazione non è già stata eseguita.

2.  Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale appropriata.

3.  Nella barra dei comandi fare clic su **Connetti**.

    ![Accesso alla macchina virtuale][Accesso alla macchina virtuale]

4.  Fare clic su **Apri** per usare il file di Remote Desktop Protocol creato automaticamente per la macchina virtuale.

5.  Fare clic su **Connetti** per continuare.

    ![Procedere alla connessione][Procedere alla connessione]

6.  Digitare il nome utente e la password dell'account amministrativo della macchina virtuale, quindi fare clic su **OK**. Il nome utente e la password devono corrispondere a quelli specificati al momento della creazione della macchina virtuale, a meno che tale macchina non sia ora un controller di dominio. In tal caso, digitare il nome utente e la password dell'account amministratore del dominio.

7.  Fare clic su **Sì** per verificare l'identità della macchina virtuale.

    ![Verificare l'identità della macchina virtuale][Verificare l'identità della macchina virtuale]

    È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

  [VMAccess]: http://go.microsoft.com/fwlink/p/?LinkId=396856
  [Connettersi a una macchina virtuale di Azure con RDP o SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Accesso alla macchina virtuale]: ./media/virtual-machines-log-on-windows-server/connectwindows.png
  [Procedere alla connessione]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Verificare l'identità della macchina virtuale]: ./media/virtual-machines-log-on-windows-server/connectverify.png
