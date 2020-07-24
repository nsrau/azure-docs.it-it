---
title: Cenni preliminari sulle specifiche del modello
description: Viene descritto come creare specifiche di modello e condividerle con altri utenti nell'organizzazione.
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47dcf44b35ad5c0b77dd0b88d683071a7f2f4ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097727"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Specifiche del modello di Azure Resource Manager (anteprima)

Una specifica del modello è un nuovo tipo di risorsa per l'archiviazione di un modello di Azure Resource Manager (ARM template) in Azure per una distribuzione successiva. Questo tipo di risorsa consente di condividere i modelli ARM con altri utenti dell'organizzazione. Analogamente a qualsiasi altra risorsa di Azure, è possibile usare il controllo degli accessi in base al ruolo (RBAC) per condividere la specifica del modello. gli utenti devono solo accedere in lettura alla specifica del modello per distribuire il modello, in modo da poter condividere il modello senza consentire ad altri utenti di modificarlo.

**Microsoft. resources/templateSpecs** è il nuovo tipo di risorsa per le specifiche del modello. È costituito da un modello principale e da un numero qualsiasi di modelli collegati. Azure archivia in modo sicuro le specifiche del modello nei gruppi di risorse. Le specifiche del modello supportano il [controllo delle versioni](#versioning).

Per distribuire la specifica del modello, si usano strumenti standard di Azure come PowerShell, l'interfaccia della riga di comando di Azure, portale di Azure, REST e altri SDK e client supportati. Si utilizzano gli stessi comandi e si passano gli stessi parametri per il modello.

Il vantaggio di usare le specifiche dei modelli è che i team dell'organizzazione non devono ricreare o copiare i modelli per gli scenari comuni. È possibile creare modelli canonici e condividerli. I modelli inclusi in una specifica del modello devono essere verificati dagli amministratori dell'organizzazione per seguire i requisiti e le linee guida dell'organizzazione.

> [!NOTE]
> Le specifiche del modello sono attualmente in anteprima. Per usarlo, è necessario [iscriversi all'elenco di attesa](https://aka.ms/templateSpecOnboarding).

## <a name="create-template-spec"></a>Crea specifica modello

L'esempio seguente illustra un modello semplice per la creazione di un account di archiviazione in Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

Quando si crea la specifica del modello, ai comandi di PowerShell o dell'interfaccia della riga di comando viene passato il file di modello principale. Se il modello principale fa riferimento a modelli collegati, i comandi li troveranno e li comprimeranno per creare la specifica del modello. Per altre informazioni, vedere [creare una specifica del modello con i modelli collegati](#create-a-template-spec-with-linked-templates).

Creare una specifica del modello usando:

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

È possibile visualizzare tutte le specifiche del modello nella sottoscrizione usando:

```azurepowershell
Get-AzTemplateSpec
```

È possibile visualizzare i dettagli di una specifica del modello, incluse le relative versioni con:

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>Distribuisci specifiche modello

Dopo aver creato la specifica del modello, gli utenti con accesso in **lettura** alla specifica del modello possono distribuirla. Per informazioni sulla concessione dell'accesso, vedere [esercitazione: concedere a un gruppo l'accesso alle risorse di Azure usando Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Le specifiche dei modelli possono essere distribuite tramite il portale, PowerShell, l'interfaccia della riga di comando di Azure o come modello collegato in una distribuzione di modelli più ampia. Gli utenti di un'organizzazione possono distribuire una specifica di modello in qualsiasi ambito in Azure (gruppo di risorse, sottoscrizione, gruppo di gestione o tenant).

Anziché passare un percorso o un URI per un modello, è necessario distribuire una specifica del modello specificando il relativo ID risorsa. Il formato dell'ID risorsa è il seguente:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Si noti che l'ID risorsa include un numero di versione per la specifica del modello.

Ad esempio, si distribuisce una specifica del modello con il comando di PowerShell seguente.

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

In pratica, in genere si esegue `Get-AzTemplateSpec` per ottenere l'ID della specifica del modello che si vuole distribuire.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

## <a name="create-a-template-spec-with-linked-templates"></a>Creare una specifica di modello con i modelli collegati

Se il modello principale per la specifica del modello fa riferimento a modelli collegati, i comandi di PowerShell e dell'interfaccia della riga di comando possono trovare e creare automaticamente il pacchetto dei modelli collegati dall'unità locale. Non è necessario configurare manualmente gli account di archiviazione o i repository per ospitare le specifiche del modello. tutto è indipendente nella risorsa specifica del modello.

L'esempio seguente è costituito da un modello principale con due modelli collegati. L'esempio è solo un estratto del modello. Si noti che usa una proprietà denominata `relativePath` per collegare gli altri modelli. `apiVersion` `2020-06-01` Per la risorsa distribuzioni è necessario usare o versione successiva.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/webapp.json"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/database.json"
                }
            }
        }
    ],
    "outputs": {}
}

```

Quando il comando di PowerShell o dell'interfaccia della riga di comando per creare la specifica del modello viene eseguito per l'esempio precedente, il comando trova tre file, il modello principale, il modello di app Web ( `webapp.json` ) e il modello di database ( `database.json` ) e li inserisce nella specifica del modello.

Per altre informazioni, vedere [esercitazione: creare una specifica di modello con i modelli collegati](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Distribuire la specifica del modello come modello collegato

Dopo aver creato una specifica del modello, è facile riutilizzarla da un modello ARM o da un'altra specifica del modello. È possibile collegarsi a una specifica del modello aggiungendo il relativo ID di risorsa al modello. La specifica del modello collegato viene distribuita automaticamente quando si distribuisce il modello principale. Questo comportamento consente di sviluppare specifiche di modelli modulari e riutilizzarle in base alle esigenze.

Ad esempio, è possibile creare una specifica del modello che distribuisce le risorse di rete e un'altra specifica di modello che distribuisce le risorse di archiviazione. Nei modelli ARM, è possibile collegarsi a queste due specifiche del modello ogni volta che è necessario configurare le risorse di rete o di archiviazione.

L'esempio seguente è simile all'esempio precedente, ma si usa la `id` proprietà per eseguire il collegamento a una specifica del modello anziché alla `relativePath` proprietà per eseguire il collegamento a un modello locale. Usare `2020-06-01` per la versione API per la risorsa distribuzioni. Nell'esempio le specifiche del modello si trovano in un gruppo di risorse denominato **templateSpecsRG**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "networkingDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "storageDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                }
            }
        }
    ],
    "outputs": {}
}
```

Per altre informazioni sul collegamento delle specifiche dei modelli, vedere [esercitazione: distribuire una specifica di modello come modello collegato](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Controllo delle versioni

Quando si crea una specifica del modello, è necessario specificare un numero di versione. Quando si esegue l'iterazione del codice del modello, è possibile aggiornare una versione esistente (per gli hotfix) o pubblicare una nuova versione. La versione è una stringa di testo. È possibile scegliere di seguire qualsiasi sistema di controllo delle versioni, incluso il controllo delle versioni semantico. Gli utenti della specifica del modello possono fornire il numero di versione che si desidera utilizzare per la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* Per creare e distribuire una specifica del modello, vedere [Guida introduttiva: creare e distribuire una specifica del modello](quickstart-create-template-specs.md).

* Per altre informazioni sul collegamento dei modelli nelle specifiche del modello, vedere [esercitazione: creare una specifica del modello con i modelli collegati](template-specs-create-linked.md).

* Per altre informazioni sulla distribuzione di una specifica di modello come modello collegato, vedere [esercitazione: distribuire una specifica di modello come modello collegato](template-specs-deploy-linked-template.md).
