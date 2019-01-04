---
title: Ridimensionare le app Web del servizio app di Azure con Ansible
description: Informazioni su come usare Ansible per creare un'app Web con il runtime di Java 8 e del contenitore Tomcat nel servizio app in Linux
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, servizio app di Azure, app Web, ridimensionare, Java
author: tomarcher
manager: jeconnoc
ms.author: kyliel
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 740ff6d6a636377f9d58a5231692c87f935ae6d2
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601866"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Ridimensionare le app Web del servizio app di Azure con Ansible
Le [app Web del servizio app di Azure ](https://docs.microsoft.com/azure/app-service/overview), o semplicemente app Web, consentono l'hosting di applicazioni Web, API REST e back-end mobili. È possibile usare il linguaggio di sviluppo preferito &mdash; .NET, .NET Core, Java, Ruby, Node.js, PHP o Python.

Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. Questo articolo illustra come usare Ansible per ridimensionare l'app nel servizio app di Azure.

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **App Web del servizio app di Azure** - Se non si dispone ancora di un'app Web del servizio app di Azure, è possibile [creare app Web di Azure con Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Aumentare le prestazioni di un'app nel servizio app
È possibile aumentare le prestazioni cambiando il piano tariffario del piano del servizio app a cui appartiene l'app. In questa sezione viene presentato un playbook Ansible di esempio che definisce l'operazione seguente:
- Trovare informazioni su un piano di servizio app esistente
- Aggiornare il piano di servizio app al livello S2 con tre ruoli di lavoro

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App serivce plan
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

Salvare il playbook come *webapp_scaleup.yml*.

Per eseguire il playbook, usare il comando **ansible-playbook** come segue:
```bash
ansible-playbook webapp_scaleup.yml
```

Dopo aver eseguito il playbook, un output simile all'esempio seguente indica che il piano del servizio app è stato successivamente aggiornato al livello S2 con tre ruoli di lavoro:
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App serivce plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Ansible in Azure](https://docs.microsoft.com/azure/ansible/)