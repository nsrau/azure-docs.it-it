---
title: Eseguire Ansible in Azure Cloud Shell
description: "Informazioni su come eseguire diverse attività di Ansible in Azure Cloud Shell"
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: d5a818616d382954d0880bcae58bb13b632ad757
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="run-ansible-in-the-azure-cloud-shell"></a>Eseguire Ansible in Azure Cloud Shell

In questa esercitazione si imparerà come eseguire diverse attività di Ansible in Azure Cloud Shell. Queste attività includono la connessione a una macchina virtuale e la creazione di playbook di Ansible per creare ed eliminare un gruppo di risorse di Azure.

## <a name="prerequisites"></a>prerequisiti

- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Credenziali di Azure** - [Creare le credenziali di Azure e configurare Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Configurare Azure Cloud Shell** - Se non si ha familiarità con Azure Cloud Shell, nell'articolo [Guida introduttiva a Bash in Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart) è descritto come avviare e configurare Cloud Shell.

## <a name="use-ansible-to-connect-to-a-vm"></a>Usare Ansible per connettersi a una macchina virtuale
Ansible ha creato uno script di Python denominato [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) che genera un inventario dinamico delle risorse di Azure eseguendo richieste API per Azure Resource Manager. La procedura seguente illustra l'uso dello script `azure_rm.py` per connettersi a una macchina virtuale di Azure:

1. Aprire Azure Cloud Shell.

1. Se non si dispone di una macchina virtuale da usare, immettere i comandi seguenti in Cloud Shell per creare una macchina virtuale con cui eseguire il test:

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Usare il comando `wget` GNU per recuperare lo script `azure_rm.py`:

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Usare il comando `chmod` per modificare le autorizzazioni di accesso per lo script `azure_rm.py`. Il comando seguente usa il parametro `+x` per consentire l'esecuzione del file specificato (`azure_rm.py`):

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Usare il [comando ansible](https://docs.ansible.com/ansible/2.4/ansible.html) per connettersi alla macchina virtuale: 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  Dopo avere stabilito la connessione, viene visualizzato un output simile al seguente:

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Se è stato creato un gruppo di risorse e una macchina virtuale in questa sezione:

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Eseguire un playbook in Cloud Shell
Il comando [ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) esegue i playbook Ansible eseguendo le attività nell'host o negli host di destinazione. In questa sezione viene illustrato in dettaglio come usare Cloud Shell per creare ed eseguire due playbook, il primo per creare un gruppo di risorse e il secondo per eliminare tale gruppo. 

1. Creare un file denominato `rg.yml` come indicato di seguito:

  ```azurecli-interactive
  vi rg.yml
  ```

1. Copiare il contenuto seguente nella finestra della Cloud Shell, che ora contiene un'istanza dell'editor VI:

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          location: eastus
  ```

1. Salvare il file e chiudere l'editor VI immettendo `:wq` e premendo &lt;INVIO>.

1. Usare il comando `ansible-playbook` per eseguire il playbook `rg.yml`:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Verrà visualizzata un output simile al seguente:

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. Verificare la distribuzione:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. Dopo aver creato il gruppo di risorse, creare un secondo playbook Ansible per eliminare il gruppo di risorse:

  ```azurecli-interactive
  vi rg2.yml
  ```

1. Copiare il contenuto seguente nella finestra della Cloud Shell, che ora contiene un'istanza dell'editor VI:

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          state: absent
  ```

1. Salvare il file e chiudere l'editor VI immettendo `:wq` e premendo &lt;INVIO>.

1. Usare il comando `ansible-playbook` per eseguire il playbook `rg2.yml`:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Verrà visualizzata un output simile al seguente:

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Creare una macchina virtuale di base in Azure con Ansible](/azure/virtual-machines/linux/ansible-create-vm)