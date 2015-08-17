<properties
	pageTitle="Come aggiornare l'agente Linux di Azure alla versione più recente da Github"
	description="Informazioni su come aggiornare l'agente Linux di Azure da Github per VM Linux in Azure."
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
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# Come aggiornare l'agente Linux di Azure per la versione più recente da Github

Per aggiornare il [agente Linux di Azure](https://github.com/Azure/WALinuxAgent), è necessario avere già:

1. una macchina virtuale Linux in esecuzione in Azure
2. Si è connessi a VM Linux tramite SSH

> [AZURE.NOTE]Se questa attività verrà effettuata da un computer Windows, è possibile utilizzare Putty per SSH al computer Linux. Per altre informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-linux-how-to-log-on.md).

Distribuzioni Linux da Azure inserite nei loro repository il pacchetto agente Linux di Azure, pertanto verificare e installare la versione più recente da quel repository Distro innanzitutto se possibile.

Per Ubuntu, digitare:
     
    #sudo apt-get install waagent

e su CentOS, digitare:

    #sudo yum install waagent

Per Oracle Linux, assicurarsi che il repository del componente aggiuntivo sia abilitato nel file `/etc/yum.repo.d/public-yum-ol6.repo` o `/etc/yum.repo.d/public-yum-ol7.repo`, quindi digitare:

    #sudo yum install WALinuxAgent

In genere è sufficiente, ma se per qualche motivo è necessario installarla da https://github.com direttamente, attenersi alla seguente procedura.


## 5\.2 Installare wget

Account di accesso alla macchina virtuale tramite SSH.

Installare wget (esistono alcune distribuzioni non installare il file per impostazione predefinita come versioni Redhat, CentOS e Oracle Linux 6.4 e 6.5) digitando `#sudo yum install wget` nella riga di comando.


## Scaricare la versione più recente

Aprire [la versione dell'agente Linux di Azure in Github](https://github.com/Azure/WALinuxAgent/releases) in una pagina web e scopri simili numero di versione più recente: 2.0.12. (È possibile individuare la versione corrente digitando `#waagent --version`.)

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

La riga seguente utilizza la versione 2.0.12 come esempio:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.12/waagent  

## Rendere eseguibile waagent

    #chmod +x waagent

## Copiare il nuovo eseguibile in /usr /sbin/Service
    
    #sudo cp waagent /usr/sbin

Per CoreOS, utilizzare:

    #sudo cp waagent /usr/share/oem/bin/
 
## Riavviare il servizio waagent

La maggior parte delle distribuzioni di linux:

    #sudo service waagent restart

Per Ubuntu, utilizzare:

    #sudo service walinuxagent restart

Per CoreOS, utilizzare:

    #sudo systemctl restart waagent 

## Verificare la versione dell'agente Linux di Azure
   
    #waagent -version

Per CoreOS, di sopra di comando potrebbe non funzionare.

Verrà visualizzata che la versione dell'agente Linux è stata aggiornata alla nuova versione.

Per ulteriori informazioni relative all'agente Linux di Azure, visitare [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).



 

<!---HONumber=August15_HO6-->