---
title: 'Esercitazione: Creare un cluster Kubernetes con il servizio Azure Kubernetes usando Terraform'
description: Esercitazione che illustra come creare un cluster Kubernetes con il servizio Azure Kubernetes e Terraform
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 792c075cfb40eb4904a30b63e9902a59ceda9bc1
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159308"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Esercitazione: Creare un cluster Kubernetes con il servizio Azure Kubernetes usando Terraform

Il [servizio Azure Kubernetes](/azure/aks/) gestisce l'ambiente Kubernetes ospitato, che consente di distribuire e gestire applicazioni in contenitori senza avere competenze nell'orchestrazione di contenitori. Permette inoltre di eseguire molte operazioni di manutenzione comuni senza portare offline l'app, tra cui il provisioning, l'aggiornamento e il ridimensionamento delle risorse su richiesta.

In questa esercitazione si apprenderà come eseguire le attività seguenti:

> [!div class="checklist"]
> * Usare HCL (linguaggio HashiCorp) per definire un cluster Kubernetes
> * Usare Terraform e servizio Azure Kubernetes per creare un cluster Kubernetes
> * Usare lo strumento kubectl per verificare la disponibilità di un cluster Kubernetes

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Configurare Terraform**: Seguire le indicazioni nell'articolo su [Terraform e configurazione dell'accesso ad Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Entità servizio di Azure**: seguire le indicazioni riportate nella sezione relativa a **Creare l'entità servizio** nell'articolo [Creare entità servizio di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Prendere nota dei valori di appId, displayName, password e tenant.

## <a name="create-the-directory-structure"></a>Creare la struttura di directory

Il primo passaggio consiste nel creare la directory che contiene i file di configurazione Terraform per l'esercitazione.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire [Azure Cloud Shell](/azure/cloud-shell/overview). Se in precedenza non è stato selezionato un ambiente, selezionare **Bash** come ambiente.

    ![Prompt di Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Passare alla directory `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crea una directory denominata `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Passare alla nuova directory:

    ```bash
    cd terraform-aks-k8s
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
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

## <a name="define-a-kubernetes-cluster"></a>Definire un cluster Kubernetes

Creare il file di configurazione Terraform che dichiara le risorse per il cluster Kubernetes.

1. In Cloud Shell creare un file denominato `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Incollare il codice seguente nell'editor:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        agent_pool_profile {
            name            = "agentpool"
            count           = var.agent_count
            vm_size         = "Standard_DS1_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Il codice precedente imposta il nome del cluster, la posizione e il nome del gruppo di risorse. Viene anche impostato il prefisso per il nome di dominio completo. Il nome di dominio completo viene usato per accedere al cluster.

    Il record `linux_profile` permette di configurare le impostazioni che consentono di accedere ai nodi di lavoro con SSH.

    Con servizio Azure Kubernetes si pagano solo i nodi di lavoro. Il record `agent_pool_profile` configura i dettagli di questi nodi di lavoro. Il record `agent_pool_profile record` include il numero e il tipo di nodi di lavoro da creare. Se in futuro sarà necessario aumentare o ridurre il cluster, modificare il valore `count` di questo record.

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

## <a name="declare-the-variables"></a>Dichiarare le variabili

1. In Cloud Shell creare un file denominato `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Incollare il codice seguente nell'editor:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

## <a name="create-a-terraform-output-file"></a>Creare un file di output Terraform

Gli [output di Terraform](https://www.terraform.io/docs/configuration/outputs.html) consentono di definire i valori che verranno evidenziati all'utente quando Terraform applica un piano. È possibile eseguire query su di essi con il comando `terraform output`. In questa sezione si crea un file di output che consente l'accesso al cluster con [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

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
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Impostare l'archivio di Azure per l'archiviazione dello stato Terraform

Terraform tiene traccia dello stato localmente tramite il file `terraform.tfstate`. Questo modello funziona bene in un ambiente con una sola persona. In un ambiente con più persone, l'[account di archiviazione di Azure](/azure/storage/) viene usato per tenere traccia dello stato.

In questa sezione viene illustrato come eseguite le attività seguenti:
- Recuperare informazioni sull'account di archiviazione (nome e chiave dell'account)
- Creare un contenitore di archiviazione in cui verranno archiviate le informazioni sullo stato di Terraform.

1. Nel portale di Azure selezionare **Tutti i servizi** nel menu a sinistra.

1. Selezionare **Account di archiviazione**.

1. Nella scheda **Account di archiviazione** selezionare il nome dell'account di archiviazione in cui Terraform dovrà archiviare lo stato. È ad esempio possibile usare l'account di archiviazione creato quando è stato aperto Cloud Shell per la prima volta.  Il nome dell'account di archiviazione creato da Cloud Shell inizia generalmente con `cs`, seguito da una stringa casuale di numeri e lettere. Prendere nota dell'account di archiviazione selezionato. Questo valore sarà necessario in un secondo momento.

1. Nella scheda dell'account di archiviazione selezionare **Chiavi di accesso**.

    ![Menu dell'account di archiviazione](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Prendere nota del valore **chiave** **key1**. Selezionando l'icona a destra della chiave, il valore verrà copiato negli Appunti.

    ![Chiavi di accesso dell'account di archiviazione](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. In Cloud Shell creare un contenitore nell'account di archiviazione di Azure. Sostituire i segnaposto con i valori appropriati per l'ambiente.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Creare il cluster Kubernetes

Questa sezione illustra come usare il comando `terraform init` per creare le risorse definite nei file di configurazione creati nelle sezioni precedenti.

1. In Cloud Shell inizializzare Terraform. Sostituire i segnaposto con i valori appropriati per l'ambiente.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Il comando `terraform init` visualizza l'esito positivo dell'inizializzazione del plug-in back-end e del provider:

    ![Esempio di risultati di "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Esportare le credenziali dell'entità servizio. Sostituire i segnaposto con i valori appropriati per l'entità servizio.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Eseguire il comando `terraform plan` per creare il piano Terraform che definisce gli elementi dell'infrastruttura. 

    ```bash
    terraform plan -out out.plan
    ```

    Il comando `terraform plan` visualizza le risorse che verranno create quando si esegue il comando `terraform apply`:

    ![Esempio di risultati di "terraform plan"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Eseguire il comando `terraform apply` per applicare il piano per la creazione del cluster Kubernetes. Il processo di creazione di un cluster Kubernetes può richiedere diversi minuti, con il conseguente timeout della sessione di Cloud Shell. In caso di timeout della sessione di Cloud Shell, è possibile seguire la procedura descritta nella sezione "Ripristino da un timeout di Cloud Shell" per completare l'esercitazione.

    ```bash
    terraform apply out.plan
    ```

    Il comando `terraform apply` visualizza i risultati della creazione delle risorse definite nei file di configurazione:

    ![Esempio di risultati di "terraform apply"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Nel portale di Azure selezionare **Tutte le risorse** nel menu a sinistra per visualizzare le risorse create per il nuovo cluster Kubernetes.

    ![Tutte le risorse nel portale di Azure](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

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

    ![Lo strumento kubectl consente di verificare l'integrità del cluster Kubernetes](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Monitoraggio di stato e log

Quando è stato creato il cluster del servizio Azure Kubernetes, è stato abilitato il monitoraggio per acquisire le metriche di integrità sia per i nodi del cluster che per i pod. Queste metriche di integrità sono disponibili nel portale di Azure. Per altre informazioni sul monitoraggio dell'integrità dei contenitori, vedere [Monitorare l'integrità del servizio Azure Kubernetes](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Vedere altre informazioni sull'uso di Terraform in Azure](/azure/terraform)