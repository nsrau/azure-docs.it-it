---
title: Esercitazione - Aggiornare l'immagine personalizzata di set di scalabilità di macchine virtuali di Azure tramite Ansible | Microsoft Docs
description: Informazioni su come usare Ansible per aggiornare i set di scalabilità di macchine virtuali in Azure con l'immagine personalizzata
keywords: ansible, azure, devops, bash, playbook, macchina virtuale, set di scalabilità di macchine virtuali, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d3eedc5b83190af46669b9b5df8643f3c80e9bb1
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230841"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Esercitazione: Aggiornare l'immagine personalizzata di set di scalabilità di macchine virtuali di Azure tramite Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Dopo averla distribuita, si configura la macchina virtuale con il software necessario per l'app. Invece di eseguire questa attività di configurazione per ogni macchina virtuale, è possibile creare un'immagine personalizzata. Un'immagine personalizzata è uno snapshot di una macchina virtuale esistente che include qualsiasi software installato. Quando si [configura un set di scalabilità](./ansible-create-configure-vmss.md), si specifica l'immagine da usare per le macchine virtuali del set di scalabilità. Usando un'immagine personalizzata, ogni istanza di macchina virtuale viene configurata in modo identico per l'app. In alcuni casi, potrebbe essere necessario aggiornare l'immagine personalizzata del set di scalabilità. Questa attività è l'obiettivo di questa esercitazione.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurare due macchine virtuali con HTTPD
> * Creare un'immagine personalizzata da una macchina virtuale esistente
> * Creare un set di scalabilità da un'immagine
> * Aggiornare l'immagine personalizzata

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Configurare due macchine virtuali

Il codice del playbook in questa sezione crea due macchine virtuali con HTTPD installato in entrambe. 

La pagina `index.html` per ogni macchina virtuale visualizza una stringa di test:

* La prima macchina virtuale visualizza il valore `Image A`
* La seconda macchina virtuale visualizza il valore `Image B`

Questa stringa è progettata per simulare la configurazione di ogni macchina virtuale con un software diverso.

È possibile ottenere il playbook di esempio in due modi:

* [Scaricare il playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) e salvarlo in `create_vms.yml`.
* Creare un nuovo file denominato `create_vms.yml` e copiarvi il contenuto seguente:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Eseguire il playbook usando il comando `ansible-playbook`, sostituendo `myrg` con il nome del gruppo di risorse:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Grazie alla presenza delle sezioni `debug` del playbook, il comando `ansible-playbook` stamperà l'indirizzo IP di ogni macchina virtuale. Copiare questi indirizzi IP per un uso successivo.

![Indirizzi IP delle macchine virtuali](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Connettersi alle due macchine virtuali

In questa sezione ci si connette a ogni macchina virtuale. Come accennato nella sezione precedente, le stringhe `Image A` e `Image B` simulano la presenza di due macchine virtuali distinte con configurazioni diverse.

Usare gli indirizzi IP dalla sezione precedente per connettersi a entrambe le macchine virtuali:

![Screenshot dalla macchina virtuale A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Screenshot dalla macchina virtuale B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Creare immagini da ogni macchina virtuale

A questo punto sono disponibili due macchine virtuali con configurazioni leggermente diverse (i file `index.html` corrispondenti).

Il codice del playbook in questa sezione creata un'immagine personalizzata per ogni macchina virtuale:

* `image_vmforimageA` - Immagine personalizzata creata per la macchina virtuale che consente di visualizzare `Image A` nella relativa home page.
* `image_vmforimageB` - Immagine personalizzata creata per la macchina virtuale che consente di visualizzare `Image B` nella relativa home page.

È possibile ottenere il playbook di esempio in due modi:

* [Scaricare il playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) e salvarlo in `capture-images.yml`.
* Creare un nuovo file denominato `capture-images.yml` e copiarvi il contenuto seguente:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Eseguire il playbook usando il comando `ansible-playbook`, sostituendo `myrg` con il nome del gruppo di risorse:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Creare un set di scalabilità usando l'immagine A

In questa sezione viene usato un playbook per configurare le risorse di Azure seguenti:

* Indirizzo IP pubblico
* Bilanciamento del carico
* Set di scalabilità che fa riferimento a `image_vmforimageA`

È possibile ottenere il playbook di esempio in due modi:

* [Scaricare il playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) e salvarlo in `create-vmss.yml`.
* Creare un nuovo file denominato `create-vmss.yml` e copiarvi il contenuto seguente:

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Eseguire il playbook usando il comando `ansible-playbook`, sostituendo `myrg` con il nome del gruppo di risorse:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Grazie alla presenza della sezione `debug` del playbook, il comando `ansible-playbook` stamperà l'indirizzo IP del set di scalabilità. Copiare questo indirizzo IP per usarlo in seguito.

![Indirizzo IP pubblico](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Connettersi al set di scalabilità

In questa sezione ci si connette al set di scalabilità. 

Connettersi al set di scalabilità usando l'indirizzo IP dalla sezione precedente.

Come accennato nella sezione precedente, le stringhe `Image A` e `Image B` simulano la presenza di due macchine virtuali distinte con configurazioni diverse.

Il set di scalabilità fa riferimento all'immagine personalizzata denominata `image_vmforimageA`. L'immagine personalizzata `image_vmforimageA` è stata creata dalla macchina virtuale la cui home page visualizza `Image A`.

Viene pertanto visualizzata una home page con `Image A`:

![Il set di scalabilità è associato alla prima macchina virtuale.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Lasciare aperta la finestra del browser mentre si continua con la sezione successiva.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Modificare l'immagine personalizzata nel set di scalabilità e aggiornare le istanze

Il codice del playbook in questa sezione cambia l'immagine del set di scalabilità da `image_vmforimageA` a `image_vmforimageB`. Vengono anche aggiornate tutte le macchine virtuali correnti distribuite dal set di scalabilità.

È possibile ottenere il playbook di esempio in due modi:

* [Scaricare il playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) e salvarlo in `update-vmss-image.yml`.
* Creare un nuovo file denominato `update-vmss-image.yml` e copiarvi il contenuto seguente:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Eseguire il playbook usando il comando `ansible-playbook`, sostituendo `myrg` con il nome del gruppo di risorse:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Tornare al browser e aggiornare la pagina. 

Si noterà che l'immagine personalizzata sottostante alla macchina virtuale è aggiornata.

![Il set di scalabilità è associato alla seconda macchina virtuale](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Aggiungere il codice seguente come `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible)