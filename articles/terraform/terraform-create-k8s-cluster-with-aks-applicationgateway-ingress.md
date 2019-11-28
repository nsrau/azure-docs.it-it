---
title: 'Esercitazione: Creare un controller del traffico in ingresso del gateway applicazione nel servizio Azure Kubernetes'
description: Esercitazione che illustra come creare un cluster Kubernetes con il servizio Azure Kubernetes con il gateway applicazione come controller di ingresso
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: 6d07fc6becf76453de792c69b25aea49c39775ae
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159103"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Esercitazione: Creare un controller di ingresso del gateway applicazione nel servizio Azure Kubernetes

Il [servizio Azure Kubernetes](/azure/aks/) gestisce l'ambiente Kubernetes ospitato, consentendo di distribuire e gestire applicazioni in contenitori in modo semplice e rapido senza competenze nell'orchestrazione di contenitori. Elimina inoltre la necessità di portare offline le applicazioni per le attività operative e di manutenzione. Con il servizio Azure Kubernetes queste attività, incluse le attività di provisioning, aggiornamento e ridimensionamento delle risorse, possono essere eseguite su richiesta.

Un controller di ingresso fornisce varie funzionalità per i servizi Kubernetes. Queste funzionalità includono il proxy inverso, il routing del traffico configurabile e la terminazione TLS. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso, un singolo indirizzo IP può instradare il traffico a più servizi in un cluster Kubernetes. Tutte queste funzionalità vengono fornite dal [gateway applicazione](/azure/Application-Gateway/) di Azure, che è quindi un controller di ingresso ideale per Kubernetes in Azure. 

In questa esercitazione si apprenderà come eseguire le attività seguenti:

> [!div class="checklist"]
> * Creare un cluster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) usando il servizio Azure Kubernetes con il gateway applicazione come controller di ingresso.
> * Usare HCL (linguaggio HashiCorp) per definire un cluster Kubernetes.
> * Usare Terraform per creare una risorsa gateway applicazione.
> * Usare Terraform e il servizio Azure Kubernetes per creare un cluster Kubernetes.
> * Usare lo strumento kubectl per verificare la disponibilità di un cluster Kubernetes.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Configurare Terraform**: Seguire le indicazioni nell'articolo su [Terraform e configurazione dell'accesso ad Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Gruppo di risorse di Azure**: se non è già disponibile, [creare un gruppo di risorse di Azure](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups) da usare per la demo. Prendere nota del nome e della posizione del gruppo di risorse perché questi valori vengono usati nella demo.

- **Entità servizio di Azure**: seguire le indicazioni riportate nella sezione relativa a **Creare l'entità servizio** nell'articolo [Creare entità servizio di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Prendere nota dei valori per appId, displayName e password.

- **Ottenere l'ID oggetto dell'entità servizio**: Eseguire il comando seguente in Cloud Shell: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Creare la struttura di directory

Il primo passaggio consiste nel creare la directory che contiene i file di configurazione Terraform per l'esercitazione.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Passare alla directory `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crea una directory denominata `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Passare alla nuova directory:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Dichiarare il provider di Azure

Creare il file di configurazione Terraform che dichiara il provider di Azure.

1. In Cloud Shell creare un file denominato `main.tf`.

    ```bash
    code main.tf
    ```

1. Incollare il codice seguente nell'editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

## <a name="define-input-variables"></a>Definire le variabili di input

Creare il file di configurazione di Terraform in cui sono elencate tutte le variabili necessarie per questa distribuzione.

1. In Cloud Shell creare un file denominato `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Incollare il codice seguente nell'editor:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = "map"

      default = {
        source = "terraform"
      }
    }
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

## <a name="define-the-resources"></a>Definire le risorse 
Creare il file di configurazione Terraform che crea tutte le risorse. 

1. In Cloud Shell creare un file denominato `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Incollare il blocco di codice seguente per creare un blocco di variabili locali per le variabili calcolate da riutilizzare:

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. Incollare il blocco di codice seguente per creare un'origine dati per il gruppo di risorse, nuova identità utente:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Incollare il blocco di codice seguente per creare le risorse di rete di base:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Incollare il blocco di codice seguente per creare la risorsa gateway applicazione:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Incollare il blocco di codice seguente per creare le assegnazioni di ruolo:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Incollare il blocco di codice seguente per creare il cluster Kubernetes:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      agent_pool_profile {
        name            = "agentpool"
        count           = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_type         = "Linux"
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

Il codice presentato in questa sezione imposta il nome del cluster, la posizione (location) e il nome del gruppo di risorse (resource_group_name). Viene impostato il valore `dns_prefix`, che fa parte del nome di dominio completo (FQDN) usato per accedere al cluster.

Il record `linux_profile` permette di configurare le impostazioni che consentono di accedere ai nodi di lavoro con SSH.

Con servizio Azure Kubernetes si pagano solo i nodi di lavoro. Il record `agent_pool_profile` configura i dettagli di questi nodi di lavoro. Il record `agent_pool_profile record` include il numero e il tipo di nodi di lavoro da creare. Se in futuro sarà necessario aumentare o ridurre il cluster, modificare il valore `count` di questo record.

## <a name="create-a-terraform-output-file"></a>Creare un file di output Terraform

Gli [output di Terraform](https://www.terraform.io/docs/configuration/outputs.html) consentono di definire i valori che vengono evidenziati all'utente quando Terraform applica un piano e sui quali è possibile eseguire query con il comando `terraform output`. In questa sezione si crea un file di output che consente l'accesso al cluster con [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. In Cloud Shell creare un file denominato `output.tf`.

    ```bash
    code output.tf
    ```

1. Incollare il codice seguente nell'editor:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Configurare l'archiviazione di Azure per archiviare lo stato di Terraform

Terraform tiene traccia dello stato localmente tramite il file `terraform.tfstate`. Questo modello funziona bene in un ambiente con una sola persona. In un ambiente più realistico con più persone, è necessario tenere traccia dello stato nel server usando l'[archiviazione di Azure](/azure/storage/). In questa sezione si apprenderà come recuperare le informazioni necessarie sull'account di archiviazione e creare un contenitore di archiviazione. Le informazioni sullo stato di Terraform vengono quindi archiviate in quel contenitore.

1. Nel portale di Azure, in **servizi di Azure**, selezionare **Account di archiviazione**. Se l'opzione **Account di archiviazione** non è visibile nella pagina principale, selezionare **Altri servizi** e quindi individuarla e selezionarla.

1. Nella pagina **Account di archiviazione** selezionare il nome dell'account di archiviazione in cui Terraform dovrà archiviare lo stato. È ad esempio possibile usare l'account di archiviazione creato quando è stato aperto Cloud Shell per la prima volta.  Il nome dell'account di archiviazione creato da Cloud Shell inizia generalmente con `cs`, seguito da una stringa casuale di numeri e lettere. 

    Prendere nota dell'account di archiviazione selezionato, in quanto servirà in un secondo momento.

1. Nella pagina dell'account di archiviazione selezionare **Chiavi di accesso**.

    ![Menu dell'account di archiviazione](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Prendere nota del valore **chiave** **key1**. Selezionando l'icona a destra della chiave, il valore verrà copiato negli Appunti.

    ![Chiavi di accesso dell'account di archiviazione](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. In Cloud Shell creare un contenitore nell'account di archiviazione di Azure. Sostituire i segnaposto con i valori appropriati per l'account di archiviazione.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Creare il cluster Kubernetes
Questa sezione illustra come usare il comando `terraform init` per creare le risorse definite nei file di configurazione creati nelle sezioni precedenti.

1. In Cloud Shell inizializzare Terraform. Sostituire i segnaposto con i valori appropriati per l'account di archiviazione.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    Il comando `terraform init` visualizza l'esito positivo dell'inizializzazione del plug-in back-end e del provider:

    ![Esempio di risultati di "terraform init"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. In Cloud Shell creare un file denominato `terraform.tfvars`:

    ```bash
    code terraform.tfvars
    ```

1. Incollare nell'editor le variabili seguenti create in precedenza. Per ottenere il valore della posizione per l'ambiente, usare `az account list-locations`.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

1. Eseguire il comando `terraform plan` per creare il piano Terraform che definisce gli elementi dell'infrastruttura. 

    ```bash
    terraform plan -out out.plan
    ```

    Il comando `terraform plan` visualizza le risorse che vengono create quando si esegue il comando `terraform apply`:

    ![Esempio di risultati di "terraform plan"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Eseguire il comando `terraform apply` per applicare il piano per la creazione del cluster Kubernetes. Il processo di creazione di un cluster Kubernetes può richiedere diversi minuti, con il conseguente timeout della sessione di Cloud Shell. In caso di timeout della sessione di Cloud Shell, è possibile seguire la procedura descritta nella sezione "Ripristino da un timeout di Cloud Shell" per completare l'esercitazione.

    ```bash
    terraform apply out.plan
    ```

    Il comando `terraform apply` visualizza i risultati della creazione delle risorse definite nei file di configurazione:

    ![Esempio di risultati di "terraform apply"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Nel portale di Azure selezionare **Gruppi di risorse** nel menu a sinistra per visualizzare le risorse create per il nuovo cluster Kubernetes nel gruppo di risorse selezionato.

    ![Prompt di Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Ripristino da un timeout di Cloud Shell

In caso di timeout della sessione di Cloud Shell, è possibile eseguire questi passaggi per il ripristino:

1. Avviare una sessione di Cloud Shell.

1. Passare alla directory contenente i file di configurazione Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Eseguire il comando seguente:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>Testare il cluster Kubernetes
È possibile usare gli strumenti Kubernetes per verificare il cluster appena creato.

1. Ottenere la configurazione Kubernetes dallo stato Terraform e archiviarla in un file che possa essere letto da kubectl.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Impostare una variabile di ambiente in modo che kubectl selezioni la configurazione corretta.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Verificare l'integrità del cluster.

    ```bash
    kubectl get nodes
    ```

    Saranno visibili i dettagli dei nodi di lavoro e tutti i nodi avranno lo stato **Ready** come mostrato nell'immagine seguente:

    ![Lo strumento kubectl consente di verificare l'integrità del cluster Kubernetes](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Installare l'identità dei pod di Azure AD

L'identità dei pod di Azure Active Directory fornisce l'accesso basato su token ad [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

L'[identità dei pod di Azure AD](https://github.com/Azure/aad-pod-identity) aggiunge i componenti seguenti al cluster Kubernetes:

  - [Definizioni di risorse personalizzate](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) di Kubernetes: `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
  - Componente [Controller identità gestita](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - Componente [Identità gestita del nodo](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Se il controllo degli accessi in base al ruolo è **abilitato**, eseguire il comando seguente per installare l'identità dei pod di Azure AD nel cluster:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Se il controllo degli accessi in base al ruolo è **disabilitato**, eseguire il comando seguente per installare l'identità dei pod di Azure AD nel cluster:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Installare Helm

Il codice descritto in questa sezione usa [Helm](/azure/aks/kubernetes-helm), l'utilità di gestione pacchetti di Kubernetes, per installare il pacchetto `application-gateway-kubernetes-ingress`:

1. Se il controllo degli accessi in base al ruolo è **abilitato**, eseguire il set di comandi seguente per installare e configurare Helm:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Se il controllo degli accessi in base al ruolo è **disabilitato**, eseguire il comando seguente per installare e configurare Helm:

    ```bash
    helm init
    ```

1. Aggiungere il repository Helm AGIC:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Installare il grafico Helm del controller di ingresso

1. Scaricare `helm-config.yaml` per configurare AGIC:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Modificare il file `helm-config.yaml` e immettere i valori appropriati per le sezioni `appgw` e `armAuth`.

    ```bash
    code helm-config.yaml
    ```

    Ecco le descrizioni dei valori:

    - `verbosityLevel`: imposta il livello di dettaglio dell'infrastruttura di registrazione di AGIC. Per i valori possibili, vedere [Livelli di registrazione](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels).
    - `appgw.subscriptionId`: ID sottoscrizione di Azure per il gateway applicazione. Esempio: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: nome del gruppo di risorse di Azure in cui è stato creato il gateway applicazione. 
    - `appgw.name`: nome del gateway applicazione. Esempio: `applicationgateway1`.
    - `appgw.shared`: il valore predefinito di questo flag booleano deve essere `false`. Impostarlo su `true` se è necessario un [gateway applicazione condiviso](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: specificare lo spazio dei nomi che AGIC deve controllare. Lo spazio dei nomi può essere un singolo valore stringa o un elenco delimitato da virgole di spazi dei nomi. Se si lascia questa variabile come commento o se la si imposta su una stringa vuota, il controller del traffico in ingresso osserverà tutti gli spazi dei nomi accessibili.
    - `armAuth.type`: valore `aadPodIdentity` o `servicePrincipal`.
    - `armAuth.identityResourceID`: ID risorsa dell'identità gestita.
    - `armAuth.identityClientId`: ID client dell'identità.
    - `armAuth.secretJSON`: obbligatorio solo quando si sceglie il tipo Segreto entità servizio (quando `armAuth.type` è impostato su `servicePrincipal`).

    Note chiave:
    - Il valore `identityResourceID` viene creato nello script terraform ed è possibile trovarlo eseguendo `echo "$(terraform output identity_client_id)"`.
    - Il valore `identityClientID` viene creato nello script terraform ed è possibile trovarlo eseguendo `echo "$(terraform output identity_resource_id)"`.
    - Il valore `<resource-group>` è il gruppo di risorse del gateway applicazione.
    - Il valore `<identity-name>` è il nome dell'identità creata.
    - È possibile elencare tutte le identità per una determinata sottoscrizione usando `az identity list`.

1. Installare il pacchetto del controller di ingresso del gateway applicazione:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Installare un'app di esempio

Dopo aver installato il gateway applicazione, il servizio Azure Kubernetes e AGIC, è possibile installare un'app di esempio tramite [Azure Cloud Shell](https://shell.azure.com/):

1. Usare il comando curl per scaricare il file YAML:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Applicare il file YAML:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo.  

Sostituire il segnaposto con il valore appropriato. Tutte le risorse nel gruppo di risorse specificato verranno eliminate.

```bash
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Controller di ingresso del gateway applicazione](https://azure.github.io/application-gateway-kubernetes-ingress/)