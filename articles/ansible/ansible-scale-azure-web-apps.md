---
title: Esercitazione - Ridimensionare app nel Servizio app di Azure con Ansible
description: Informazioni su come ridimensionare un'app nel servizio app di Azure
keywords: ansible, azure, devops, bash, playbook, servizio app di Azure, app Web, ridimensionare, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155920"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Esercitazione: Ridimensionare app nel servizio app di Azure tramite Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Trovare informazioni su un piano di servizio app esistente
> * Ampliare il piano di servizio app al livello S2 con tre ruoli di lavoro

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **App di servizio app di Azure** - Se non è disponibile un'app del servizio app di Azure, [configurare un'app nel servizio app di Azure tramite Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Aumentare le prestazioni di un'app

Sono disponibili due flussi di lavoro per la scalabilità, l'*aumento delle prestazioni* e l'*aumento del numero di istanze*.

**Aumentare le prestazioni:** aumentare le prestazioni significa acquisire più risorse. Queste risorse includono CPU, memoria, spazio su disco, macchine virtuali e altro ancora. È possibile aumentare le prestazioni di un'app cambiando il piano tariffario del piano di servizio app a cui appartiene l'app. 
**Aumentare il numero di istanze:** è possibile incrementare il numero di istanze di macchine virtuali che eseguono l'app. A seconda del piano tariffario del piano di servizio app, è possibile specificare al massimo 20 istanze. La [scalabilità automatica](/azure/azure-monitor/platform/autoscale-get-started) consente di aumentare automaticamente il numero di istanze in base a pianificazioni e regole predefinite.

Il codice del playbook in questa sezione definisce l'operazione seguente:

* Trovare informazioni su un piano di servizio app esistente
* Aggiornare il piano di servizio app al livello S2 con tre ruoli di lavoro

Salvare il playbook seguente come `webapp_scaleup.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook webapp_scaleup.yml
```

Dopo avere eseguito il playbook, viene visualizzato un output simile ai risultati seguenti:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)