<properties
	pageTitle="Aggiornare l'agente Linux di Azure da Github | Microsoft Azure"
	description="Informazioni su come aggiornare l'agente Linux di Azure per VM Linux in Azure all’ultima versione da Github."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="mingzhan"/>


# Come aggiornare l'agente Linux di Azure su una macchina virtuale per la versione più recente da GitHub

Per aggiornare l'[agente Linux di Azure](https://github.com/Azure/WALinuxAgent), su una VM Linux in Azure è necessario avere già:

1. Una VM Linux in esecuzione in Azure.
2. Una connessione a tale VM Linux mediante SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Se questa attività verrà eseguita da un computer Windows, è possibile usare PuTTY per SSH nel computer Linux. Per altre informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-linux-mac-create-ssh-keys.md).

Distribuzioni Linux da Azure inserite nei loro repository il pacchetto agente Linux di Azure, pertanto verificare e installare la versione più recente da quel repository Distro innanzitutto se possibile.

Per Ubuntu, digitare:

    #sudo apt-get install walinuxagent

E in CentOS digitare:

    #sudo yum install waagent


Per Oracle Linux, verificare che il repository `Addons` sia abilitato. Scegliere di modificare il file `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e la riga `enabled=0` in `enabled=1` in **[ol6\_addons]** o **[ol7\_addons]** in questo file.

Installare quindi la versione più recente dell'agente Linux di Azure e digitare:


    #sudo yum install WALinuxAgent

Se non si trova il repository del componente aggiuntivo, è possibile aggiungere le righe seguenti alla fine del file con estensione repo in base alla versione di Oracle Linux:

Per macchine virtuali Oracle Linux 6:

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Per macchine virtuali Oracle Linux 7:

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

Quindi digitare:

    #sudo yum update WALinuxAgent

In genere è sufficiente, ma se per qualche motivo è necessario installarla da https://github.com direttamente, attenersi alla seguente procedura.


## 5\.2 Installare wget

Account di accesso alla macchina virtuale tramite SSH.

Installare wget (esistono alcune distribuzioni non installare il file per impostazione predefinita come versioni Redhat, CentOS e Oracle Linux 6.4 e 6.5) digitando `#sudo yum install wget` nella riga di comando.


## Scaricare la versione più recente

Aprire [la versione dell’agente Linux di Azure in Github](https://github.com/Azure/WALinuxAgent/releases) in una pagina Web e trovare il numero di versione più recente. (È possibile individuare la versione corrente digitando `#waagent --version`.)

### Per la versione 2.0.x, digitare:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   La riga seguente utilizza la versione 2.0.14 come esempio:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### Per la versione 2.1.x o successiva, digitare:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   La riga seguente utilizza la versione 2.1.0 come esempio:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## Installare l'agente Linux di Azure

### Per la versione 2.0.x, usare:

 Rendere waagent eseguibile:

    #chmod +x waagent

 Copiare il nuovo eseguibile in /usr/sbin/.

  Per la maggior parte delle distribuzioni Linux, usare:

    #sudo cp waagent /usr/sbin

  Per CoreOS, usare:

    #sudo cp waagent /usr/share/oem/bin/

  Se questa è la nuova installazione dell'agente Linux di Azure, eseguire:
 
    #sudo /usr/sbin/waagent -install -verbose

### Per la versione 2.1.x, usare:

Potrebbe essere necessario installare prima il pacchetto `setuptools`. Vedere [qui](https://pypi.python.org/pypi/setuptools). Quindi eseguire:

    #sudo python setup.py install

## Riavviare il servizio waagent

La maggior parte delle distribuzioni di linux:

    #sudo service waagent restart

Per Ubuntu, utilizzare:

    #sudo service walinuxagent restart

Per CoreOS, utilizzare:

    #sudo systemctl restart waagent

## Verificare la versione dell'agente Linux di Azure

    #waagent -version

Per CoreOS, il comando sopra riportato potrebbe non funzionare.

Si noterà che l'agente Linux di Azure è stato aggiornato alla nuova versione.

Per altre informazioni relative all'agente Linux di Azure, vedere [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).

<!---HONumber=AcomDC_0824_2016-->