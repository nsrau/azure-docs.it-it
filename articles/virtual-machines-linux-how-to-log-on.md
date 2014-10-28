<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Come accedere a una macchina virtuale che esegue Linux

Per accedere a una macchina virtuale in cui è in esecuzione il sistema operativo Linux, si usa un client SSH (Secure Shell).

Per accedere alla macchina virtuale, è necessario installare un client SSH nel computer che si intende usare. È disponibile un'ampia scelta di programmi client SSH. Di seguito vengono indicate alcune tra le scelte possibili:

-   In un computer che esegue un sistema operativo Windows, è possibile usare un client SSH come PuTTY. Per ulteriori informazioni, vedere la [pagina di download di PuTTY][pagina di download di PuTTY].
-   In un computer che esegue un sistema operativo Linux, è possibile usare un client SSH come OpenSSH. Per altre informazioni, vedere la pagina [OpenSSH][OpenSSH].

> [WACOM.NOTE] Per altri requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH][Connettersi a una macchina virtuale di Azure con RDP o SSH].

In questa procedura viene illustrato come utilizzare il programma PuTTY per accedere alla macchina virtuale.

1.  Individuare le informazioni **Host Name** e **Port information** nel [portale di gestione][portale di gestione]. È possibile rilevare le informazioni necessarie dal dashboard della macchina virtuale. Fare clic sul nome della macchina virtuale e individuare **SSH Details** nella sezione **Quick Glance** del dashboard.

    ![Ricerca dei dettagli SSH][Ricerca dei dettagli SSH]

2.  Aprire il programma PuTTY.

3.  Immettere in Host Name e Port information i dati raccolti dal dashboard, quindi fare clic su **Open**.

    ![Apertura di PuTTY][Apertura di PuTTY]

4.  Accedere alla macchina virtuale utilizzando l'account specificato durante la creazione del computer Il nome utente predefinito è azureuser.

    ![Accesso alla macchina virtuale][Accesso alla macchina virtuale]

> [WACOM.NOTE] Se si dimentica la chiave o la password SSH, è possibile reimpostarla mediante l'estensione VMAccess. Se si dimentica il nome utente, è possibile usare l'estensione per crearne uno nuovo con privilegi sudo. Per istruzioni, vedere l'articolo relativo all'[uso dell'estensione VMAccess per reimpostare le credenziali di accesso per la macchina virtuale di Linux][uso dell'estensione VMAccess per reimpostare le credenziali di accesso per la macchina virtuale di Linux].

È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

<!-- LINKS -->

  [pagina di download di PuTTY]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH]: http://www.openssh.org/
  [Connettersi a una macchina virtuale di Azure con RDP o SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [portale di gestione]: http://manage.windowsazure.com
  [Ricerca dei dettagli SSH]: ./media/virtual-machines-linux-how-to-log-on/sshdetails.png
  [Apertura di PuTTY]: ./media/virtual-machines-linux-how-to-log-on/putty.png
  [Accesso alla macchina virtuale]: ./media/virtual-machines-linux-how-to-log-on/sshlogin.png
  [uso dell'estensione VMAccess per reimpostare le credenziali di accesso per la macchina virtuale di Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138
