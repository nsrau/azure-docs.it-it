---
title: Esercitazione - Configurare argomenti nel bus di servizio di Azure con Ansible
description: Informazioni su come usare Ansible per creare un argomento nel bus di servizio di Azure
keywords: ansible, azure, devops, bash, playbook, bus di servizio, argomenti, sottoscrizioni
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155805"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Esercitazione: Configurare argomenti nel bus di servizio di Azure tramite Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare un argomento
> * Creare una sottoscrizione
> * Creare criteri di firma di accesso condiviso
> * Recuperare informazioni sullo spazio dei nomi
> * Recuperare informazioni sull'argomento e la sottoscrizione
> * Revocare criteri di firma di accesso condiviso

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Creare un argomento del bus di servizio

Il codice di esempio del playbook crea le risorse seguenti:
- Gruppo di risorse di Azure
- Spazio dei nomi del bus di servizio all'interno del gruppo di risorse
- Argomento del bus di servizio con lo spazio dei nomi

Salvare il playbook seguente come `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Creare la sottoscrizione

Il codice del playbook di esempio crea la sottoscrizione in un argomento del bus di servizio. Gli argomenti del bus di servizio di Azure possono avere più sottoscrizioni. Il sottoscrittore di un argomento può ricevere una copia di ogni messaggio inviato a tale argomento. Le sottoscrizioni sono entità denominate, che vengono create in modo permanente, ma possono facoltativamente scadere.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Salvare il playbook seguente come `servicebus_subscription.yml`:

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Creare criteri di firma di accesso condiviso

Una [firma di accesso condiviso](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) è un meccanismo di autorizzazione basato su attestazioni che usa token. 

Il codice di esempio del playbook crea due criteri di firma di accesso condiviso per una coda del bus di servizio con privilegi diversi.

Salvare il playbook seguente come `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Recuperare informazioni sull'argomento e la sottoscrizione

Il codice di esempio del playbook esegue una query per ottenere le informazioni seguenti:
- Informazioni sull'argomento del bus di servizio
- Elenco dei dettagli della sottoscrizione per l'argomento
 
Salvare il playbook seguente come `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Prima di eseguire il playbook, vedere le note seguenti:
- Il valore `show_sas_policies` indica se visualizzare i criteri di firma di accesso condiviso nella coda specificata. Per impostazione predefinita, il valore è impostato su `False` per evitare un sovraccarico di rete aggiuntivo.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Ansible in Azure](/azure/ansible/)