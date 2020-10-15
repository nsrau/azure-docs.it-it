---
title: "Esercitazione: Effettuare il provisioning di un'istanza di Azure Spring Cloud con Terraform"
description: Come effettuare il provisioning di un'istanza di Azure Spring Cloud con Terraform.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 4ef08ea03cab711901225cab227a0f5f84055d7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906812"
---
# <a name="tutorial-provision-an-azure-spring-cloud-instance-with-terraform"></a>Esercitazione: Effettuare il provisioning di un'istanza di Azure Spring Cloud con Terraform

**Questo articolo si applica a:** ✔️ Java ✔️ C#

In questa esercitazione viene creata un'istanza di Azure Spring Cloud con Terraform. Seguendo le procedure, si creeranno le risorse seguenti:

> [!div class="checklist"]
> * Gruppo di risorse
> * Istanza di Azure Spring Cloud
> * Archiviazione di Azure per Log Analytics

> [!NOTE]
> Per il supporto specifico di Terraform, usare uno dei canali di supporto della community di HashiCorp:
>
> * Domande, casi d'uso e modelli utili: [Sezione del portale della community di HashiCorp relativa a Terraform](https://discuss.hashicorp.com/c/terraform-core)
> * Domande correlate al provider: [Sezione del portale della community di HashiCorp relativa ai provider di Terraform](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="create-configuration-file"></a>Creare il file di configurazione

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Aprire [Azure Cloud Shell](https://docs.microsoft.com/azure/app-service/quickstart-java#use-azure-cloud-shell).

1. Avviare l'editor di Cloud Shell:

    ```bash
    code main.tf
    ```

1. La configurazione in questo passaggio modella le risorse di Azure, tra cui un gruppo di risorse di Azure e un'istanza di Azure Spring Cloud.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

## <a name="apply-the-configuration"></a>Applicare la configurazione

In questa sezione vengono usati numerosi comandi di Terraform per eseguire la configurazione.

1. Il comando [terraform init](https://www.terraform.io/docs/commands/init.html) inizializza la directory di lavoro. Eseguire il comando seguente in Cloud Shell:

    ```bash
    terraform init
    ```

1. Il comando [terraform plan](https://www.terraform.io/docs/commands/plan.html) viene usato per convalidare la sintassi della configurazione. Il parametro `-out` indirizza i risultati a un file. Il file di output può essere usato in un secondo momento per applicare la configurazione. Eseguire il comando seguente in Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. Per applicare la configurazione, viene usato il comando [terraform apply](https://www.terraform.io/docs/commands/apply.html). Questo comando specifica il file di output del passaggio precedente. Crea le risorse di Azure. Eseguire il comando seguente in Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Per verificare i risultati all'interno del portale di Azure, passare al nuovo gruppo di risorse. La nuova istanza di **Azure Spring Cloud** viene visualizzata nel nuovo gruppo di risorse.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Aggiornare la configurazione ai log e alle metriche di configurazione

Questa sezione illustra come aggiornare la configurazione per abilitare i log e le metriche per Azure Spring Cloud con un account di archiviazione di Azure.

1. Avviare l'editor di Cloud Shell:

    ```bash
    code main.tf
    ```

1. Aggiungere la configurazione seguente alla fine del file:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Salvare il file ( **&lt;CTRL+S**) e uscire dall'editor ( **&lt;CTRL+Q**).

1. Come nella sezione precedente, eseguire il comando seguente per apportare le modifiche:

    ```bash
    terraform plan --out plan.out
    ```

1. Eseguire il comando `terraform apply` per applicare la configurazione.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo.

Eseguire il comando [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) per rimuovere le risorse di Azure create in questa esercitazione:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Installare e configurare Terraform per il provisioning delle risorse di Azure](https://docs.microsoft.com/azure/developer/terraform/getting-started-cloud-shell).
