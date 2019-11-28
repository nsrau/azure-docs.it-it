---
title: Esercitazione - Configurare app nel Servizio app di Azure con Ansible
description: Informazioni su come creare un'app nel servizio app di Azure con Java 8 e il runtime del contenitore Tomcat
keywords: ansible, azure, devops, bash, playbook, Servizio app di Azure, App Web, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156561"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Esercitazione: Configurare app nel servizio app di Azure tramite Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare un'app nel servizio app di Azure con Java 8 e il runtime del contenitore Tomcat
> * Creare un profilo di Gestione traffico di Azure
> * Definire un endpoint di Gestione traffico con l'app creata

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Creare un servizio app di base

Il codice del playbook in questa sezione definisce le risorse seguenti:

* Gruppo di risorse di Azure in cui vengono distribuiti il piano di servizio app e l'app
* Servizio app in Linux con Java 8 e il runtime del contenitore Tomcat

Salvare il playbook seguente come `firstwebapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook firstwebapp.yml
```

Dopo avere eseguito il playbook, viene visualizzato un output simile ai risultati seguenti:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Creare un'app e usare Gestione traffico di Azure

[Gestione traffico di Azure](/azure/app-service/web-sites-traffic-manager) consente di controllare il modo in cui le richieste dai client Web vengono distribuite alle app nel servizio app di Azure. Quando si aggiungono endpoint di servizio app a un profilo di Gestione traffico di Azure, Gestione traffico rileva lo stato delle app del servizio app. Lo stato può essere in esecuzione, arrestata ed eliminata. Gestione traffico viene usato per decidere quali endpoint devono ricevere il traffico.

Nel servizio app un'app viene eseguita in un [piano di servizio app](/azure/app-service/overview-hosting-plans). Un piano di servizio app definisce un set di risorse di calcolo per l'esecuzione di un'app. È possibile gestire il piano di servizio app e l'app Web in gruppi diversi.

Il codice del playbook in questa sezione definisce le risorse seguenti:

* Gruppo di risorse di Azure all'interno del quale viene distribuito il piano di servizio app
* Piano di servizio app
* Gruppo di risorse di Azure in cui viene distribuita l'app
* Servizio app in Linux con Java 8 e il runtime del contenitore Tomcat
* Profilo di Gestione traffico
* Endpoint di Gestione traffico con l'app creata

Salvare il playbook seguente come `webapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook webapp.yml
```

Dopo avere eseguito il playbook, viene visualizzato un output simile ai risultati seguenti:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Esercitazione: Ridimensionare app nel servizio app di Azure tramite Ansible](/azure/ansible/ansible-scale-azure-web-apps)