---
title: Esercitazione - Gestire il traffico Web con il gateway applicazione di Azure con Ansible
description: Informazioni su come usare Ansible per la creazione e la configurazione di un gateway applicazione di Azure per gestire il traffico Web
keywords: ansible, azure, devops, bash, playbook, gateway applicazione, bilanciamento del carico, traffico Web
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1dd547fb59a41a90de18d595a392b64ef518023a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241878"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Esercitazione: Gestire il traffico Web con il gateway applicazione di Azure tramite Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

Il [gateway applicazione di Azure](/azure/application-gateway/overview) è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web. In base all'indirizzo IP e alla porta di origine, gli strumenti tradizionali di bilanciamento del carico instradano il traffico a un indirizzo IP e a una porta di destinazione. Il gateway applicazione offre un livello di controllo maggiore, consentendo di instradare il traffico in base all'URL. Ad esempio, è possibile stabilire che, se `images` è il percorso dell'URL, il traffico deve essere instradato a un set specifico di server (noto come pool) configurato per le immagini.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurare una rete
> * Creare due Istanze di Azure Container con immagini HTTPD
> * Creare un gateway applicazione utilizzabile con le Istanze di Azure Container nel pool di server

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il codice del playbook in questa sezione crea un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico in cui vengono configurate le risorse di Azure.  

Salvare il playbook seguente come `rg.yml`:

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

Prima di eseguire il playbook, vedere le note seguenti:

- Il nome del gruppo di risorse è `myResourceGroup`. Questo valore viene usato in tutta l'esercitazione.
- Il gruppo di risorse viene creato nella posizione `eastus`.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Creare risorse di rete

Il codice del playbook in questa sezione crea una rete virtuale per consentire al gateway applicazione di comunicare con altre risorse.

Salvare il playbook seguente come `vnet_create.yml`:

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

Prima di eseguire il playbook, vedere le note seguenti:

* La sezione `vars` contiene i valori usati per creare le risorse di rete. 
* Sarà necessario modificare questi valori per l'ambiente specifico.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Creare server

Il codice del playbook in questa sezione crea due Istanze di Azure Container con le immagini HTTPD da usare come server Web per il gateway applicazione.  

Salvare il playbook seguente come `aci_create.yml`:

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

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Il codice del playbook in questa sezione crea un gateway applicazione denominato `myAppGateway`.  

Salvare il playbook seguente come `appgw_create.yml`:

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

Prima di eseguire il playbook, vedere le note seguenti:

* Il valore `appGatewayIP` è definito nel blocco `gateway_ip_configurations`. Un riferimento alla subnet è necessario per la configurazione IP del gateway.
* Il valore `appGatewayBackendPool` è definito nel blocco `backend_address_pools`. Un gateway applicazione deve avere almeno un pool di indirizzi back-end.
* Il valore `appGatewayBackendHttpSettings` è definito nel blocco `backend_http_settings_collection`. Specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
* Il valore `appGatewayHttpListener` è definito nel blocco `backend_http_settings_collection`. È il listener predefinito associato ad appGatewayBackendPool.
* Il valore `appGatewayFrontendIP` è definito nel blocco `frontend_ip_configurations`. Assegna myAGPublicIPAddress ad appGatewayHttpListener.
* Il valore `rule1` è definito nel blocco `request_routing_rules`. È la regola di routing predefinita associata ad appGatewayHttpListener.

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook appgw_create.yml
```

Il processo di creazione del gateway applicazione potrebbe richiedere alcuni minuti.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Nella sezione [Creare un gruppo di risorse](#create-a-resource-group) si specifica la posizione. Prendere nota del valore.

1. Nella sezione [Creare le risorse di rete](#create-network-resources) si specifica il dominio. Prendere nota del valore.

1. Per l'URL di test sostituire il modello seguente con la posizione e il dominio specifici: `http://<domain>.<location>.cloudapp.azure.com`.

1. Passare all'URL di test.

1. Se viene visualizzata la pagina seguente, il gateway applicazione funziona come previsto.

    ![Test riuscito di un gateway applicazione funzionante](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Aggiungere il codice seguente come `cleanup.yml`:

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

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ansible in Azure](/azure/ansible/)