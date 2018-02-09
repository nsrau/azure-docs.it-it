---
title: Usare Ansible per gestire gli inventari dinamici di Azure
description: Informazioni su come usare Ansible per gestire gli inventari dinamici di Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloud shell, inventario dinamico
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 8753d039582abdf22f105bf7f139a35c224e7c59
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Usare Ansible per gestire gli inventari dinamici di Azure
Ansible può essere usato per eseguire il pull delle informazioni degli inventari da diverse origini (incluse origini cloud, ad esempio Azure) in un *inventario dinamico*. In questo articolo si usa [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) per configurare un inventario dinamico di Azure per Ansible in cui si creano due macchine virtuali, si contrassegna una di tali macchine virtuali e si installa Nginx nella macchina virtuale contrassegnata.

## <a name="prerequisites"></a>prerequisiti

- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Credenziali di Azure** - [Creare le credenziali di Azure e configurare Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Creare le macchine virtuali di test

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Aprire [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

1. Creare un gruppo di risorse di Azure in cui includere le macchine virtuali per questa esercitazione.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Creare due macchine virtuali Linux in Azure con una delle tecniche seguenti:

    - **Playbook di Ansible**: l'articolo [Creare una macchina virtuale di base in Azure con Ansible](/azure/virtual-machines/linux/ansible-create-vm) illustra come creare una macchina virtuale da un playbook di Ansible. Se si usa un playbook per definire una o entrambe le macchine virtuali, verificare che venga usata la connessione SSH invece di una password.

    - **Interfaccia della riga di comando di Azure**: eseguire ognuno dei comandi seguenti in Cloud Shell per creare le due macchine virtuali:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>Contrassegnare una macchina virtuale
È possibile [usare i tag per organizzare le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) in base a categorie definite dall'utente. 

Immettere il comando [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) seguente per contrassegnare la macchina virtuale `ansible-inventory-test-vm1` con la chiave `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/&lt;YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generare un inventario dinamico
Dopo avere definito (e contrassegnato) le macchine virtuali, è necessario generare l'inventario dinamico. Ansible fornisce uno script di Python denominato [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) che genera un inventario dinamico delle risorse di Azure eseguendo richieste API per Azure Resource Manager. La procedura seguente illustra l'uso dello script `azure_rm.py` per connettersi alle due macchine virtuali di Azure di test:

1. Usare il comando `wget` GNU per recuperare lo script `azure_rm.py`:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Usare il comando `chmod` per modificare le autorizzazioni di accesso per lo script `azure_rm.py`. Il comando seguente usa il parametro `+x` per consentire l'esecuzione del file specificato (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Usare il [comando ansible](https://docs.ansible.com/ansible/2.4/ansible.html) per connettersi al gruppo di risorse: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Dopo avere stabilito la connessione, viene visualizzato un output simile al seguente:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>Abilitare il tag della macchina virtuale
Dopo avere impostato il tag desiderato, è necessario "abilitarlo". Un modo per abilitare un tag consiste nell'esportarlo in una variabile di ambiente denominata `AZURE_TAGS` tramite il comando **export**:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Dopo avere esportato il tag, è possibile provare di nuovo il comando `ansible`:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Viene ora visualizzata una sola macchina virtuale (quella con il tag corrispondente al valore esportato nella variabile di ambiente **AZURE_TAGS**):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurare Nginx nella VM contrassegnata
Lo scopo dei tag è consentire un uso facile e rapido dei sottogruppi delle macchine virtuali. Si supponga, ad esempio, che sia necessario installare Nginx solo nelle macchine virtuali a cui è stato assegnato il tag `nginx`. La procedura seguente illustrata come questa operazione sia semplice:

1. Creare un file (che conterrà il playbook) denominato `nginx.yml`, come segue:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Inserire il codice seguente nel nuovo file `nginx.yml` creato:

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. Eseguire il playbook `nginx.yml`:

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. Dopo avere eseguito il playbook, viene visualizzato un output simile al seguente:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Testare l'installazione di Nginx
Questa sezione illustra una tecnica per testare l'installazione di Nginx nella macchina virtuale.

1. Usare il comando [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) per recuperare l'indirizzo IP della macchina virtuale `ansible-inventory-test-vm1`. Il valore restituito (l'indirizzo IP della macchina virtuale) viene quindi usato come parametro per il comando SSH per la connessione alla macchina virtuale.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Il comando [nginx -v](https://nginx.org/en/docs/switches.html) viene in genere usato per ottenere la versione di Nginx. Tuttavia, può anche essere usato per determinare se è installato Nginx. Immetterlo mentre si è connessi alla macchina virtuale `ansible-inventory-test-vm1`.

    ```azurecli-interactive
    nginx -v
    ```

1. Dopo avere eseguito il comando `nginx -v`, viene visualizzata la versione di Nginx (seconda riga) che indica che Nginx è installato.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Premere la combinazione di tasti **&lt;Ctrl+D** per disconnettere la sessione SSH.

1. Eseguendo i passaggi precedenti per la macchina virtuale `ansible-inventory-test-vm2`, viene visualizzato un messaggio informativo indicante dove è possibile ottenere Nginx (il che implica che non è ancora installato):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Creare una macchina virtuale di base in Azure con Ansible](/azure/virtual-machines/linux/ansible-create-vm)