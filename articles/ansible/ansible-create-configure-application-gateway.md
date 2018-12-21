---
title: Gestire il traffico Web con un gateway applicazione di Azure tramite Ansible
description: Informazioni su come usare Ansible per la creazione e la configurazione di un gateway applicazione di Azure per gestire il traffico Web
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, gateway applicazione, bilanciamento del carico, traffico Web
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: af7f22ae5c289a01e6876d8ce586cb32383c8d3b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253360"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>Gestire il traffico Web con un gateway applicazione di Azure tramite Ansible

Il [gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/) è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web.

Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. Questo articolo illustra come usare Ansible per creare un gateway applicazione. Descrive inoltre come usare il gateway per gestire il traffico verso due server Web in esecuzione nelle istanze di contenitore di Azure.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare due istanze di contenitore di Azure con immagini HTTPD
> * Creare un gateway applicazione utilizzabile con le istanze di contenitore di Azure nel pool di server

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> In questa esercitazione, per eseguire i playbook di esempio seguenti è necessario Ansible 2.7. 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.  

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroup** nella località **stati uniti orientali**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Salvare questo playbook come *rg.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Creare risorse di rete

Creare innanzitutto una rete virtuale per consentire al gateway applicazione di comunicare con altre risorse.

L'esempio seguente crea una rete virtuale **myVNet**, una subnet **myAGSubnet** e un indirizzo IP pubblico **myAGPublicIPAddress** con un dominio **mydomain**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Salvare questo playbook come *vnet_create.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Creare server

L'esempio seguente mostra come creare due istanze di contenitore di Azure con le immagini HTTPD da usare come server Web per il gateway applicazione.  

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Salvare questo playbook come *aci_create.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

L'esempio seguente crea un gateway applicazione denominato **myAppGateway** con configurazioni per back-end, front-end e HTTP.  

* **appGatewayIP** è definito nel blocco **gateway_ip_configurations**. Un riferimento alla subnet è necessario per la configurazione IP del gateway.
* **appGatewayBackendPool** è definito nel blocco **backend_address_pools**. Un gateway applicazione deve avere almeno un pool di indirizzi back-end.
* **appGatewayBackendHttpSettings** è definito nel blocco **backend_http_settings_collection**. Specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
* **appGatewayHttpListener** è definito nel blocco **backend_http_settings_collection**. È il listener predefinito associato ad appGatewayBackendPool.
* **appGatewayFrontendIP** è definito nel blocco **frontend_ip_configurations**. Assegna myAGPublicIPAddress ad appGatewayHttpListener.
* **rule1** è definito nel blocco **request_routing_rules**. È la regola di routing predefinita associata ad appGatewayHttpListener.

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Salvare questo playbook come *appgw_create.yml*. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook appgw_create.yml
```

Il processo di creazione del gateway applicazione potrebbe richiedere alcuni minuti.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Nel playbook di esempio per le risorse di rete è stato creato il dominio **mydomain** in **eastus**. Passare a `http://mydomain.eastus.cloudapp.azure.com` nel browser. Se viene visualizzata la pagina seguente, il gateway applicazione funziona come previsto.

![Test riuscito di un gateway applicazione funzionante](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Pulire le risorse

Se queste risorse non sono necessarie, è possibile eliminarle eseguendo il codice seguente. Viene cancellato un gruppo di risorse denominato **myResourceGroup**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Salvare questo playbook come *rg_delete*.yml. Per eseguire il playbook, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ansible in Azure](https://docs.microsoft.com/azure/ansible/)
