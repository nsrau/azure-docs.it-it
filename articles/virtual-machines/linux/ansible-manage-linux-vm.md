---
title: Usare Ansible per gestire una macchina virtuale Linux in Azure
description: Informazioni su come usare Ansible per gestire una macchina virtuale Linux in Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 66106346b298fae22cce47081916a6c8eec8fd40
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250670"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Usare Ansible per gestire una macchina virtuale Linux in Azure
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire le macchine virtuali in Azure, allo stesso modo di qualsiasi altra risorsa. Questo articolo descrive come usare un playbook Ansible per avviare e arrestare una macchina virtuale di Linux. 

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: in assenza di una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Configurare Azure Cloud Shell** o **installare e configurare Ansible in una macchina virtuale Linux**

  **Configurare Azure Cloud Shell**

  1. **Configurare Azure Cloud Shell**: se non si ha familiarità con Azure Cloud Shell, l'articolo [Guida introduttiva a Bash in Azure Cloud Shell](/azure/cloud-shell/quickstart) descrive come avviare e configurare Cloud Shell. 

  1. **Macchina virtuale Linux**: se non si ha accesso a una macchina virtuale Linux, è possibile [creare una macchina virtuale con Ansible](ansible-create-vm.md).

  **-- OPPURE --**

  **Installare e configurare Ansible in una macchina virtuale Linux**

  1. **Installare Ansible**: installare Ansible in una [piattaforma Linux supportata](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Configurare Ansible** - [Creare le credenziali di Azure e configurare Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Usare Ansible per deallocare (arrestare) una macchina virtuale di Azure
Questa sezione descrive come usare Ansible per deallocare (arrestare) una macchina virtuale di Azure

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Aprire [Cloud Shell](/azure/cloud-shell/overview).

1. Creare un file (che conterrà il playbook) denominato `azure_vm_stop.yml` e aprirlo nell'editor VI, in questo modo:

  ```azurecli-interactive
  vi azure_vm_stop.yml
  ```

1. Attivare la modalità di inserimento con il tasto **I**.

1. Incollare il codice di esempio seguente nell'editor:

    ```yaml
    - name: Stop Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
            allocated: no 
    ```

1. Premere **ESC** per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Eseguire il playbook Ansible di esempio.

  ```bash
  ansible-playbook azure_vm_stop.yml
  ```

1. L'output è simile all'esempio seguente, che mostra che la macchina virtuale è stata deallocata (arrestata) correttamente:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>Usare Ansible per avviare una macchina virtuale di Azure deallocata (arrestata)
Questa sezione descrive come usare Ansible per avviare una macchina virtuale di Azure deallocata (arrestata)

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Aprire [Cloud Shell](/azure/cloud-shell/overview).

1. Creare un file (che conterrà il playbook) denominato `azure_vm_start.yml` e aprirlo nell'editor VI, in questo modo:

  ```azurecli-interactive
  vi azure_vm_start.yml
  ```

1. Attivare la modalità di inserimento con il tasto **I**.

1. Incollare il codice di esempio seguente nell'editor:

    ```yaml
    - name: Start Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Start the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
    ```

1. Premere **ESC** per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Eseguire il playbook Ansible di esempio.

  ```bash
  ansible-playbook azure_vm_start.yml
  ```

1. L'output è simile all'esempio seguente, che mostra che la macchina virtuale è stata avviata correttamente:

    L'output è simile all'esempio seguente, che mostra che la macchina virtuale è stata avviata correttamente:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Usare Ansible per gestire gli inventari dinamici di Azure](../../ansible/ansible-manage-azure-dynamic-inventories.md)