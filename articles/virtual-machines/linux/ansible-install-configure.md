---
title: Installare Ansible in macchine virtuali di Azure
description: Informazioni su come installare e configurare Ansible per la gestione delle risorse di Azure in Ubuntu, CentOS e SLES
ms.service: virtual-machines-linux
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 38a1ffdc815b357f7bb7ebe2c337b55a738fb6b5
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790433"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Installare Ansible in macchine virtuali di Azure

Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire le macchine virtuali in Azure, così come si farebbe con qualsiasi altra risorsa. Questo articolo descrive come installare Ansible e i moduli di Azure Python SDK necessari per alcune delle distribuzioni di Linux più comuni. È possibile installare Ansible in altre distribuzioni regolando i pacchetti installati per adattarli alla piattaforma specifica. Per creare le risorse di Azure in modo sicuro, si apprenderà anche come creare e definire le credenziali da usare con Ansible. Per un elenco degli strumenti aggiuntivi disponibili in Cloud Shell, vedere [Strumenti e funzionalità per Bash in Azure Cloud Shell](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: se non si possiede una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Accesso a Linux o a una macchina virtuale Linux**: in assenza di un computer Linux, creare una [macchina virtuale Linux](https://docs.microsoft.com/azure/virtual-network/quick-create-cli).

- **Entità servizio di Azure**: seguire le indicazioni riportate nella sezione relativa a **Creare l'entità servizio** nell'articolo [Creare entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Prendere nota dei valori per **appId**, **displayName**, **password** e **tenant**.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Installare Ansible in una macchina virtuale Linux di Azure

Accedere al computer Linux e selezionare una delle distribuzioni seguenti per informazioni sui passaggi per l'installazione di Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Installare i pacchetti necessari per i moduli Azure Python SDK e Ansible immettendo i comandi seguenti in una finestra del terminale o di Bash:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Seguire le istruzioni presentate nella sezione [Creare credenziali di Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Installare i pacchetti necessari per i moduli Azure Python SDK e Ansible immettendo i comandi seguenti in una finestra del terminale o di Bash:


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Seguire le istruzioni presentate nella sezione [Creare credenziali di Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Installare i pacchetti necessari per i moduli Azure Python SDK e Ansible immettendo i comandi seguenti in una finestra del terminale o di Bash:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Seguire le istruzioni presentate nella sezione [Creare credenziali di Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Creare credenziali di Azure

La combinazione dell'ID sottoscrizione e delle informazioni restituite dalla creazione dell'entità servizio viene usata per configurare le credenziali di Ansible in uno dei due modi seguenti:

- [Creare un file di credenziali di Ansible](#file-credentials)
- [Usare variabili di ambiente di Ansible](#env-credentials)

Se si intende usare strumenti come Ansible Tower o Jenkins, sarà necessario usare l'opzione di dichiarazione dei valori dell'entità servizio come variabili di ambiente.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Creare un file di credenziali di Ansible

Questa sezione descrive come creare un file di credenziali locale per fornire credenziali ad Ansible. Per altre informazioni su come definire le credenziali di Ansible, vedere [Providing Credentials to Azure Modules](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules) (Fornire le credenziali ai moduli di Azure).

Per un ambiente di sviluppo, creare un file di *credenziali* per Ansible nella macchina virtuale host in questo modo:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Inserire le righe seguenti nel file di *credenziali*, sostituendo i segnaposto con le informazioni ottenute dalla creazione dell'entità servizio.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Salvare e chiudere il file.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Usare variabili di ambiente di Ansible

Questa sezione descrive come configurare le credenziali di Ansible esportandole come variabili di ambiente.

In una finestra del terminale o di Bash immettere i comandi seguenti:

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>Verificare la configurazione
Per verificare la configurazione, è ora possibile usare Ansible per creare un gruppo di risorse.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Usare Ansible per creare una macchina virtuale Linux in Azure](./ansible-create-vm.md)
