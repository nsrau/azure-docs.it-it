---
title: Usare cloud-init per personalizzare una macchina virtuale Linux | Documentazione Microsoft
description: Come usare cloud-init per personalizzare una VM Linux durante la creazione con l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Usare cloud-init per personalizzare una macchina virtuale Linux in Azure
Questo articolo illustra come usare [cloud-init](https://cloudinit.readthedocs.io) per impostare il nome host, aggiornare i pacchetti e gestire gli account utente in una macchina virtuale (VM) in Azure. Questi script cloud init vengono eseguiti all'avvio quando si crea una macchina virtuale con l'interfaccia della riga di comando di Azure 2.0. Per una panoramica più approfondita su come installare applicazioni, scrivere file di configurazione e inserire chiavi da Key Vault, vedere [questa esercitazione](tutorial-automate-vm-deployment.md). È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](using-cloud-init-nodejs.md).


## <a name="cloud-init-overview"></a>Panoramica di cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) è un approccio diffuso per personalizzare una macchina virtuale Linux al primo avvio. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Quando cloud-init viene eseguito durante il processo di avvio iniziale non vi sono altri passaggi o agenti necessari per applicare la configurazione.

Cloud-init funziona anche fra distribuzioni. Ad esempio, non si usa **apt-get install** o **yum install** per installare un pacchetto. In alternativa, è possibile definire un elenco di pacchetti da installare. Cloud-init userà automaticamente lo strumento di gestione del pacchetto nativo per la distribuzione selezionata.

Microsoft collabora con i partner per promuovere l'inclusione e il funzionamento di cloud-init con le immagini da essi fornite per Azure. La tabella seguente illustra l'attuale disponibilità di cloud-init nelle immagini della piattaforma Azure:

| Alias | Autore | Offerta | SKU | Versione |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-LTS |più recenti |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |più recenti |
| CoreOS |CoreOS |CoreOS |Stabile |più recenti |


## <a name="set-the-hostname-with-cloud-init"></a>Impostare il nome host con cloud-init
I file cloud-init sono scritti in [YAML](http://www.yaml.org). Per eseguire uno script cloud-init quando si crea una VM in Azure con [az vm create](/cli/azure/vm#create), specificare il file cloud-init con l'opzione `--custom-data`. Verranno esaminati alcuni esempi di ciò che è possibile configurare con un file cloud-init. Uno scenario comune consiste nell'impostare il nome host di una macchina virtuale. Per impostazione predefinita, il nome host corrisponde al nome della VM. 

Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea il gruppo di risorse denominato *myResourceGroup* nella località *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Nella shell corrente creare un file denominato *cloud_init_hostname.txt* e incollare la configurazione seguente. Ad esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. In Cloud Shell immettere `sensible-editor cloud_init_hostname.txt` per creare il file e visualizzare un elenco degli editor disponibili. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga:

```yaml
#cloud-config
hostname: myhostname
```

Creare ora una VM con [az vm create](/cli/azure/vm#create) e specificare il file cloud-init con `--custom-data cloud_init_hostname.txt` come segue:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

Dopo la creazione della VM, l'interfaccia della riga di comando di Azure visualizza le relative informazioni. Usare `publicIpAddress` per stabilire una connessione SSH alla VM. Immettere il proprio indirizzo come di seguito:

```bash
ssh azureuser@publicIpAddress
```

Per visualizzare il nome della VM, usare il comando `hostname` come segue:

```bash
hostname
```

La VM segnalerà il nome host come il valore impostato nel file cloud-init, come illustrato nell'output di esempio seguente:

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>Aggiornare una macchina virtuale con cloud init
Per motivi di sicurezza, potrebbe essere necessario configurare una VM per applicare gli aggiornamenti più recenti al primo avvio. Poiché cloud-init funziona in distribuzioni Linux diverse, non è necessario specificare `apt` o `yum` per la gestione pacchetti. È invece necessario definire `package_upgrade` e consentire al processo cloud-init di determinare il meccanismo appropriato per la distribuzione in uso. Questo flusso di lavoro consente di usare gli stessi script cloud-init nelle distribuzioni.

Per verificare il funzionamento del processo di aggiornamento, creare un file cloud-init denominato *cloud_init_upgrade.txt* e incollare la configurazione seguente:

```yaml
#cloud-config
package_upgrade: true
```

Creare ora una VM con [az vm create](/cli/azure/vm#create) e specificare il file cloud-init con `--custom-data cloud_init_upgrade.txt` come segue:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

Stabilire una connessione SSH all'indirizzo IP pubblico della VM visualizzata nell'output del comando precedente. Immettere il proprio indirizzo IP pubblico come di seguito:

```bash
ssh azureuser@publicIpAddress
```

Eseguire lo strumento di gestione pacchetti e cercare gli aggiornamenti. L'esempio seguente usa `apt-get` in una VM Ubuntu:

```bash
sudo apt-get upgrade
```

Poiché cloud-init ha cercato e installato gli aggiornamenti all'avvio, non sono presenti aggiornamenti da applicare, come illustrato nell'output di esempio seguente:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Aggiungere un utente a una macchina virtuale con cloud init
Una delle prime attività eseguite in qualsiasi nuova VM Linux è l'aggiunta di un utente per evitare di usare *root*. Le chiavi SSH sono la procedura consigliata per la sicurezza e l'usabilità. Le chiavi vengono aggiunte al file *~/.ssh/authorized_keys* con questo script cloud-init.

Per aggiungere un utente a una VM Linux, creare un file cloud-init denominato *cloud_init_add_user.txt* e incollare la configurazione seguente. Fornire la chiave pubblica (ad esempio il contenuto di *~/.ssh/id_rsa.pub*) per *ssh-authorized-keys*:

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

Creare ora una VM con [az vm create](/cli/azure/vm#create) e specificare il file cloud-init con `--custom-data cloud_init_add_user.txt` come segue:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

Stabilire una connessione SSH all'indirizzo IP pubblico della VM visualizzata nell'output del comando precedente. Immettere il proprio indirizzo IP pubblico come di seguito:

```bash
ssh myadminuser@publicIpAddress
```

Per verificare che l'utente sia stato aggiunto alla VM e ai gruppi specificati, visualizzare il contenuto del file */etc/group* come segue:

```bash
cat /etc/group
```

L'output di esempio seguente illustra che l'utente dal file *cloud_init_add_user.txt* è stato aggiunto alla VM e al gruppo appropriato:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Passaggi successivi
Cloud-init è uno dei metodi standard per modificare le VM Linux in fase di avvio. In Azure è anche possibile usare le estensioni di VM per modificare la VM Linux all'avvio o quando è in esecuzione. È ad esempio possibile usare l'estensione di VM di Azure per eseguire uno script in una VM in esecuzione e non solo al primo avvio. Per altre informazioni sulle estensioni di VM, vedere [Estensioni della macchina virtuale e funzionalità](extensions-features.md). Per esempi di come usare l'estensione, vedere [Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess](using-vmaccess-extension.md).