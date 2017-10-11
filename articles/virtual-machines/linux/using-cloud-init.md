---
title: Usare cloud-init per personalizzare una macchina virtuale Linux | Documentazione Microsoft
description: Come usare cloud-init per personalizzare una VM Linux durante la creazione con l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a7a6daad34525683579e25b9591ed28f2bf29c04
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017

---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation"></a>Usare cloud-init per personalizzare una VM Linux durante la creazione
Questo articolo illustra come creare script cloud-init per impostare il nome host, aggiornare i pacchetti installati e gestire gli account utente con l'interfaccia della riga di comando di Azure 2.0. Gli script cloud-init vengono richiamati durante la creazione di una macchina virtuale (VM) dall'interfaccia della riga di comando di Azure. Per una panoramica più approfondita sull'installazione di applicazioni, la scrittura di file di configurazione e l'inserimento di chiavi da Key Vault, vedere [questa esercitazione](tutorial-automate-vm-deployment.md). È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](using-cloud-init-nodejs.md).

## <a name="quick-commands"></a>Comandi rapidi
Creare uno script cloud-init.txt che consente di impostare il nome host, aggiornare tutti i pacchetti e aggiungere un utente sudo per Linux.

```yaml
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

Creare un gruppo di risorse per avviare le macchine virtuali con [az group create](/cli/azure/group#create). L'esempio seguente crea il gruppo di risorse denominato *myResourceGroup*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare una VM Linux con [az vm create](/cli/azure/vm#create) usando cloud-init per configurarla durante l'avvio con il parametro `--custom-data`.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata
Quando si avvia una nuova VM Linux si ottiene un VM Linux standard senza alcuna personalizzazione né alcun adattamento. [Cloud-init](https://cloudinit.readthedocs.org) è un metodo standard che consente di inserire uno script o alcune impostazioni di configurazione in una VM Linux quando viene avviata per la prima volta.

In Azure è possibile apportare modifiche a una VM Linux in fase di distribuzione o avvio in vari modi.

* Inserire gli script che usano cloud-init.
* Inserire gli script che usano l' [estensione VMAccess](using-vmaccess-extension.md)di Azure.
* Un modello di Azure che usa cloud-init.
* Un modello di Azure che usa [CustomScriptExtention](extensions-customscript.md).

Per inserire script in qualsiasi momento dopo l'avvio:

* Esecuzione diretta di comandi tramite SSH
* Inserire gli script usando l' [estensione VMAccess](using-vmaccess-extension.md)di Azure, in modo imperativo o in un modello di Azure
* Strumenti per la gestione della configurazione come Ansible, Salt, Chef e Puppet.

> [!NOTE]
> L'estensione VMAccess esegue uno script come radice nello stesso modo di SSH. Tuttavia, l'utilizzo dell'estensione della VM consente di abilitare diverse funzionalità di da Azure potenzialmente utili a seconda dello scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilità di cloud-init negli alias delle immagini a creazione rapida della VM di Azure:
| Alias | Autore | Offerta | SKU | Versione | Cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7,2 |più recenti |no |
| CoreOS |CoreOS |CoreOS |Stabile |più recenti |sì |
| Debian |credativ |Debian |8 |più recenti |no |
| openSUSE |SUSE |openSUSE |13.2 |più recenti |no |
| RHEL |Redhat |RHEL |7,2 |più recenti |no |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |più recenti |sì |

Microsoft collabora con i partner per promuovere l'inclusione e il funzionamento di cloud-init con le immagini da essi fornite per Azure.

## <a name="add-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Aggiungere uno script cloud-init per la creazione della VM con l'interfaccia della riga di comando di Azure
Per avviare uno script cloud-init durante la creazione di una VM in Azure, specificare il file cloud-init tramite l'opzione `--custom-data` dell'interfaccia della riga di comando di Azure.

Creare un gruppo di risorse per avviare le macchine virtuali con [az group create](/cli/azure/group#create). L'esempio seguente crea il gruppo di risorse denominato *myResourceGroup*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare una VM Linux con [az vm create](/cli/azure/vm#create) usando cloud-init per configurarla durante l'avvio.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

## <a name="create-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Creare uno script cloud-init per impostare il nome host di una VM Linux
Una delle impostazioni più semplici e più importanti per qualsiasi VM Linux è il nome host. È possibile definire facilmente tale impostazione tramite cloud-init con questo script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Script cloud-init di esempio denominato `cloud_config_hostname.txt`.
```yaml
#cloud-config
hostname: myservername
```

Durante l'avvio iniziale della VM questo script cloud-init imposta il nome host su *myservername*. Creare una VM Linux con [az vm create](/cli/azure/vm#create) usando cloud-init per configurarla durante l'avvio.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Accedere e verificare il nome host della nuova VM.

```bash
ssh myVM
hostname
myservername
```

## <a name="create-a-cloud-init-script"></a>Creare uno script cloud-init
Per motivi di sicurezza, è consigliabile aggiornare la VM Ubuntu al primo avvio. Con cloud-init è possibile eseguire questa operazione tramite lo script seguente, a seconda della distribuzione Linux in uso.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Script cloud-init `cloud_config_apt_upgrade.txt` di esempio per la famiglia Debian
```yaml
#cloud-config
apt_upgrade: true
```

Dopo l'avvio di Linux, tutti i pacchetti installati vengono aggiornati tramite **apt-get**. Creare una VM Linux con [az vm create](/cli/azure/vm#create) usando cloud-init per configurarla durante l'avvio.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
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

## <a name="create-a-cloud-init-script-to-add-a-user-to-linux"></a>Creare uno script cloud-init per aggiungere un utente a Linux
Una delle prime attività eseguite in qualsiasi nuova VM Linux è l'aggiunta di un utente distinto da *root* o per evitare di usare quest'ultimo. Le chiavi SSH rappresentano la procedura consigliata per la sicurezza e l'usabilità e vengono aggiunte al file *~/.ssh/authorized_keys* con questo script cloud-init.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Script cloud-init `cloud_config_add_users.txt` di esempio per la famiglia Debian
```yaml
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Una volta avviato Linux, tutti gli utenti elencati vengono creati e aggiunti al gruppo sudo. Creare una VM Linux con [az vm create](/cli/azure/vm#create) usando cloud-init per configurarla durante l'avvio.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
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

[Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](extensions-features.md)

[Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess](using-vmaccess-extension.md)
