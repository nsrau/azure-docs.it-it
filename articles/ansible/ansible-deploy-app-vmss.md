---
title: Distribuire applicazioni nei set di scalabilità di macchine virtuali in Azure tramite Ansible
description: Informazioni su come usare Ansible per configurare un set di scalabilità di macchine virtuali e distribuire l'applicazione nel set di scalabilità di macchine virtuali in Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, macchina virtuale, set di scalabilità di macchine virtuali, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: b9c8058606e13c0db4908530e98cddb69d2caf50
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011499"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Distribuire applicazioni nei set di scalabilità di macchine virtuali in Azure tramite Ansible
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per distribuire le applicazioni in Azure. Questo articolo illustra come distribuire un'applicazione Java in un set di scalabilità di macchine virtuali di Azure (VMSS).  

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- **Configurare Ansible** - [Creare le credenziali di Azure e configurare Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible e i moduli Python SDK di Azure** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Set di scalabilità di macchine virtuali** - Se non si dispone già di un set di scalabilità di macchine virtuali, è possibile [creare un set di scalabilità di macchine virtuali con Ansible](ansible-create-configure-vmss.md). 
- **git** - [git](https://git-scm.com) viene usato per scaricare un esempio di Java usato in questa esercitazione.
- **Java SE Development Kit (JDK)** -JDK viene usato per compilare il progetto Java di esempio.
- **Strumenti di compilazione di Apache Maven** - Gli [strumenti di compilazione di Apache Maven](https://maven.apache.org/download.cgi) vengono usati per compilare il progetto Java di esempio.

> [!Note]
> In questa esercitazione, per eseguire i playbook di esempio seguenti è necessario Ansible 2.6. 

## <a name="get-host-information"></a>Ottenere informazioni sugli host

Questa sezione illustra come usare Ansible per recuperare informazioni sull'host per un gruppo di macchine virtuali di Azure. Di seguito è riportato un playbook di Ansible di esempio. Il codice ottiene gli indirizzi IP pubblici e il bilanciamento del carico all'interno del gruppo di risorse specificato e crea un gruppo host denominato **saclesethosts** nell'inventario. 

Salvare il playbook di esempio seguente come `get-hosts-tasks.yml`: 

  ```yaml
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

In questa sezione, git viene usato per clonare un progetto Java di esempio da GitHub e compilare il progetto. Salvare il playbook seguente come `app.yml`:

  ```yaml
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

L'output del comando ansible-playbook è simile al seguente, in cui l'app di esempio clonata è stata compilata a partire da GitHub:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Distribuire l'applicazione in VMSS

La sezione seguente in un playbook Ansible consente di installare JRE (Java Runtime Environment) in un gruppo host denominato **saclesethosts**e di distribuire l'applicazione Java in un gruppo host denominato **saclesethosts**: 

(Sostituire la `admin_password` con la propria password.)

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
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

È possibile salvare il playbook di Ansible di esempio precedente come `vmss-setup-deploy.yml`, oppure [scaricare l'intero playbook di esempio](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Per usare il tipo di connessione ssh con le password è necessario installare il programma sshpass. 
  - Per Ubunto 16.04, eseguire il comando `apt-get install sshpass`.
  - Per CentOS 7.4, eseguire il comando `yum install sshpass`.

Potrebbe essere visualizzato l'errore **Non è possibile usare una password SSH anziché una chiave perché è abilitato il controllo della chiave host e sshpass non supporta questa opzione.  Aggiungere l'impronta digitale dell'host al proprio file known_hosts per gestire questo host.** Se viene visualizzato questo errore, è possibile disabilitare il controllo della chiave host aggiungendo la riga seguente al file `/etc/ansible/ansible.cfg` o al file `~/.ansible.cfg`:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Eseguire il playbook con il comando seguente:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

L'output prodotto dall'esecuzione del comando ansible-playbook indica che l'applicazione Java di esempio è stata installata nel gruppo host del set di scalabilità di macchine virtuali:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Congratulazioni. L'applicazione è in esecuzione su Azure. È ora possibile passare all'URL del servizio di bilanciamento del carico per il set di scalabilità di macchine virtuali:

![App Java in esecuzione in un set di scalabilità di macchine virtuali in Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Playbook Ansible di esempio per il set di scalabilità di macchine virtuali](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)