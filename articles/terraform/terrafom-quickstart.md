---
title: Introduzione a Terraform in Azure.
description: Iniziare a usare Terraform in Azure distribuendo un'istanza di Azure Cosmos DB e Istanze di Azure Container.
services: terraform
author: neilpeterson
ms.service: terraform
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: nepeters
ms.openlocfilehash: 408e07e9b6214dbd223bd4ec7959b00ecc414f1e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869178"
---
# <a name="create-a-terraform-configuration-for-azure"></a>Creare una configurazione Terraform per Azure

In questo esempio, si acquisisce esperienza nella creazione di una configurazione Terraform e nella distribuzione di questa configurazione in Azure. Al termine, saranno state distribuite un'istanza di Azure Cosmos DB, un'istanza di Istanze di Azure Container e un'applicazione che funziona tra queste due risorse. Questo documento presuppone che tutto il lavoro venga completato in Azure Cloud Shell, in cui sono preinstallati gli strumenti di Terraform. Se si vuole usare l'esempio nel proprio sistema, Terraform può essere installato usando le istruzioni riportate [qui](../virtual-machines/linux/terraform-install-configure.md).

## <a name="create-first-configuration"></a>Crea la prima configurazione

In questa sezione si creerà la configurazione per un'istanza di Azure Cosmos DB.

Selezionare **Prova adesso** per aprire Azure Cloud Shell. Una volta aperto, immettere `code .` per aprire l'editor di codice di Cloud Shell.

```azurecli-interactive
code .
```

Copiare e incollare la seguente configurazione di Terraform.

Questa configurazione modella un gruppo di risorse di Azure, un numero intero casuale e un'istanza di Azure Cosmos DB. Il numero intero casuale viene usato nel nome dell'istanza di Cosmos DB. Vengono configurate anche diverse impostazioni di Cosmos DB. Per un elenco completo delle configurazioni di Terraform per Cosmos DB, vedere il [riferimento di Terraform per Cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html).

Al termine, salvare il file come `main.tf`. Questa operazione può essere eseguita usando i puntini di sospensione nella parte superiore destra dell'editor di codice.

```azurecli-interactive
resource "azurerm_resource_group" "vote-resource-group" {
  name     = "vote-resource-group"
  location = "westus"
}

resource "random_integer" "ri" {
  min = 10000
  max = 99999
}

resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
  name                = "tfex-cosmos-db-${random_integer.ri.result}"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  offer_type          = "Standard"
  kind                = "GlobalDocumentDB"

  consistency_policy {
    consistency_level       = "BoundedStaleness"
    max_interval_in_seconds = 10
    max_staleness_prefix    = 200
  }

  geo_location {
    location          = "westus"
    failover_priority = 0
  }
}
```

Il comando [terraform init](https://www.terraform.io/docs/commands/init.html) inizializza la directory di lavoro. Eseguire `terraform init` nel terminale Cloud Shell per preparare la distribuzione della nuova configurazione.

```azurecli-interactive
terraform init
```

Il comando [terraform plan](https://www.terraform.io/docs/commands/plan.html) può essere usato per verificare che la configurazione sia formattata correttamente e per visualizzare quali risorse verranno create, aggiornate o eliminate definitivamente. I risultati possono essere archiviati in un file e usati in un secondo momento per applicare la configurazione.

Eseguire `terraform plan` per testare la nuova configurazione di Terraform.

```azurecli-interactive
terraform plan --out plan.out
```

Applicare la configurazione usando [terraform apply](https://www.terraform.io/docs/commands/apply.html) e specificando il nome del file del piano. Questo comando distribuisce le risorse nella sottoscrizione di Azure.

```azurecli-interactive
terraform apply plan.out
```

Al termine, si noterà che è stato creato il gruppo di risorse e che un'istanza di Azure Cosmos DB è stata inserita nel gruppo di risorse.

## <a name="update-configuration"></a>Aggiornare la configurazione

Aggiornare la configurazione in modo da includere un'istanza di Istanze di Azure Container. Il contenitore esegue un'applicazione che legge e scrive i dati in Cosmos DB.

Copiare la configurazione seguente alla fine del file `main.tf`. Al termine, salvare il file.

Vengono impostate due variabili di ambiente, `COSMOS_DB_ENDPOINT` e `COSMOS_DB_MASTERKEY`. Queste variabili contengono il percorso e la chiave per l'accesso al database. I valori di queste variabili vengono ottenuti dall'istanza del database creata nel passaggio precedente. Questo processo è noto come interpolazione. Per altre informazioni sull'interpolazione di Terraform, vedere l'articolo relativo alla [sintassi di interpolazione](https://www.terraform.io/docs/configuration/interpolation.html).


La configurazione include anche un blocco di output, che restituisce il nome di dominio completo (FQDN) dell'istanza del contenitore.

```azurecli-interactive
resource "azurerm_container_group" "vote-aci" {
  name                = "vote-aci"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  ip_address_type     = "public"
  dns_name_label      = "vote-aci"
  os_type             = "linux"

  container {
    name   = "vote-aci"
    image  = "microsoft/azure-vote-front:cosmosdb"
    cpu    = "0.5"
    memory = "1.5"
    ports  = {
      port     = 80
      protocol = "TCP"
    }

    secure_environment_variables {
      "COSMOS_DB_ENDPOINT"  = "${azurerm_cosmosdb_account.vote-cosmos-db.endpoint}"
      "COSMOS_DB_MASTERKEY" = "${azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key}"
      "TITLE"               = "Azure Voting App"
      "VOTE1VALUE"          = "Cats"
      "VOTE2VALUE"          = "Dogs"
    }
  }
}

output "dns" {
  value = "${azurerm_container_group.vote-aci.fqdn}"
}
```

Eseguire `terraform plan` per creare il piano aggiornato e visualizzare le modifiche da apportare. Si noterà che una risorsa di Azure Container Instances è stata aggiunta alla configurazione.

```azurecli-interactive
terraform plan --out plan.out
```

Eseguire infine `terraform apply` per applicare la configurazione.

```azurecli-interactive
terraform apply plan.out
```

Al termine, prendere nota del nome di dominio completo dell'istanza del contenitore.

## <a name="test-application"></a>Testare l'applicazione

Passare al nome di dominio completo dell'istanza del contenitore. Se la configurazione è stata eseguita correttamente, viene visualizzata la seguente applicazione.

![Applicazione di voto di Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, le risorse di Azure e il gruppo di risorse possono essere rimossi con il comando [terraform destroy](https://www.terraform.io/docs/commands/destroy.html).

```azurecli-interactive
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stata creata, distribuita ed eliminata definitivamente una configurazione di Terraform. Per altre informazioni sull'uso di Terraform in Azure, vedere la documentazione del provider di Terraform per Azure.

> [!div class="nextstepaction"]
> [Provider di Terraform per Azure](https://www.terraform.io/docs/providers/azurerm/)