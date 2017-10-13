---
title: Installare e configurare Ansible per l'uso con le macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come installare e configurare Ansible per la gestione delle risorse di Azure in Ubuntu, CentOS e SLES
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: 3fabfee169c3ebedc7bca81a6a6d9a44171daf20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Installare e configurare Ansible per gestire le macchine virtuali in Azure
Questo articolo descrive come installare Ansible e i moduli di Azure Python SDK necessari per alcune delle distribuzioni di Linux più comuni. È possibile installare Ansible in altre distribuzioni regolando i pacchetti installati per adattarli alla piattaforma specifica. Per creare le risorse di Azure in modo sicuro, si apprenderà anche come creare e definire le credenziali da usare con Ansible. 

Per altre opzioni di installazione e la procedura per altre piattaforme, vedere la [guida all'installazione di Ansible](https://docs.ansible.com/ansible/intro_installation.html).


## <a name="install-ansible"></a>Installare Ansible
Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAnsible* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

A questo punto, creare una macchina virtuale e installare Ansible per una delle distribuzioni seguenti:

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12.2 SP2](#sles-122-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
Creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). L'esempio seguente crea una macchina virtuale denominata *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH per la macchina virtuale con `publicIpAddress` indicato nell'output dall'operazione di creazione della macchina virtuale:

```bash
ssh azureuser@<publicIpAddress>
```

Nella macchina virtuale installare i pacchetti richiesti per i moduli di Azure Python SDK e Ansible come segue:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Passare ora a [Creare le credenziali di Azure](#create-azure-credentials).


### <a name="centos-73"></a>CentOS 7.3
Creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). L'esempio seguente crea una macchina virtuale denominata *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH per la macchina virtuale con `publicIpAddress` indicato nell'output dall'operazione di creazione della macchina virtuale:

```bash
ssh azureuser@<publicIpAddress>
```

Nella macchina virtuale installare i pacchetti richiesti per i moduli di Azure Python SDK e Ansible come segue:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Passare ora a [Creare le credenziali di Azure](#create-azure-credentials).


### <a name="sles-122-sp2"></a>SLES 12.2 SP2
Creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). L'esempio seguente crea una macchina virtuale denominata *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH per la macchina virtuale con `publicIpAddress` indicato nell'output dall'operazione di creazione della macchina virtuale:

```bash
ssh azureuser@<publicIpAddress>
```

Nella macchina virtuale installare i pacchetti richiesti per i moduli di Azure Python SDK e Ansible come segue:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 python-devel \
    libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Passare ora a [Creare le credenziali di Azure](#create-azure-credentials).


## <a name="create-azure-credentials"></a>Creare credenziali di Azure
Ansible comunica con Azure usando un nome utente e password o un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come Ansible. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente. Per migliorare la sicurezza indicare un nome utente e password, questo esempio crea un'entità servizio di base.

Creare un'entità servizio con [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) e generare l'output delle credenziali necessarie per Ansible:

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

Ecco un esempio di output dei comandi precedenti:

```json
[
  "eec5624a-90f8-4386-8a87-02730b5410d5",
  "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "72f988bf-86f1-41af-91ab-2d7cd011db47"
]
```

Per eseguire l'autenticazione in Azure, è anche necessario ottenere l'ID della sottoscrizione di Azure con [az account show](/cli/azure/account#show):

```azurecli
az account show --query [id] --output tsv
```

L'output di questi due comandi verrà usato nel passaggio successivo.


## <a name="create-ansible-credentials-file"></a>Creare un file di credenziali di Ansible
Per specificare le credenziali per Ansible, definire le variabili di ambiente o creare un file di credenziali locali. Per altre informazioni su come definire le credenziali di Ansible, vedere [Providing Credentials to Azure Modules](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules) (Fornire le credenziali ai moduli di Azure). 

Per un ambiente di sviluppo, creare un file di *credenziali* per Ansible sulla macchina virtuale host come segue:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Il file delle *credenziali* combina l'ID della sottoscrizione con l'output ottenuto dalla creazione di un'entità servizio. L'output del comando [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) precedente segue lo stesso ordine in base alle esigenze di *client_id*, *secret* e *tenant*. Il file delle *credenziali* seguente mostra questi valori che corrispodono a quelli dell'output precedente. Immettere valori personalizzati come di seguito:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>Usare le variabili di ambiente di Ansible
Se si intende usare strumenti quali Ansible Tower o Jenkins, è possibile definire variabili di ambiente come indicato di seguito. Queste variabili combinano l'ID della sottoscrizione con l'output della creazione di un'entità servizio. L'output del comando [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) precedente segue lo stesso ordine in base alle esigenze di *AZURE_CLIENT_ID*, *AZURE_SECRET* e *AZURE_TENANT*. 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto Ansible e i moduli necessari di Azure Python SDK sono stati installati e sono state definite le credenziali che Ansible dovrà usare. Informazioni su come [creare una macchina virtuale con Ansible](ansible-create-vm.md). È anche possibile capire come [creare una macchina virtuale di Azure completa e le risorse di supporto con Ansible](ansible-create-complete-vm.md).