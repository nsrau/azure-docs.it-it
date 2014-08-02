<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="" solutions="" manager="" editor="" />

Come accedere a una macchina virtuale che esegue Linux
======================================================

Per accedere a una macchina virtuale in cui è in esecuzione il sistema operativo Linux si utilizza un client Secure Shell (SSH).

È necessario installare un client SSH nel computer che si intende utilizzare per accedere alla macchina virtuale. È disponibile un'ampia scelta di programmi client SSH. Di seguito vengono indicate alcune tra le scelte possibili:

-   Se si utilizza un computer che esegue un sistema operativo Windows, è possibile utilizzare un client SSH come PuTTY. Per ulteriori informazioni, vedere la [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
-   Se si utilizza un computer che esegue un sistema operativo Linux, è possibile utilizzare un client SSH come OpenSSH. Per ulteriori informazioni, vedere la pagina [OpenSSH](http://www.openssh.org/).

In questa procedura viene illustrato come utilizzare il programma PuTTY per accedere alla macchina virtuale.

1.  Individuare le informazioni **Host Name** e **Port information** nel [portale di gestione](http://manage.windowsazure.com). È possibile rilevare le informazioni necessarie dal dashboard della macchina virtuale. Fare clic sul nome della macchina virtuale e individuare **SSH Details** nella sezione **Quick Glance** del dashboard.

    ![Ricerca dei dettagli SSH](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2.  Aprire il programma PuTTY.

3.  Immettere in Host Name e Port information i dati raccolti dal dashboard, quindi fare clic su **Open**.

    ![Apertura di PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4.  Accedere alla macchina virtuale utilizzando l'account specificato durante la creazione del computer

    ![Accesso alla macchina virtuale](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

    È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.


