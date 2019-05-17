---
title: Esercitazione - Distribuire app in set di scalabilità di macchine virtuali in Azure tramite Ansible | Microsoft Docs
description: Informazioni su come usare Ansible per configurare set di scalabilità di macchine virtuali di Azure e distribuire l'applicazione nel set di scalabilità
keywords: ansible, azure, devops, bash, playbook, macchina virtuale, set di scalabilità di macchine virtuali, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a44fd06ace9b21122f5f4253ac7d9601b54e6b62
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231033"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Esercitazione: Distribuire app in set di scalabilità di macchine virtuali in Azure tramite Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Recuperare informazioni sull'host per un gruppo di macchine virtuali di Azure
> * Clonare e compilare l'app di esempio
> * Installare JRE (Java Runtime Environment) in un set di scalabilità
> * Distribuire l'applicazione Java in un set di scalabilità

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) viene usato per scaricare un esempio di Java usato in questa esercitazione.
- **Java SE Development Kit (JDK)** - [JDK](https://aka.ms/azure-jdks) viene usato per compilare il progetto Java di esempio.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) viene usato per compilare il progetto Java di esempio.

## <a name="get-host-information"></a>Ottenere informazioni sugli host

Il codice del playbook in questa sezione consente di recuperare informazioni sull'host per un gruppo di macchine virtuali. Il codice ottiene gli indirizzi IP pubblici e il bilanciamento del carico all'interno di un gruppo di risorse specificato e crea un gruppo host denominato `scalesethosts` nell'inventario.

Salvare il playbook di esempio seguente come `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Preparare un'applicazione per la distribuzione

Il codice del playbook in questa sezione usa `git` per clonare un progetto di esempio Java da GitHub e compila il progetto. 

Salvare il playbook seguente come `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Eseguire il playbook di Ansible di esempio con il comando seguente:

  ```bash
  ansible-playbook app.yml
  ```

Dopo avere eseguito il playbook, viene visualizzato un output simile ai risultati seguenti:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Distribuire l'applicazione in un set di scalabilità

Il codice del playbook in questa sezione viene usato per:

* Installare JRE in un gruppo host denominato `saclesethosts`
* Distribuire l'applicazione Java in un gruppo host denominato `saclesethosts`

È possibile ottenere il playbook di esempio in due modi:

* [Scaricare il playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) e salvarlo in `vmss-setup-deploy.yml`.
* Creare un nuovo file denominato `vmss-setup-deploy.yml` e copiarvi il contenuto seguente:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Prima di eseguire il playbook, vedere le note seguenti:

* Nella sezione `vars` sostituire il segnaposto `{{ admin_password }}` con la propria password.
* Per usare il tipo di connessione ssh con le password, installare il programma sshpass:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* In alcuni ambienti potrebbe essere visualizzato un errore sull'uso di una password SSH invece di una chiave. Se si riceve questo errore, è possibile disabilitare il controllo della chiave dell'host aggiungendo la riga seguente a `/etc/ansible/ansible.cfg` o `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Eseguire il playbook con il comando seguente:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

L'output prodotto dall'esecuzione del comando ansible-playbook indica che l'applicazione Java di esempio è stata installata nel gruppo host del set di scalabilità:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Verificare i risultati

Verificare i risultati delle varie operazioni eseguite passando all'URL del servizio di bilanciamento del carico per il set di scalabilità:

![App Java in esecuzione in un set di scalabilità in Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Scalabilità automatica di set di scalabilità di macchine virtuali in Azure con Ansible](./ansible-auto-scale-vmss.md)