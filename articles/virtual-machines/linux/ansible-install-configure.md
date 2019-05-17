---
title: Avvio rapido - Installare Ansible in macchine virtuali Linux in Azure | Microsoft Docs
description: Questo articolo di avvio rapido descrive come installare e configurare Ansible per gestire le risorse di Azure in Ubuntu, CentOS e SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 60cefe24feab9de4262e81eb1bc313aeadc7eb05
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409247"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Guida introduttiva: Installare Ansible in macchine virtuali Linux in Azure

Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. Questo articolo illustra come configurare Ansible per alcune delle distribuzioni Linux più comuni. Per installare Ansible in altre distribuzioni, modificare i pacchetti installati per adattarli alla piattaforma specifica. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Accesso a Linux o a una macchina virtuale Linux**: in assenza di un computer Linux, creare una [macchina virtuale Linux](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Installare Ansible in una macchina virtuale Linux di Azure

Accedere al computer Linux e selezionare una delle distribuzioni seguenti per informazioni sui passaggi per l'installazione di Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

In questa sezione si configura CentOS per l'uso di Ansible.

1. Aprire una finestra del terminale.

1. Immettere il comando seguente per installare i pacchetti necessari per i moduli di Azure Python SDK:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Immettere il comando seguente per installare i pacchetti Ansible necessari:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Creare le credenziali di Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

In questa sezione si configura Ubuntu per l'uso di Ansible.

1. Aprire una finestra del terminale.

1. Immettere il comando seguente per installare i pacchetti necessari per i moduli di Azure Python SDK:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Immettere il comando seguente per installare i pacchetti Ansible necessari:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Creare le credenziali di Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

In questa sezione si configura SLES per l'uso di Ansible.

1. Aprire una finestra del terminale.

1. Immettere il comando seguente per installare i pacchetti necessari per i moduli di Azure Python SDK:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Immettere il comando seguente per installare i pacchetti Ansible necessari:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Immettere il comando seguente per rimuovere il pacchetto di crittografia Python in conflitto:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Creare le credenziali di Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Creare credenziali di Azure

Per configurare le credenziali di Ansible, sono necessarie le informazioni seguenti:

* L'ID sottoscrizione di Azure 
* I valori dell'entità servizio

Se si usa Ansible Tower o Jenkins, dichiarare i valori dell'entità servizio come variabili di ambiente.

Configurare le credenziali di Ansible con una delle tecniche seguenti:

- [Creare un file di credenziali di Ansible](#file-credentials)
- [Usare variabili di ambiente di Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Creare un file di credenziali di Ansible

In questa sezione viene creato un file di credenziali locale per fornire credenziali ad Ansible. 

Per altre informazioni su come definire le credenziali di Ansible, vedere [Providing Credentials to Azure Modules](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules) (Fornire le credenziali ai moduli di Azure).

1. Per un ambiente di sviluppo, creare un file denominato `credentials` nella macchina virtuale host:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Inserire le righe seguenti nel file. Sostituire i segnaposto con i valori dell'entità servizio.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Salvare e chiudere il file.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Usare variabili di ambiente di Ansible

In questa sezione vengono esportati i valori dell'entità servizio per configurare le credenziali di Ansible.

1. Aprire una finestra del terminale.

1. Esportare i valori dell'entità servizio:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Verificare la configurazione

Per verificare la configurazione, usare Ansible per creare un gruppo di risorse di Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Avvio rapido: Configurare una macchina virtuale Linux in Azure tramite Ansible](./ansible-create-vm.md)