---
title: Preparare l'immagine di macchina virtuale di Azure per l'uso con cloud-init
description: Come preparare un'immagine di macchina virtuale di Azure preesistente per la distribuzione con cloud-init
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: a75bceebe584522ee999f86664b8afb9fa00f17b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036740"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Preparare un'immagine di macchina virtuale Linux di Azure esistente da usare con cloud-init
Questo articolo descrive come preparare una macchina virtuale di Azure esistente per ridistribuirla e renderla pronta per l'uso di cloud-init. L'immagine risultante può essere usata per distribuire una nuova macchina virtuale o un nuovo set di scalabilità di macchine virtuali, ciascuno dei quali può quindi essere ulteriormente personalizzato tramite cloud-init in fase di distribuzione.  Questi script cloud-init vengono eseguiti al primo avvio dopo il provisioning delle risorse da parte di Azure. Per altre informazioni sul funzionamento di cloud-init in modo nativo in Azure e sulle distribuzioni Linux supportate, vedere la [panoramica di cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>prerequisiti
Questo documento presuppone già la presenza di una macchina virtuale di Azure in esecuzione con una versione supportata del sistema operativo Linux. La macchina è già stata configurata in base alle esigenze, sono stati installati tutti i moduli necessari, sono stati elaborati tutti gli aggiornamenti necessari e la macchina è stata testata per verificare che soddisfi i requisiti. 

## <a name="preparing-rhel-76--centos-76"></a>Preparazione di RHEL 7,6/CentOS 7,6
È necessario eseguire SSH nella macchina virtuale Linux ed eseguire i comandi seguenti per installare cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Aggiornare la sezione `cloud_init_modules` in `/etc/cloud/cloud.cfg` per includere i moduli seguenti:
```bash
- disk_setup
- mounts
```

Ecco un esempio dell'aspetto di una sezione `cloud_init_modules` per utilizzo generico.
```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```
Alcune attività correlate al provisioning e alla gestione di dischi temporanei devono essere aggiornate in `/etc/waagent.conf`. Eseguire i comandi seguenti per aggiornare le impostazioni appropriate. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Consentire solo Azure come origine dati per l'agente Linux di Azure creando un nuovo file `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` usando un editor di propria scelta con la riga seguente:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Se per l'immagine di Azure esistente è configurato un file di scambio e si vuole modificare la configurazione del file di scambio per nuove immagini con cloud-init, è necessario rimuovere il file di scambio esistente.

Per immagini basate su RedHat, seguire le istruzioni nel documento di RedHat seguente, che descrive come [rimuovere il file di scambio](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Per immagini CentOS con il file di scambio abilitato, è possibile eseguire il comando seguente per disattivare il file di scambio:
```bash
sudo swapoff /mnt/resource/swapfile
```

Assicurarsi che il riferimento al file di scambio venga rimosso da `/etc/fstab`. L'output dovrebbe essere simile al seguente:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Per risparmiare spazio e rimuovere il file di scambio, è possibile eseguire il comando seguente:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Passaggio aggiuntivo per l'immagine preparata per cloud-init
> [!NOTE]
> Se l'immagine era in precedenza un'immagine preparata e configurata per **cloud-init**, è necessario completare i passaggi seguenti.

I tre comandi seguenti vengono usati solo se in precedenza è stato effettuato il provisioning tramite cloud-init della macchina virtuale che si sta personalizzando come nuova immagine di origine specializzata.  NON è necessario eseguire questi passaggi se l'immagina è stata configurata usando l'agente Linux di Azure.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizzazione dell'impostazione dell'agente Linux 
In tutte le immagini della piattaforma Azure è installato l'agente Linux di Azure, indipendentemente dal fatto che sia stato configurato o meno tramite cloud-init.  Eseguire il comando seguente per completare il deprovisioning dell'utente dalla macchina Linux. 

```bash
sudo waagent -deprovision+user -force
```

Per altre informazioni sui comandi di deprovisioning dell'agente Linux di Azure, vedere [Agente Linux di Azure](../extensions/agent-linux.md).

Uscire dalla sessione SSH, quindi eseguire i comandi AzureCLI seguenti dalla shell Bash per deallocare, generalizzare e creare una nuova immagine di macchina virtuale di Azure.  Sostituire `myResourceGroup` e `sourceVmName` con le informazioni appropriate in base alla macchina virtuale di origine.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi cloud-init di modifiche di configurazione, vedere i documenti seguenti:
 
- [Aggiungere un altro utente Linux a una VM](cloudinit-add-user.md)
- [Eseguire uno strumento di gestione di pacchetti per aggiornare pacchetti esistenti al primo avvio](cloudinit-update-vm.md)
- [Modificare il nome host locale della macchina virtuale](cloudinit-update-vm-hostname.md) 
- [Installare un pacchetto dell'applicazione, aggiornare i file di configurazione e inserire le chiavi](tutorial-automate-vm-deployment.md)
