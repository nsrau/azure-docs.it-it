---
title: Installare e configurare Ansible per l'uso con le macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come installare e configurare Ansible per la gestione delle risorse di Azure in Ubuntu, CentOS e SLES
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: iainfou
ms.openlocfilehash: c00ebcb771081f8e35c67bf384f5f6822e16f268
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652992"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Installare e configurare Ansible per gestire le macchine virtuali in Azure

Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire le macchine virtuali in Azure, così come si farebbe con qualsiasi altra risorsa. Questo articolo descrive come installare Ansible e i moduli di Azure Python SDK necessari per alcune delle distribuzioni di Linux più comuni. È possibile installare Ansible in altre distribuzioni regolando i pacchetti installati per adattarli alla piattaforma specifica. Per creare le risorse di Azure in modo sicuro, si apprenderà anche come creare e definire le credenziali da usare con Ansible.

Per altre opzioni di installazione e la procedura per altre piattaforme, vedere la [guida all'installazione di Ansible](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.30 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Installare Ansible

Uno dei modi più semplici per usare Ansible con Azure è con Azure Cloud Shell, un'esperienza shell basata sul browser per gestire e sviluppare le risorse di Azure. Poiché Ansible è preinstallato in Cloud Shell, è possibile ignorare le istruzioni di installazione di Ansible e passare a [Creare credenziali di Azure](#create-azure-credentials). Per un elenco di strumenti aggiuntivi disponibili in Cloud Shell, vedere [Strumenti e funzionalità per Bash in Azure Cloud Shell](../../cloud-shell/features.md#tools).

Le istruzioni seguenti illustrano come creare una VM Linux per distribuzioni diverse e quindi installare Ansible. Se non è necessario creare una VM Linux, ignorare il primo passaggio per creare un gruppo di risorse di Azure. Se è necessario creare una VM, creare prima un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

A questo punto, selezionare una delle distribuzioni seguenti per i passaggi su come creare una macchina virtuale, se necessario, e quindi installare Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Se necessario, creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
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

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Se necessario, creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
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

### <a name="sles-12-sp2"></a>SLES 12 SP2

Se necessario, creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
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
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Passare ora a [Creare le credenziali di Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Creare credenziali di Azure

Ansible comunica con Azure usando un nome utente e password o un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come Ansible. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente. Per migliorare la sicurezza rispetto alla semplice immissione di nome utente e password, questo esempio crea un'entità servizio di base.

Nel computer host o in Azure Cloud Shell creare un'entità servizio usando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Le credenziali necessarie per Ansible vengono visualizzate sullo schermo:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Ecco un esempio di output dei comandi precedenti:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Per eseguire l'autenticazione in Azure, è anche necessario ottenere l'ID della sottoscrizione di Azure con [az account show](/cli/azure/account#az-account-show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

L'output di questi due comandi verrà usato nel passaggio successivo.

## <a name="create-ansible-credentials-file"></a>Creare un file di credenziali di Ansible

Per specificare le credenziali per Ansible, definire le variabili di ambiente o creare un file di credenziali locali. Per altre informazioni su come definire le credenziali di Ansible, vedere [Providing Credentials to Azure Modules](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules) (Fornire le credenziali ai moduli di Azure).

Per un ambiente di sviluppo, creare un file di *credenziali* per Ansible sulla macchina virtuale host. Creare un file di credenziali nella macchina virtuale in cui si è installato Ansible in un passaggio precedente:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Il file delle *credenziali* combina l'ID della sottoscrizione con l'output ottenuto dalla creazione di un'entità servizio. L'output del comando [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) precedente è lo stesso necessario per *client_id*, *secret* e *tenant*. Il file delle *credenziali* seguente mostra questi valori che corrispodono a quelli dell'output precedente. Immettere valori personalizzati come di seguito:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Salvare e chiudere il file.

## <a name="use-ansible-environment-variables"></a>Usare le variabili di ambiente di Ansible

Se si intende usare strumenti quali Ansible Tower o Jenkins, è necessario definire le variabili di ambiente. Questo passaggio può essere ignorato se si intende usare solo il client Ansible e il file di credenziali di Azure creato nel passaggio precedente. Le variabili di ambiente definiscono le stesse informazioni del file di credenziali di Azure:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto Ansible e i moduli necessari di Azure Python SDK sono stati installati e sono state definite le credenziali che Ansible dovrà usare. Informazioni su come [creare una macchina virtuale con Ansible](ansible-create-vm.md). È anche possibile capire come [creare una macchina virtuale di Azure completa e le risorse di supporto con Ansible](ansible-create-complete-vm.md).
