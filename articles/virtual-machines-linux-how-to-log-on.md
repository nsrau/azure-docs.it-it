<properties urlDisplayName="Log on to a VM" pageTitle="Accedere a una macchina virtuale che esegue Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />




#Come accedere a una macchina virtuale che esegue Linux #

Per accedere a una macchina virtuale in cui è in esecuzione il sistema operativo Linux, si usa un client SSH (Secure Shell).

Per accedere alla macchina virtuale, è necessario installare un client SSH nel computer che si intende usare. È disponibile un'ampia scelta di programmi client SSH. Di seguito vengono indicate alcune tra le scelte possibili:

- In un computer che esegue un sistema operativo Windows, è possibile usare un client SSH come PuTTY. Per altre informazioni, vedere la [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- In un computer che esegue un sistema operativo Linux, è possibile usare un client SSH come OpenSSH. Per altre informazioni, vedere la pagina [OpenSSH](http://www.openssh.org/).

>[WACOM.NOTE] Per informazioni sui requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294). 

In questa procedura viene illustrato come usare il programma PuTTY per accedere alla macchina virtuale.

1. Individuare le informazioni **Host Name** e **Port information** nel [portale di gestione](http://manage.windowsazure.com). È possibile rilevare le informazioni necessarie dal dashboard della macchina virtuale. Fare clic sul nome della macchina virtuale e individuare **SSH Details** nella sezione **Quick Glance** del dashboard.

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Aprire il programma PuTTY.

3. Immettere in Host Name e Port information i dati raccolti dal dashboard, quindi fare clic su **Open**.

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Accedere alla macchina virtuale usando l'account specificato durante la creazione del computer Il nome utente predefinito è azureuser.

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[WACOM.NOTE] Se si dimentica la chiave o la password SSH, è possibile reimpostarla mediante l'estensione VMAccess. Se si dimentica il nome utente, è possibile usare l'estensione per crearne uno nuovo con privilegi sudo. Per istruzioni, vedere [Come reimpostare una password o SSH per le macchine virtuali Linux]. 
	
È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

<!-- LINKS -->
[Come reimpostare una password o SSH per le macchine virtuali Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!--HONumber=35.1-->
