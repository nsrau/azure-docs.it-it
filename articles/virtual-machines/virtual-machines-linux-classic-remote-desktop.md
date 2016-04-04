<properties
	pageTitle="Desktop remoto per una macchina virtuale Linux | Microsoft Azure"
	description="Informazioni sull'installazione e la configurazione di Desktop remoto per collegarsi a una macchina virtuale Linux di Microsoft Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="mingzhan"/>


#Uso di Desktop remoto per connettersi a una macchina virtuale Linux di Microsoft Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


##Panoramica

Il protocollo RDP (Remote Desktop Protocol) è un protocollo proprietario utilizzato per Windows. Come si può utilizzare RDP per connettersi a una VM (macchina virtuale) Linux in modalità remota?

L'articolo seguente risponderà a questa domanda. Consentirà di installare e configurare xrdp su macchine virtuali Linux di Microsoft Azure e di connettersi con Desktop remoto da un computer Windows. Utilizzeremo una macchina virtuale Linux che esegue Ubuntu o OpenSUSE come nell'esempio riportato in questa guida.

Xrdp è un server RDP open source, che consente di connettere il server Linux con Desktop remoto da un computer Windows. Funziona molto meglio di VNC (Virtual Network Computing). VNC ha fama di qualità "JPEG" e di lentezza di funzionamento, mentre RDP è veloce e nitido.


> [AZURE.NOTE] È necessario disporre di una macchina virtuale di Microsoft Azure che esegue Linux. Vedere l'[esercitazione relativa alle macchine virtuali Linux di Azure](virtual-machines-linux-cli-create.md) per creare e impostare una macchina virtuale Linux.


##Creare endpoint per Desktop remoto
In questo documento verrà usato l'endpoint predefinito 3389 per Desktop remoto. Quindi, configurare l'endpoint 3389 come Desktop remoto per la macchina virtuale Linux come segue:


![immagine](./media/virtual-machines-linux-classic-remote-desktop/no1.png)


Se non si è al corrente di come configurare endpoint per la macchina virtuale, vedere [queste indicazioni](virtual-machines-linux-classic-setup-endpoints.md).


##Installare Gnome Desktop

Connettersi alla macchina virtuale Linux tramite putty e installare `Gnome Desktop`.

Per Ubuntu, utilizzare:

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


Per OpenSUSE, usare:

	#sudo zypper install gnome-session

##Installare xrdp

Per Ubuntu, utilizzare:

	#sudo apt-get install xrdp

Per OpenSUSE, usare:

> [AZURE.NOTE] Aggiornare la versione OpenSUSE con la versione in uso nel comando seguente. Di seguito è riportato un comando di esempio per `OpenSUSE 13.2`.

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##Avviare xrdp e impostare il servizio xdrp all'avvio

Per OpenSUSE, usare:

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp

Per Ubuntu, xrdp verrà avviato e abilitato automaticamente al momento dell'avvio dopo l'installazione.

##Uso di xfce se si usa una versione di Ubuntu successiva a Ubuntu 12.04LTS

Poiché attualmente xrdp non supporta Gnome Desktop dalla versione di Ubuntu successiva a Ubuntu 12.04LTS, verrà usato Desktop `xfce`.

Installare `xfce`, usare:

    #sudo apt-get install xubuntu-desktop

Quindi abilitare `xfce`, usare:

    #echo xfce4-session >~/.xsession

Modificare il file di configurazione `/etc/xrdp/startwm.sh`, usare:

    #sudo vi /etc/xrdp/startwm.sh   

Aggiungere la riga `xfce4-session` prima della riga `/etc/X11/Xsession`.

Riavviare il servizio xrdp, usare:

    #sudo service xrdp restart


##Connettersi alla macchina virtuale Linux da un computer Windows
In un computer Windows avviare il client desktop remoto, immettere il nome DNS della VM Linux oppure passare al `Dashboard` della VM nel portale di Azure classico e fare clic su `Connect` per connettere la VM Linux, verrà visualizzata la finestra di accesso seguente:

![immagine](./media/virtual-machines-linux-classic-remote-desktop/no2.png)

Accedere con `user` e `password` per la macchina Linux e sarà possibile usare subito Desktop remoto dalla macchina virtuale Linux di Microsoft Azure.


##Avanti
Per altre informazioni sull'uso di xrdp, vedere [questa pagina](http://www.xrdp.org/).

<!---HONumber=AcomDC_0323_2016-->