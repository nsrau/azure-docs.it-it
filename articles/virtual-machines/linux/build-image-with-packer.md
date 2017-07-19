---
title: Come creare immagini di macchine virtuali di Linux in Azure con Packer | Microsoft Docs
description: Informazioni su come usare Packer per creare immagini di macchine virtuali di Linux in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fa30f7b9aebf3b9a3fb1e037983e8460aa76442e
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Come usare Packer per creare immagini di macchine virtuali di Linux in Azure
Ogni macchina virtuale (VM, Virtual Machine) in Azure viene creata a partire da un'immagine che ne definisce la distribuzione di Linux e la versione del sistema operativo. Le immagini possono includere applicazioni e configurazioni preinstallate. In Microsoft Azure Marketplace sono disponibili molte prime immagini e immagini di terze parti per gli ambienti applicativi e di distribuzione più diffusi. In alternativa, è possibile creare immagini personalizzate su misura per le proprie esigenze. Questo articolo illustra in dettaglio come definire e compilare immagini personalizzate in Azure tramite lo strumento open source [Packer](https://www.packer.io/).


## <a name="create-supporting-azure-resources"></a>Creare risorse di supporto di Azure
Durante il processo di compilazione della macchina virtuale di origine Packer crea risorse di Azure temporanee. Per acquisire la macchina virtuale di origine per usarla come immagine, è necessario definire un gruppo di risorse e un account di archiviazione, all'interno dei quali verrà archiviato l'output del processo di compilazione di Packer.

Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```

Creare quindi un account di archiviazione con [az storage account create](/cli/azure/storage/account#create). I nomi degli account di archiviazione devono essere univoci, avere una lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole. L'esempio seguente crea un account di archiviazione denominato *mystorageaccount*:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --sku Standard_LRS
```


## <a name="create-azure-credentials"></a>Creare credenziali di Azure
Per eseguire l'autenticazione con Azure, Packer usa un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come Packer. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente.

Creare un'entità servizio con [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) e generare l'output delle credenziali necessarie per Packer:

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

Ecco un esempio di output dei comandi precedenti:

```azurecli
"f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
"0e760437-bf34-4aad-9f8d-870be799c55d",
"72f988bf-86f1-41af-91ab-2d7cd011db47"
```

Per eseguire l'autenticazione in Azure, è anche necessario ottenere l'ID della sottoscrizione di Azure con [az account show](/cli/azure/account#show):

```azurecli
az account show --query [id] --output tsv
```

L'output di questi due comandi verrà usato nel passaggio successivo.


## <a name="define-packer-template"></a>Definire un modello di Packer
Per compilare immagini, è necessario creare un modello come file JSON. Nel modello si definiscono i compilatori e gli strumenti di provisioning che eseguono il processo di compilazione effettivo. Packer è dotato di uno [strumento di provisioning per Azure](https://www.packer.io/docs/builders/azure.html) che consente di definire risorse di Azure, ad esempio le credenziali delle entità servizio create nel passaggio precedente.

Creare un file con nome *ubuntu.json* e incollare al suo interno il contenuto seguente. Immettere valori personalizzati per i parametri seguenti:

| Parametro       | Origine |
|-----------------|----------------------------------------------------|
| *client_id*      | Prima riga di output da `az ad sp` create command - *appId* |
| *client_secret*  | Seconda riga di output da `az ad sp` create command - *password* |
| *tenant_id*      | Terza riga di output da `az ad sp` create command - *tenant* |
| *subscription_id* | Output del comando `az account show` |
| *storage_account* | Nome specificato in `az storage account create` |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04.0-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Questo modello compila un'immagine di Ubuntu 16.04 LTS, installa NGINX, quindi effettua il deprovisioning della macchina virtuale.

> [!NOTE]
> Se si espande questo modello per eseguire il provisioning delle credenziali utente, modificare il comando dello strumento di provisioning che effettua il deprovisioning dell'agente di Azure per leggere `-deprovision` anziché `deprovision+user`.
> Il flag `+user` rimuove tutti gli account utente dalla macchina virtuale di origine.


## <a name="build-packer-image"></a>Compilare l'immagine in Packer
Se Packer non è installato nel computer locale, [seguire le istruzioni di installazione di Packer](https://www.packer.io/docs/install/index.html).

Compilare l'immagine specificando il file del modello di Packer come segue:

```bash
./packer build ubuntu.json
```

Ecco un esempio di output dei comandi precedenti:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : engineering
==> azure-arm:  ->> task : image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '13.90.250.248'
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /tmp/packer-shell529418469
    azure-arm: Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
    azure-arm: Hit:2 http://azure.archive.ubuntu.com/ubuntu xenial InRelease
    azure-arm: Get:3 http://azure.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
    azure-arm: Get:4 http://azure.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
    [snip]
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd?se=2017-07-08T20%3A57%3A53Z&sig=yl1yl3I2gKnO0I%2B7paw%2FQzKT5dawf5i%2B
LPmATMt5ot4%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json?se=2017-07-08T20%3A57%3A53Z&sig=GB1iSl0hhw1ZYG4nl%2BCfR9WEaquCF
OEhNtKlvp%2B5TdE%3D&sp=r&sr=b&sv=2015-02-21
```


## <a name="create-azure-image"></a>Creare l'immagine di Azure
L'output dal processo di compilazione di Packer è un disco rigido virtuale nell'account di archiviazione specificato. Creare un'immagine di Azure da questo disco rigido virtuale con [az image create](/cli/azure/image#create) e specificare il percorso `OSDiskUri` indicato alla fine dell'output di compilazione di Packer. L'esempio seguente crea un'immagine denominata `myImage`:

```azurecli
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --os-type linux \
    --source https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
```

Questa immagine può essere usata per creare macchine virtuali nella sottoscrizione di Azure. La creazione di macchine virtuali non è limitata allo stesso gruppo di risorse dell'immagine di origine.


## <a name="create-vm-from-azure-image"></a>Creare una macchina virtuale da un'immagine di Azure
È ora possibile creare una macchina virtuale dall'immagine con [az vm create](/cli/azure/vm#create). Specificare l'immagine creata con il parametro `--image`. L'esempio seguente crea una macchina virtuale denominata *myVM* da *myImage* e genera chiavi SSH, se non esistono ancora:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

La creazione della macchina virtuale richiede alcuni minuti. Dopo aver creato la macchina virtuale, prendere nota dell'indirizzo `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Questo indirizzo viene usato per accedere al sito NGINX tramite un Web browser.

Per consentire al traffico Web di raggiungere la macchina virtuale, aprire la porta 80 da Internet con il comando [az vm open_port](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testare la macchina virtuale e NGINX
È ora possibile aprire un Web browser e immettere `http://publicIpAddress` bella barra degli indirizzi. Fornire il proprio indirizzo IP pubblico dal processo di creazione della macchina virtuale. La pagina NGINX predefinita viene visualizzata come illustrato nell'esempio seguente:

![Sito NGINX predefinito](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Passaggi successivi
In questo esempio Packer è stato usato per creare un'immagine di macchina virtuale con NGINX già installato. È possibile usare questa immagine di macchina virtuale insieme a flussi di lavoro di distribuzione esistenti, ad esempio per distribuire app in macchine virtuali create dall'immagine con Ansible, Chef o Puppet.

Per altri modelli di Packer di esempio per distribuzioni di Linux di altro tipo, vedere [questo repository di GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
