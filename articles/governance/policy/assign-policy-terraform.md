---
title: 'Avvio rapido: Nuova assegnazione di criteri con Terraform'
description: Questo argomento di avvio rapido descrive come usare Terraform e la sintassi HCL per creare un'assegnazione di criteri per identificare le risorse non conformi.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93104922"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Avvio rapido: Creare un'assegnazione di criteri per identificare le risorse non conformi con Terraform

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse.
Questa guida introduttiva illustra il processo di creazione di un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti.

Alla fine di questo processo, verranno identificate le macchine virtuali che non usano dischi gestiti e che sono quindi _non conformi_ all'assegnazione di criteri.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- [Terraform](https://www.terraform.io/) versione 0.12.0 o successiva configurata nell'ambiente in uso.
  Per le istruzioni, vedere [Configurare Terraform con Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell).
- Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure 2.13.0 o versioni successive. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Creare la configurazione, la variabile e il file di output di Terraform

In questo avvio rapido i crea un'assegnazione di criteri e si assegna la definizione **Controlla macchine virtuali che non usano dischi gestiti** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

In primo luogo, creare la configurazione, la variabile e i file di output di Terraform. Le risorse di Terraform per Criteri di Azure usano il [provider Azure](https://www.terraform.io/docs/providers/azurerm/index.html).

1. Creare una nuova cartella denominata `policy-assignment` e passare alla directory.

1. Creare `main.tf` con il codice seguente:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Creare `variables.tf` con il codice seguente:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   L'ambito determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri Può spaziare da un gruppo di gestione a una singola risorsa. Assicurarsi di sostituire `{scope}` con uno dei criteri seguenti:

   - Sottoscrizione: `/subscriptions/{subscriptionId}`
   - Gruppo di risorse: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Risorsa: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Creare `output.tf` con il codice seguente:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Inizializzare Terraform e creare il piano

A questo punto, inizializzare Terraform per scaricare i provider necessari e quindi creare un piano.

1. Eseguire il comando [terraform init](https://www.terraform.io/docs/commands/init.html). Questo comando scarica i moduli di Azure necessari per creare le risorse di Azure nella configurazione di Terraform.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Screenshot dell'esecuzione del comando terraform init che mostra il download del modulo azurerm e un messaggio di operazione completata.":::

1. Eseguire l'autenticazione con l'[interfaccia della riga di comando di Azure](/cli/azure/) per Terraform. Per altre informazioni, vedere [Provider Azure: Autenticazione con l'interfaccia della riga di comando di Azure](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Creare il piano di esecuzione con il comando [terraform plan](https://www.terraform.io/docs/commands/plan.html) e il parametro **out**.

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Screenshot dell'esecuzione del comando terraform plan con il parametro out per visualizzare la risorsa di Azure che verrà creata.":::

   > [!NOTE]
   > Per informazioni sul salvataggio permanente dei piani di esecuzione e sulla sicurezza, vedere [Terraform Plan: Avviso di sicurezza](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Applicare un piano di esecuzione Terraform

Infine, applicare il piano di esecuzione.

Eseguire il comando [terraform apply](https://www.terraform.io/docs/commands/apply.html) e specificare il piano `assignment.tfplan` precedentemente creato.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Screenshot dell'esecuzione del comando terraform apply e della creazione della risorsa.":::

Il messaggio "Apply complete! Risorse: 1 added, 0 changed, 0 destroyed." indica che l'assegnazione di criteri è stata creata. Poiché è stato definito il file `outputs.tf`, viene restituito anche il valore per _assignment\_id_.

## <a name="identify-non-compliant-resources"></a>Identificare risorse non conformi

Per visualizzare le risorse non sono conformi in questa nuova assegnazione, usare il valore di _assignment\_id_ restituito da `terraform apply`. Eseguire quindi questo comando per ottenere gli ID risorsa delle risorse non conformi restituite in un file JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

I risultati saranno simili all'esempio seguente:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

I risultati sono paragonabili alle informazioni in genere riportate sotto **Non-compliant resources** (Risorse non conformi) nella visualizzazione nel portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere l'assegnazione creata, usare l'interfaccia della riga di comando di Azure o invertire il piano di esecuzione di Terraform con `terraform destroy`.

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri per convalidare la conformità delle nuove risorse, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)
