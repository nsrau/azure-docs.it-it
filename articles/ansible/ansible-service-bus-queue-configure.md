---
title: 'Esercitazione: Configurare code nel bus di servizio di Azure tramite Ansible | Microsoft Docs'
description: Informazioni su come usare Ansible per creare una coda nel bus di servizio di Azure
keywords: ansible, azure, devops, bash, playbook, bus di servizio, coda
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 6efc11106fae18beac43ab1896733ab6bfc64dad
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230781"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Esercitazione: Configurare code nel bus di servizio di Azure tramite Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare una coda
> * Creare criteri di firma di accesso condiviso
> * Recuperare informazioni sullo spazio dei nomi
> * Recuperare informazioni sulla coda
> * Revocare i criteri di firma di accesso condiviso della coda

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Creare una coda del bus di servizio

Il codice di esempio del playbook crea le risorse seguenti:
- Gruppo di risorse di Azure
- Spazio dei nomi del bus di servizio all'interno del gruppo di risorse
- Coda del bus di servizio con lo spazio dei nomi

Salvare il playbook seguente come `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Creare criteri di firma di accesso condiviso

Una [firma di accesso condiviso](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) è un meccanismo di autorizzazione basato su attestazioni che usa token. 

Il codice di esempio del playbook crea due criteri di firma di accesso condiviso per una coda del bus di servizio con privilegi diversi.

Salvare il playbook seguente come `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Prima di eseguire il playbook, vedere le note seguenti:
- Il valore `rights` rappresenta il privilegio di un utente per la coda. Specificare uno di questi valori: `manage`, `listen`, `send` o `listen_send`.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Recuperare informazioni sullo spazio dei nomi

Il codice di esempio del playbook esegue una query per ottenere informazioni sullo spazio dei nomi.

Salvare il playbook seguente come `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Prima di eseguire il playbook, vedere le note seguenti:
- Il valore `show_sas_policies` indica se visualizzare i criteri di firma di accesso condiviso nello spazio dei nomi specificato. Per impostazione predefinita, il valore è `False` per evitare un sovraccarico di rete aggiuntivo.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Recuperare informazioni sulla coda

Il codice di esempio del playbook esegue una query per ottenere informazioni sulla coda. 

Salvare il playbook seguente come `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Prima di eseguire il playbook, vedere le note seguenti:
- Il valore `show_sas_policies` indica se visualizzare i criteri di firma di accesso condiviso nella coda specificata. Per impostazione predefinita, il valore è impostato su `False` per evitare un sovraccarico di rete aggiuntivo.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Revocare i criteri di firma di accesso condiviso della coda

Il codice di esempio del playbook elimina un criterio di firma di accesso condiviso della coda.

Salvare il playbook seguente come `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Aggiungere il codice seguente come `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Esercitazione: Configurare un argomento nel bus di servizio di Azure tramite Ansible](ansible-service-bus-topic-configure.md)