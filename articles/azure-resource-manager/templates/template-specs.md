---
title: Cenni preliminari sulle specifiche del modello
description: Viene descritto come creare specifiche di modello e condividerle con altri utenti nell'organizzazione.
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: b0dfc41bddccc6b5c5c924168044cffc0aa5e2b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728472"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Specifiche del modello di Azure Resource Manager (anteprima)

Una specifica del modello è un tipo di risorsa per l'archiviazione di un modello di Azure Resource Manager (modello ARM) in Azure per una distribuzione successiva. Questo tipo di risorsa consente di condividere i modelli ARM con altri utenti dell'organizzazione. Analogamente a qualsiasi altra risorsa di Azure, è possibile usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per condividere la specifica del modello.

**Microsoft. resources/templateSpecs** è il tipo di risorsa per le specifiche del modello. È costituito da un modello principale e da un numero qualsiasi di modelli collegati. Azure archivia in modo sicuro le specifiche del modello nei gruppi di risorse. Le specifiche del modello supportano il [controllo delle versioni](#versioning).

Per distribuire la specifica del modello, si usano strumenti standard di Azure come PowerShell, l'interfaccia della riga di comando di Azure, portale di Azure, REST e altri SDK e client supportati. Usare gli stessi comandi come per il modello.

> [!NOTE]
> La funzionalità Specifiche di modello è attualmente in anteprima. Per usarla, è necessario [iscriversi alla lista di attesa](https://aka.ms/templateSpecOnboarding).

## <a name="why-use-template-specs"></a>Perché usare le specifiche del modello?

Se i modelli sono attualmente presenti in un repository GitHub o in un account di archiviazione, si verificano diverse esigenze durante il tentativo di condividere e usare i modelli. Per la distribuzione da parte di un utente, il modello deve essere locale o l'URL del modello deve essere accessibile pubblicamente. Per aggirare questa limitazione, è possibile condividere copie del modello con gli utenti che devono distribuirlo o aprire l'accesso al repository o all'account di archiviazione. Quando gli utenti possiedono copie locali di un modello, queste copie possono divergere dal modello originale. Quando si rende accessibile pubblicamente un repository o un account di archiviazione, è possibile consentire agli utenti non intenzionati di accedere al modello.

Il vantaggio di usare le specifiche dei modelli è che è possibile creare modelli canonici e condividerli con i team dell'organizzazione. Le specifiche dei modelli sono sicure perché sono disponibili per Azure Resource Manager per la distribuzione, ma non sono accessibili agli utenti senza autorizzazione RBAC di Azure. Gli utenti devono solo accedere in lettura alla specifica del modello per distribuire il modello, in modo da poter condividere il modello senza consentire ad altri utenti di modificarlo.

I modelli inclusi in una specifica del modello devono essere verificati dagli amministratori dell'organizzazione per seguire i requisiti e le linee guida dell'organizzazione.

## <a name="create-template-spec"></a>Creare la specifica di modello

L'esempio seguente illustra un modello semplice per la creazione di un account di archiviazione in Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

Quando si crea la specifica del modello, ai comandi di PowerShell o dell'interfaccia della riga di comando viene passato il file di modello principale. Se il modello principale fa riferimento a modelli collegati, i comandi li troveranno e li comprimeranno per creare la specifica del modello. Per altre informazioni, vedere [creare una specifica del modello con i modelli collegati](#create-a-template-spec-with-linked-templates).

Creare una specifica del modello usando:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

È possibile visualizzare tutte le specifiche del modello nella sottoscrizione usando:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

È possibile visualizzare i dettagli di una specifica del modello, incluse le relative versioni con:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0"
```

---

## <a name="deploy-template-spec"></a>Distribuire la specifica di modello

Dopo aver creato la specifica del modello, gli utenti con accesso in **lettura** alla specifica del modello possono distribuirla. Per informazioni sulla concessione dell'accesso, vedere [esercitazione: concedere a un gruppo l'accesso alle risorse di Azure usando Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Le specifiche dei modelli possono essere distribuite tramite il portale, PowerShell, l'interfaccia della riga di comando di Azure o come modello collegato in una distribuzione di modelli più ampia. Gli utenti di un'organizzazione possono distribuire una specifica di modello in qualsiasi ambito in Azure (gruppo di risorse, sottoscrizione, gruppo di gestione o tenant).

Anziché passare un percorso o un URI per un modello, è necessario distribuire una specifica del modello specificando il relativo ID risorsa. Il formato dell'ID risorsa è il seguente:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Si noti che l'ID risorsa include un numero di versione per la specifica del modello.

Ad esempio, si distribuisce una specifica del modello con il comando seguente.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

In pratica, in genere si esegue `Get-AzTemplateSpec` per ottenere l'ID della specifica del modello che si vuole distribuire.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>Parametri

Il passaggio di parametri alla specifica del modello è esattamente come passare parametri a un modello ARM. Aggiungere i valori dei parametri inline o in un file di parametri.

Per passare un parametro inline, usare:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Per creare un file di parametri locale, usare:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

Passare quindi il file dei parametri con:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

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
