---
title: Aggiornare l&quot;agente Linux di Azure da Github | Microsoft Docs
description: Informazioni su come aggiornare l&quot;agente Linux di Azure per macchine virtuali Linux in Azure all&quot;ultima versione da GitHub
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d35e70c1d50bd4cb2d47188585354aa3e3d740d4
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Come aggiornare l'agente Linux di Azure su una macchina virtuale per la versione più recente da GitHub
Per aggiornare l' [agente Linux di Azure](https://github.com/Azure/WALinuxAgent) , su una VM Linux in Azure è necessario avere già:

1. Una VM Linux in esecuzione in Azure.
2. Una connessione a tale VM Linux mediante SSH.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> Se questa attività verrà eseguita da un computer Windows, è possibile usare PuTTY per SSH nel computer Linux. Per altre informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Distribuzioni Linux da Azure inserite nei loro repository il pacchetto agente Linux di Azure, pertanto verificare e installare la versione più recente da quel repository Distro innanzitutto se possibile.  

Per Ubuntu, digitare:

```bash
sudo apt-get install walinuxagent
```

E in CentOS digitare:

```bash
sudo yum install waagent
```

Per Oracle Linux, verificare che il repository `Addons` sia abilitato. Scegliere di modificare il file `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux ) e la riga `enabled=0` in `enabled=1` sotto **[ol6_addons]** o **[ol7_addons]** in questo file.

Installare quindi la versione più recente dell'agente Linux di Azure e digitare:

```bash
sudo yum install WALinuxAgent
```

Se non si trova il repository del componente aggiuntivo, è possibile aggiungere le righe seguenti alla fine del file con estensione repo in base alla versione di Oracle Linux:

Per macchine virtuali Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Per macchine virtuali Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

Quindi digitare:

```bash
sudo yum update WALinuxAgent
```

In genere è sufficiente, ma se per qualche motivo è necessario installarla da https://github.com direttamente, attenersi alla seguente procedura.

## <a name="install-wget"></a>5.2 Installare wget
Account di accesso alla macchina virtuale tramite SSH.

Installare wget (esistono alcune distribuzioni non installare il file per impostazione predefinita come versioni Redhat, CentOS e Oracle Linux 6.4 e 6.5) digitando `#sudo yum install wget` nella riga di comando.

## <a name="download-the-latest-version"></a>Scaricare la versione più recente
Aprire [la versione dell’agente Linux di Azure in Github](https://github.com/Azure/WALinuxAgent/releases) in una pagina Web e trovare il numero di versione più recente. (È possibile individuare la versione corrente digitando `#waagent --version`.)

### <a name="for-version-20x-type"></a>Per la versione 2.0.x, digitare:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

La riga seguente utilizza la versione 2.0.14 come esempio:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>Per la versione 2.1.x o successiva, digitare:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

La riga seguente utilizza la versione 2.1.0 come esempio:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>Installare l'agente Linux di Azure
### <a name="for-version-20x-use"></a>Per la versione 2.0.x, usare:
Rendere waagent eseguibile:

```bash
chmod +x waagent
```

Copiare il nuovo eseguibile in /usr/sbin/.

Per la maggior parte delle distribuzioni Linux, usare:

```bash
sudo cp waagent /usr/sbin
```

Per CoreOS, usare:

```bash
sudo cp waagent /usr/share/oem/bin/
```

Se questa è la nuova installazione dell'agente Linux di Azure, eseguire:

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>Per la versione 2.1.x, usare:
Potrebbe essere necessario installare prima il pacchetto `setuptools`. Vedere [qui](https://pypi.python.org/pypi/setuptools). Quindi eseguire:

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>Riavviare il servizio waagent
La maggior parte delle distribuzioni di linux:

```bash
sudo service waagent restart
```

Per Ubuntu, utilizzare:

```bash
sudo service walinuxagent restart
```

Per CoreOS, usare:

```bash
sudo systemctl restart waagent
```

## <a name="confirm-the-azure-linux-agent-version"></a>Verificare la versione dell'agente Linux di Azure
    
```bash
waagent -version
```

Per CoreOS, il comando sopra riportato potrebbe non funzionare.

Si noterà che l'agente Linux di Azure è stato aggiornato alla nuova versione.

Per altre informazioni relative all'agente Linux di Azure, vedere [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).


