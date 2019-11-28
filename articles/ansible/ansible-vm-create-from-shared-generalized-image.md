---
title: 'Esercitazione: Creare una VM o un set di scalabilità di macchine virtuali dalla Raccolta immagini condivise di Azure con Ansible'
description: Informazioni su come usare Ansible per creare una VM o un set di scalabilità di macchine virtuali in base a un'immagine generalizzata nella Raccolta immagini condivise.
keywords: ansible, azure, devops, bash, playbook, macchina virtuale, set di scalabilità di macchine virtuali, raccolta immagini condivise
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155833"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Esercitazione: Creare una VM o un set di scalabilità di macchine virtuali dalla Raccolta immagini condivise di Azure con Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Raccolta immagini condivise ](/azure/virtual-machines/windows/shared-image-galleries) è un servizio che consente di gestire, condividere e organizzare facilmente immagini gestite in modo personalizzato. Questa funzionalità è utile per scenari in cui vengono gestite e condivise molte immagini. Le immagini personalizzate possono essere condivise tra le sottoscrizioni e tra i tenant di Azure Active Directory. Le immagini possono anche essere replicate in più aree, per un ridimensionamento più rapido delle distribuzioni.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare una macchina virtuale generalizzata e un'immagine personalizzata
> * Creare una Raccolta immagini condivise
> * Creare un'immagine condivisa e una versione dell'immagine
> * Creare una VM con l'immagine generalizzata
> * Creare un set di scalabilità di macchine virtuali con l'immagine generalizzata
> * Ottenere informazioni sulla Raccolta immagini condivise, sull'immagine e sulla versione.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Ottenere i playbook di esempio

È possibile ottenere il set completo di playbook di esempio in due modi:

- [Scaricare la cartella SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) e salvarla nel computer locale.
- Creare un nuovo file per ogni sezione e copiarvi il playbook di esempio.

Il file `vars.yml` contiene le variabili usate da tutti i playbook di esempio di questa esercitazione. È possibile modificare il file per fornire nomi e valori univoci.

Il primo playbook di esempio `00-prerequisites.yml` crea gli elementi necessari per completare questa esercitazione:
- Un gruppo di risorse, ovvero un contenitore logico in cui vengono distribuite e gestite le risorse di Azure.
- Una rete virtuale, una subnet, un indirizzo IP pubblico e una scheda di interfaccia di rete per la VM.
- Una macchina virtuale di origine, che viene usata per creare l'immagine generalizzata.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook 00-prerequisites.yml
```

Nel [portale di Azure ](https://portal.azure.com) verificare il gruppo di risorse specificato in `vars.yml` per visualizzare la nuova macchina virtuale e le varie risorse create.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Generalizzare la VM e creare un'immagine personalizzata

Il playbook successivo, `01a-create-generalized-image.yml`, generalizza la VM di origine creata nel passaggio precedente e quindi crea un'immagine personalizzata basata su di essa.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Verificare il gruppo di risorse e assicurarsi che venga visualizzato `testimagea`.

## <a name="create-the-shared-image-gallery"></a>Creare la Raccolta immagini condivise

La raccolta immagini è il repository per la condivisione e la gestione delle immagini. Il codice del playbook di esempio in `02-create-shared-image-gallery.yml` crea una Raccolta immagini condivise nel gruppo di risorse.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Viene ora visualizzata una nuova raccolta, `myGallery`, nel gruppo di risorse.

## <a name="create-a-shared-image-and-image-version"></a>Creare un'immagine condivisa e una versione dell'immagine

Il playbook successivo, `03a-create-shared-image-generalized.yml`, crea una definizione dell'immagine e una versione dell'immagine.

Le definizioni delle immagini includono il tipo di immagine (Windows o Linux), le note sulla versione e i requisiti minimi e massimi di memoria. La versione dell'immagine è la versione dell'immagine. La raccolta, la definizione dell'immagine e la versione dell'immagine consentono di organizzare le immagini in gruppi logici. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Il gruppo di risorse contiene ora una definizione dell'immagine e una versione dell'immagine per la raccolta.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Creare una VM in base all'immagine generalizzata

Infine, eseguire `04a-create-vm-using-generalized-image.yml` per creare una VM in base all'immagine generalizzata creata nel passaggio precedente.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Creare un set di scalabilità di macchine virtuali in base all'immagine generalizzata

È anche possibile creare un set di scalabilità di macchine virtuali in base all'immagine generalizzata. A tale scopo, eseguire `05a-create-vmss-using-generalized-image.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Ottenere informazioni sulla raccolta

È possibile ottenere informazioni sulla raccolta, la definizione dell'immagine e la versione eseguendo `06-get-info.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Eliminare l'immagine condivisa

Per eliminare le risorse della raccolta, vedere il playbook di esempio `07-delete-gallery.yml`. Eliminare le risorse nell'ordine inverso. Iniziare a eliminare la versione dell'immagine. Dopo aver eliminato tutte le versioni dell'immagine, è possibile eliminare la definizione dell'immagine. Dopo aver eliminato tutte le definizioni dell'immagine, è possibile eliminare la raccolta.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Il codice del playbook di esempio in questa sezione viene usato per:

- Eliminare i due gruppi di risorse creati in precedenza

Salvare il playbook seguente come `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Quando si usa il playbook di esempio è necessario tenere conto di alcuni concetti fondamentali:

- Sostituire il segnaposto `{{ resource_group_name }}` con il nome del proprio gruppo di risorse.
- Tutte le risorse nei due gruppi di risorse specificati verranno eliminate.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)