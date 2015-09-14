<properties
	pageTitle="Uso di Desktop remoto tramite xrdp per connettersi a una macchina virtuale Linux di Microsoft Azure"
	description="Informazioni sull'installazione e la configurazione di Desktop remoto in una macchina virtuale Linux di Microsoft Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="mingzhan"/>


#Uso di Desktop remoto tramite xrdp per connettersi a una macchina virtuale Linux di Microsoft Azure

##Panoramica

Il protocollo RDP (Remote Desktop Protocol) è un protocollo proprietario usato per Windows, come è possibile usare RDP per connettersi a macchine virtuali Linux in remoto?

L'articolo seguente risponderà a questa domanda. Consentirà di installare e configurare xrdp su macchine virtuali Linux di Microsoft Azure e di connettersi con Desktop remoto da un computer Windows.

Xrdp è un server RDP open source, che consente di connettere il server Linux con Desktop remoto da un computer Windows. Funziona molto meglio di VNC (Virtual Network Computing). VNC ha fama di qualità "JPEG" e di lentezza di funzionamento, mentre RDP è veloce e nitido.
 

> [AZURE.NOTE]È necessario disporre di una macchina virtuale di Microsoft Azure che esegue Linux. Vedere l'[esercitazione relativa alle macchine virtuali Linux di Azure](virtual-machines-linux-tutorial.md) per creare e impostare una macchina virtuale Linux.


##Creare endpoint per Desktop remoto
In questo documento verrà usato l'endpoint predefinito 3389 per Desktop remoto. Quindi, configurare l'endpoint 3389 come Desktop remoto per la macchina virtuale Linux come segue:


![immagine](./media/virtual-machines-linux-remote-desktop/no1.png)


Se non si è al corrente di come configurare endpoint per la macchina virtuale, vedere [queste indicazioni](virtual-machines-set-up-endpoints.md).


##Installare Gnome Desktop

Connettersi alla macchina virtuale Linux tramite putty e installare `Gnome Desktop`.

Per la famiglia Red Hat Linux, usare:

	#sudo yum install gnome* "xorg*" -y

Per Debian e Ubuntu, usare:

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


Per OpenSUSE, usare:

	#sudo zypper -y install gnome-session


##Installare xrdp

Per la famiglia Red Hat Linux, è necessario per prima cosa aggiungere il repository EPEL nella macchina virtuale Linux per installare il pacchetto xrdp tramite `yum`, usare:

	#sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
	#sudo yum -y install xrdp tigervnc-server tigervnc-server-module xterm

Per Debian e Ubuntu Linux, usare:

	#sudo apt-get install xrdp


Per OpenSUSE, usare:

> [AZURE.NOTE]Aggiornare la versione OpenSUSE con la versione in uso nel comando seguente. Di seguito è riportato un comando di esempio per `OpenSUSE 13.2`.

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##Avviare xrdp e impostare il servizio xdrp all'avvio

Per la famiglia Red Hat Linux, usare:

	#sudo service xrdp start
	#sudo chkconfig xrdp on


Per OpenSUSE, usare:

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp
 

##Disabilitare iptables se si usa la famiglia Red Hat Linux 

Usare:

	#sudo service iptables stop


##Uso di xfce se si usa una versione di Ubuntu successiva a Ubuntu 12.04LTS

Poiché attualmente xrop non supporta Gnome Desktop dalla versione di Ubuntu successiva a Ubuntu 12.04LTS, verrà usato Desktop `xfce`.

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
In un computer Windows avviare il client desktop remoto, immettere il nome DNS della macchina virtuale Linux oppure passare al `Dashboard` della macchina virtuale nel portale di Azure e fare clic su `Connect`, verrà visualizzata la finestra di accesso seguente:

![immagine](./media/virtual-machines-linux-remote-desktop/no2.png)

Accedere con `user` e `password` per la macchina Linux e sarà possibile usare subito Desktop remoto dalla macchina virtuale Linux di Microsoft Azure.


##Avanti
Per altre informazioni sull'uso di xrdp, vedere [questa pagina](http://www.xrdp.org/).





 

<!---HONumber=September15_HO1-->