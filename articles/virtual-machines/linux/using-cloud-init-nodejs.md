---
title: Uso di cloud-init per personalizzare una VM Linux durante la creazione in Azure | Documentazione Microsoft
description: Come usare cloud-init per personalizzare una VM Linux durante la creazione con l&quot;interfaccia della riga di comando di Azure 1.0
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0b6150bca333188666935b3c9aa02c4b33690db9
ms.lasthandoff: 04/03/2017


---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>Usare cloud-init per personalizzare una VM Linux durante la creazione con l'interfaccia della riga di comando di Azure 1.0
Questo articolo illustra come creare script cloud-init per impostare il nome host, aggiornare i pacchetti installati e gestire gli account utente.  Gli script cloud-init vengono richiamati durante la creazione della VM dall'interfaccia della riga di comando di Azure.  L'articolo richiede:

* Un account Azure. È possibile [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Accesso tramite `azure login` per l'[interfaccia della riga di comando di Azure](../../cli-install-nodejs.md).
* L'interfaccia della riga di comando di Azure *deve essere impostata obbligatoriamente* sulla modalità Azure Resource Manager `azure config mode arm`.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa

## <a name="quick-commands"></a>Comandi rapidi
Creare uno script cloud-init.txt che consente di impostare il nome host, aggiornare tutti i pacchetti e aggiungere un utente sudo per Linux.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Creare un gruppo di risorse in cui avviare le VM.

```azurecli
azure group create myResourceGroup westus
```

Creare una VM Linux che utilizza cloud-init per configurarla durante l'avvio.

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata
### <a name="introduction"></a>Introduzione
Quando si avvia una nuova VM Linux si ottiene un VM Linux standard senza alcuna personalizzazione né alcun adattamento. [Cloud-init](https://cloudinit.readthedocs.org) è un metodo standard che consente di inserire uno script o alcune impostazioni di configurazione in una VM Linux quando viene avviata per la prima volta.

In Azure è possibile apportare modifiche a una VM Linux in fase di distribuzione o avvio in tre modi diversi.

* Inserire gli script che usano cloud-init.
* Inserire gli script che usano l' [estensione VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)di Azure.
* Un modello di Azure che usa cloud-init.
* Un modello di Azure che usa [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per inserire script in qualsiasi momento dopo l'avvio:

* Esecuzione diretta di comandi tramite SSH
* Inserire gli script usando l' [estensione VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)di Azure, in modo imperativo o in un modello di Azure
* Strumenti per la gestione della configurazione come Ansible, Salt, Chef e Puppet.

> [!NOTE]
> : l'estensione VMAccess esegue uno script come radice nello stesso modo di SSH.  Tuttavia, l'utilizzo dell'estensione della VM consente di abilitare diverse funzionalità di da Azure potenzialmente utili a seconda dello scenario.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilità di cloud-init negli alias delle immagini a creazione rapida della VM di Azure:
| Alias | Autore | Offerta | SKU | Versione | Cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7,2 |più recenti |no |
| CoreOS |CoreOS |CoreOS |Stabile |più recenti |sì |
| Debian |credativ |Debian |8 |più recenti |no |
| openSUSE |SUSE |openSUSE |13.2 |più recenti |no |
| RHEL |Redhat |RHEL |7,2 |più recenti |no |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |più recenti |sì |

Microsoft collabora con i partner per promuovere l'inclusione di cloud-init e utilizza le immagini da essi fornite per Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Aggiunta di uno script cloud-init per la creazione della VM con l'interfaccia della riga di comando di Azure
Per avviare uno script cloud-init durante la creazione di una VM in Azure, specificare il file cloud-init tramite l'opzione `--custom-data` dell'interfaccia della riga di comando di Azure.

Creare un gruppo di risorse in cui avviare le VM.

```azurecli
azure group create myResourceGroup westus
```

Creare una VM Linux che utilizza cloud-init per configurarla durante l'avvio.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Creazione di uno script cloud-init per impostare il nome host di una VM Linux
Una delle impostazioni più semplici e più importanti per qualsiasi VM Linux è il nome host. È possibile definire facilmente tale impostazione tramite cloud-init con questo script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Script cloud-init di esempio denominato `cloud_config_hostname.txt`.
```sh
#cloud-config
hostname: myservername
```

Durante l'avvio iniziale della VM questo script cloud-init imposta il nome host su `myservername`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

Accedere e verificare il nome host della nuova VM.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Creazione di uno script cloud-init per aggiornare Linux
Per motivi di sicurezza, è consigliabile aggiornare la VM Ubuntu al primo avvio.  Con cloud-init è possibile eseguire questa operazione tramite lo script seguente, a seconda della distribuzione Linux in uso.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Script cloud-init `cloud_config_apt_upgrade.txt` di esempio per la famiglia Debian
```sh
#cloud-config
apt_upgrade: true
```

Una volta avviato Linux, tutti i pacchetti installati vengono aggiornati tramite `apt-get`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

Accedere e verificare che tutti i pacchetti siano aggiornati.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Creazione di uno script cloud-init per aggiungere un utente a Linux
Una delle prime attività eseguite in qualsiasi nuova VM Linux è l'aggiunta di un utente distinto da `root`, per evitare di usare quest'ultimo. Le chiavi SSH rappresentano la procedura consigliata per la protezione e l'usabilità e vengono aggiunte al file `~/.ssh/authorized_keys` con questo script cloud-init.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Script cloud-init `cloud_config_add_users.txt` di esempio per la famiglia Debian
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Una volta avviato Linux, tutti gli utenti elencati vengono creati e aggiunti al gruppo sudo.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

Accedere e verificare l'utente appena creato.

```bash
ssh myVM
cat /etc/group
```

Output

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Passaggi successivi
Cloud-init si sta affermando come metodo standard per modificare la VM Linux in fase di avvio. Azure offre inoltre estensioni VM che consentono di modificare la VM Linux in fase di avvio o durante l'esecuzione. Ad esempio, è possibile usare VMAccessExtension di Azure per reimpostare le informazioni SSH o dell'utente mentre la VM è in esecuzione. Con cloud-init, per reimpostare la password è necessario riavviare il computer.

[Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


