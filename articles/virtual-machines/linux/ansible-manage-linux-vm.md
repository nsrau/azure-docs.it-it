---
title: Usare Ansible per gestire una macchina virtuale Linux in Azure
description: Informazioni su come usare Ansible per gestire una macchina virtuale Linux in Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: 14e675097a23b50c8ff193c69c377ac8edfb989f
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317263"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Usare Ansible per gestire una macchina virtuale Linux in Azure
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire le macchine virtuali in Azure, allo stesso modo di qualsiasi altra risorsa. Questo articolo descrive come usare un playbook Ansible per avviare e arrestare una macchina virtuale di Linux. 

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: se non si possiede una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Usare Ansible per deallocare (arrestare) una macchina virtuale di Azure
Questa sezione descrive come usare Ansible per deallocare (arrestare) una macchina virtuale di Azure

1.  Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Aprire [Cloud Shell](/azure/cloud-shell/overview).

1.  Creare un file (che conterrà il playbook) denominato `azure-vm-stop.yml` e aprirlo nell'editor VI, in questo modo:

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  Attivare la modalità di inserimento con il tasto **I**.

1.  Incollare il codice di esempio seguente nell'editor:

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

1.  Premere **ESC** per disattivare la modalità di inserimento.

1.  Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1.  Eseguire il playbook Ansible di esempio.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  L'output è simile all'esempio seguente, che mostra che la macchina virtuale è stata deallocata (arrestata) correttamente:

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

1.  Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Aprire [Cloud Shell](/azure/cloud-shell/overview).

1.  Creare un file (che conterrà il playbook) denominato `azure-vm-start.yml` e aprirlo nell'editor VI, in questo modo:

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  Attivare la modalità di inserimento con il tasto **I**.

1.  Incollare il codice di esempio seguente nell'editor:

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

1.  Premere **ESC** per disattivare la modalità di inserimento.

1.  Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1.  Eseguire il playbook Ansible di esempio.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  L'output è simile all'esempio seguente, che mostra che la macchina virtuale è stata avviata correttamente:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Usare Ansible per gestire gli inventari dinamici di Azure](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)