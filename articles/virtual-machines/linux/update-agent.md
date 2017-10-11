---
title: Aggiornare l'agente Linux di Azure da Github | Microsoft Docs
description: Informazioni su come aggiornare all'ultima versione l'agente Linux di Azure per macchine virtuali Linux in Azure da GitHub
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
ms.date: 08/02/2017
ms.author: mingzhan
ms.openlocfilehash: c79e37976a58ae5384b5856e0f7f258a773ef0fd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Come aggiornare l'agente Linux di Azure in una macchina virtuale

Per aggiornare l' [agente Linux di Azure](https://github.com/Azure/WALinuxAgent) , su una VM Linux in Azure è necessario avere già:

- Una VM Linux in esecuzione in Azure.
- Una connessione a tale VM Linux mediante SSH.

È sempre consigliabile cercare prima un pacchetto nel repository di distribuzione di Linux. È possibile che il pacchetto disponibile non sia la versione più recente; abilitando la funzione di aggiornamento automatico, si avrà la certezza di ottenere sempre la versione più recente dell'agente Linux. In caso di problemi durante l'installazione da Gestione pacchetti, è consigliabile rivolgersi al servizio di supporto del fornitore della distribuzione.

## <a name="updating-the-azure-linux-agent"></a>Aggiornamento dell'agente Linux di Azure

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Verificare la versione corrente del pacchetto

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Aggiornare la cache del pacchetto

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installare la versione più recente del pacchetto

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificare che la funzione di aggiornamento automatico sia abilitata

Per sapere se la funzione è abilitata:

```bash
cat /etc/waagent.conf
```

Trovare "AutoUpdate.Enabled". Se viene visualizzato questo output, la funzione è abilitata:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Per abilitarla, eseguire:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Riavviare il servizio waagent

#### <a name="restart-agent-for-1404"></a>Riavviare l'agente per 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Riavviare l'agente per 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Verificare la versione corrente del pacchetto

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Aggiornare la cache del pacchetto

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installare la versione più recente del pacchetto

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Abilitare l'aggiornamento automatico dell'agente
Per questa versione di Debian, che non ha una versione > = 2.0.16, la funzione di aggiornamento automatico non è disponibile. L'output del comando precedente consente di determinare se il pacchetto è aggiornato.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Verificare la versione corrente del pacchetto

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Aggiornare la cache del pacchetto

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installare la versione più recente del pacchetto

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Verificare che la funzione di aggiornamento automatico sia abilitata 

Per sapere se la funzione è abilitata:

```bash
cat /etc/waagent.conf
```

Trovare "AutoUpdate.Enabled". Se viene visualizzato questo output, la funzione è abilitata:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Per abilitarla, eseguire:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Riavviare il servizio waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>Redhat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Verificare la versione corrente del pacchetto

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verificare gli aggiornamenti disponibili

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Installare la versione più recente del pacchetto

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificare che la funzione di aggiornamento automatico sia abilitata 

Per sapere se la funzione è abilitata:

```bash
cat /etc/waagent.conf
```

Trovare "AutoUpdate.Enabled". Se viene visualizzato questo output, la funzione è abilitata:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Per abilitarla, eseguire:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Riavviare il servizio waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Verificare la versione corrente del pacchetto

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verificare gli aggiornamenti disponibili

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Installare la versione più recente del pacchetto

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificare che la funzione di aggiornamento automatico sia abilitata 

Per sapere se la funzione è abilitata:

```bash
cat /etc/waagent.conf
```

Trovare "AutoUpdate.Enabled". Se viene visualizzato questo output, la funzione è abilitata:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Per abilitarla, eseguire:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Riavviare il servizio waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Verificare la versione corrente del pacchetto

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verificare gli aggiornamenti disponibili

L'output sopra riportato consente di determinare se il pacchetto è aggiornato.

#### <a name="install-the-latest-package-version"></a>Installare la versione più recente del pacchetto

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificare che la funzione di aggiornamento automatico sia abilitata 

Per sapere se la funzione è abilitata:

```bash
cat /etc/waagent.conf
```

Trovare "AutoUpdate.Enabled". Se viene visualizzato questo output, la funzione è abilitata:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Per abilitarla, eseguire:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Riavviare il servizio waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Verificare la versione corrente del pacchetto

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verificare gli aggiornamenti disponibili

L'output sopra riportato consente di determinare se il pacchetto è aggiornato.

#### <a name="install-the-latest-package-version"></a>Installare la versione più recente del pacchetto

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificare che la funzione di aggiornamento automatico sia abilitata 

Per sapere se la funzione è abilitata:

```bash
cat /etc/waagent.conf
```

Trovare "AutoUpdate.Enabled". Se viene visualizzato questo output, la funzione è abilitata:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Per abilitarla, eseguire:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Riavviare il servizio waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 e 7

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


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aggiornare l'agente Linux se per la distribuzione non è presente alcun pacchetto agente

Installare wget (in alcune distribuzioni non viene installato per impostazione predefinita, ad esempio in Redhat, CentOS e Oracle Linux versione 6.4 e 6.5) digitando `sudo yum install wget` nella riga di comando.

### <a name="1-download-the-latest-version"></a>1. Scaricare la versione più recente
Aprire [la versione dell’agente Linux di Azure in Github](https://github.com/Azure/WALinuxAgent/releases) in una pagina Web e trovare il numero di versione più recente. (È possibile individuare la versione corrente digitando `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Per la versione 2.2.x o successiva, digitare:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip.zip
cd WALinuxAgent-2.2.x
```

La riga seguente usa la versione 2.2.0 come esempio:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Installare l'agente Linux di Azure

#### <a name="for-version-22x-use"></a>Per la versione 2.2.x, usare:
Potrebbe essere necessario installare prima il pacchetto `setuptools`. Vedere [qui](https://pypi.python.org/pypi/setuptools). Quindi eseguire:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificare che la funzione di aggiornamento automatico sia abilitata

Per sapere se la funzione è abilitata:

```bash
cat /etc/waagent.conf
```

Trovare "AutoUpdate.Enabled". Se viene visualizzato questo output, la funzione è abilitata:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Per abilitarla, eseguire:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Riavviare il servizio waagent
Per la maggior parte delle distribuzioni Linux:

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

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Verificare la versione dell'agente Linux di Azure
    
```bash
waagent -version
```

Per CoreOS, il comando sopra riportato potrebbe non funzionare.

Si noterà che l'agente Linux di Azure è stato aggiornato alla nuova versione.

Per altre informazioni relative all'agente Linux di Azure, vedere [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).