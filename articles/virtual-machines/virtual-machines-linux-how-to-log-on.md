<properties
	pageTitle="Accedere a una macchina virtuale Linux in Azure | Microsoft Azure"
	description="Informazioni su come accedere a una macchina virtuale di Azure che esegue Linux tramite un client SSH (Secure Shell)."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="rasquill"/>


#Come accedere a una macchina virtuale che esegue Linux #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-linux-tutorial-portal-rm.md).

Per accedere alla macchina virtuale, è necessario installare un client SSH nel computer che si intende usare. È disponibile un'ampia scelta di programmi client SSH. Di seguito vengono indicate alcune tra le scelte possibili:

- In un computer che esegue un sistema operativo Windows, è possibile usare un client SSH come PuTTY. Per altre informazioni, vedere la [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Per una macchina virtuale che esegue un sistema operativo Linux, si utilizza un client Secure Shell (SSH) per l'accesso; è difficile pensare a una distribuzione in cui non sia già stato installato per impostazione predefinita. Vedere [Come usare SSH](virtual-machines-linux-use-ssh-key.md) per ulteriori informazioni su Linux.


>[AZURE.NOTE]Per informazioni sui requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

In questa procedura viene illustrato come usare il programma PuTTY per accedere alla macchina virtuale.

1. Individuare le informazioni **Host Name** e **Port information** nel [portale di gestione](http://manage.windowsazure.com). È possibile rilevare le informazioni necessarie dal dashboard della macchina virtuale. Fare clic sul nome della macchina virtuale e individuare **SSH Details** nella sezione **Quick Glance** del dashboard.

	![Ricerca dei dettagli SSH](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Aprire il programma PuTTY.

3. Immettere in Host Name e Port information i dati raccolti dal dashboard, quindi fare clic su **Open**.

	![Apertura di PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Accedere alla macchina virtuale usando l'account specificato durante la creazione del computer Per altre informazioni su come creare una macchina virtuale con un nome utente e una password, vedere [Creazione di una macchina virtuale che esegue Linux](virtual-machines-linux-tutorial.md).

	![Accesso alla macchina virtuale](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[AZURE.NOTE]Se si dimentica la chiave o la password SSH, è possibile reimpostarla mediante l'estensione VMAccess. Se si dimentica il nome utente, è possibile usare l'estensione per crearne uno nuovo con privilegi sudo. Per istruzioni, vedere [Come reimpostare una password o SSH per le macchine virtuali Linux].

È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

<!-- LINKS -->
[Come reimpostare una password o SSH per le macchine virtuali Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=AcomDC_1217_2015-->