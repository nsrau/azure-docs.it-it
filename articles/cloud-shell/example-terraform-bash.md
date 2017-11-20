---
title: Distribuire con Terraform con Bash in Azure Cloud Shell | Microsoft Docs
description: Distribuire risorse di Azure con Terraform in Bash
services: Azure
documentationcenter: 
author: tomarcher
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: c75b5d521dc3eacaf5c5921c35442b1afeb4fa13
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="terraform-and-bash-in-cloud-shell"></a>Terraform e Bash in Cloud Shell
Questo articolo descrive in modo dettagliato come creare un gruppo di risorse con il [provider AzureRM di Terraform](https://www.terraform.io/docs/providers/azurerm/index.html). 

Lo strumento open source [Hashicorp Terraform](https://www.terraform.io/) consente di codificare le API in file di configurazione dichiarativi che è possibile condividere tra i membri del team per la modifica, la revisione e il controllo della versione. Il provider Microsoft AzureRM viene usato per interagire con le risorse supportate da Azure Resource Manager tramite le API AzureRM. 

## <a name="automatic-authentication"></a>Autenticazione automatica
Per impostazione predefinita, Terraform viene installato in Bash in Cloud Shell. Cloud Shell esegue inoltre automaticamente l'autenticazione della sottoscrizione dell'interfaccia della riga di comando di Azure 2.0 predefinita per distribuire le risorse tramite i moduli Azure di Terraform.

Terraform usa la sottoscrizione predefinita impostata dell'interfaccia della riga di comando di Azure 2.0. Per aggiornare le sottoscrizioni predefinite, eseguire:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Procedura dettagliata
### <a name="launch-bash-in-cloud-shell"></a>Avviare Bash in Cloud Shell
1. Avviare Cloud Shell dal percorso preferito
2. Verificare che la sottoscrizione predefinita sia impostata

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Creare un modello Terraform
Creare un nuovo modello Terraform denominato main.tf con l'editor di testo preferito.

```
vim main.tf
```

Copiare e incollare il codice seguente in Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Salvare il file e chiudere l'editor di testo.

### <a name="terraform-init"></a>Eseguire il comando terraform init
Per iniziare, eseguire `terraform init`.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Il [comando terraform init](https://www.terraform.io/docs/commands/init.html) viene usato per inizializzare una directory di lavoro che contiene i file di configurazione di Terraform. `terraform init` è il primo comando da eseguire dopo aver scritto una nuova configurazione di Terraform o averne clonata una dal controllo della versione. È consigliabile eseguire questo comando più volte.

### <a name="terraform-plan"></a>Piano Terraform
Per visualizzare in anteprima le risorse che verranno create dal modello Terraform, eseguire `terraform plan`.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

Il [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) viene usato per creare un piano di esecuzione. A meno che non sia disabilitato esplicitamente, Terraform esegue un aggiornamento e determina quali azioni sono necessarie per ottenere lo stato desiderato specificato nei file di configurazione. Il piano può essere salvato con l'opzione -out e quindi fornito a terraform apply per garantire che vengano eseguite solo le azioni pianificate in precedenza.

### <a name="terraform-apply"></a>Comando terraform apply
Per eseguire il provisioning delle risorse di Azure, usare `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Il [comando terraform apply](https://www.terraform.io/docs/commands/apply.html) consente di applicare le modifiche necessarie per raggiungere lo stato desiderato della configurazione.

### <a name="verify-deployment-with-azure-cli-20"></a>Verificare la distribuzione con l'interfaccia della riga di comando di Azure 2.0
Per verificare che il provisioning della risorsa sia stato completato, eseguire `az group show -n myRgName`.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Eseguire la pulizia con terraform destroy
Eseguire la pulizia del gruppo di risorse creato con il [comando terraform destroy](https://www.terraform.io/docs/commands/destroy.html) per pulire l'infrastruttura creata da Terraform.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

La creazione di una risorsa di Azure con Terraform è stata completata. Vedere i passaggi successivi per approfondire la conoscenza di Cloud Shell.

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sul provider Azure di Terraform](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Avvio rapido di Bash in Cloud Shell](quickstart.md)