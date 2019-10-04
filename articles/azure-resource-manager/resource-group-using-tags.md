---
title: Applicare tag alle risorse Azure per l'organizzazione logica | Documentazione Microsoft
description: Mostra come applicare i tag per organizzare le risorse Azure per la fatturazione e la gestione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: 9bcbfe1bdb501cac6ff31156db5382d1174eb8ad
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146829"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Usare tag per organizzare le risorse di Azure

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Per applicare tag alle risorse, l'utente deve avere accesso in scrittura a quel tipo di risorsa. Per applicare tag a tutti i tipi di risorse, usare il ruolo [Collaboratore](../role-based-access-control/built-in-roles.md#contributor). Per applicare tag a un solo tipo di risorsa, usare il ruolo di collaboratore per tale risorsa. Ad esempio, per applicare tag a macchine virtuali, usare il ruolo di [Collaboratore Macchina virtuale](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Criteri

È possibile usare [Criteri di Azure](../governance/policy/overview.md) per applicare le regole e le convenzioni dell'assegnazione di tag. Tramite la creazione di un criterio, è possibile evitare che lo scenario di risorse distribuito nella sottoscrizione non rispetti i tag previsti per l'organizzazione. Anziché procedere manualmente all'applicazione dei tag o alla ricerca delle risorse non conformi, è possibile creare un criterio che applica automaticamente i tag necessari durante la distribuzione. I tag possono essere ora applicati anche alle risorse esistenti con il nuovo effetto [modifica](../governance/policy/concepts/effects.md#modify) e un' [attività di correzione](../governance/policy/how-to/remediate-resources.md). La sezione seguente illustra i criteri di esempio per i tag.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per visualizzare i tag esistenti per un *gruppo di risorse*, usare:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Lo script restituisce il formato seguente:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Per visualizzare i tag esistenti per una *risorsa con un ID risorsa specificato*, usare:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

In alternativa, per visualizzare i tag esistenti per una *risorsa con un nome e un gruppo di risorse specificati*, usare:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Per ottenere i *gruppi di risorse con un tag specifico*, usare:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Per ottenere le *risorse con un tag specifico*, usare:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

Per ottenere le *risorse con un nome di tag specifico*, usare:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Ogni volta che si applicano tag a una risorsa o a un gruppo di risorse, si sovrascrivono i tag esistenti in tale risorsa o gruppo di risorse. È quindi necessario usare un approccio diverso se nella risorsa o nel gruppo di risorse esistono tag.

Per aggiungere tag a un *gruppo di risorse senza tag esistenti*, usare:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Per aggiungere tag a un *gruppo di risorse con tag esistenti*, recuperare i tag esistenti, aggiungere il nuovo tag e riapplicare i tag:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Per aggiungere tag a una *risorsa senza tag esistenti*, usare:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Per aggiungere tag a una *risorsa con tag esistenti*, usare:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Per applicare tutti i tag da un gruppo di risorse alle risorse e *non salvare i tag esistenti nelle risorse*, usare lo script seguente:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Per applicare tutti i tag da un gruppo di risorse alle risorse e *salvare i tag esistenti nelle risorse non duplicate*, usare lo script seguente:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Per rimuovere tutti i tag, passare una tabella hash vuota:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per visualizzare i tag esistenti per un *gruppo di risorse*, usare:

```azurecli
az group show -n examplegroup --query tags
```

Lo script restituisce il formato seguente:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

In alternativa, per visualizzare i tag esistenti per una *risorsa con un nome, un tipo e un gruppo di risorse specificati*, usare:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Quando si scorre una raccolta di risorse si potrebbe voler visualizzare le risorse in base all'ID di risorsa. Un esempio completo viene illustrato più avanti in questo articolo. Per visualizzare i tag esistenti per una *risorsa con un ID risorsa specificato*, usare:

```azurecli
az resource show --id <resource-id> --query tags
```

Per ottenere i gruppi di risorse con un tag specifico, usare `az group list`:

```azurecli
az group list --tag Dept=IT
```

Per ottenere tutte le risorse che hanno un tag e un valore specifici, usare `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Ogni volta che si applicano tag a una risorsa o a un gruppo di risorse, si sovrascrivono i tag esistenti in tale risorsa o gruppo di risorse. È quindi necessario usare un approccio diverso se nella risorsa o nel gruppo di risorse esistono tag.

Per aggiungere tag a un *gruppo di risorse senza tag esistenti*, usare:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Per aggiungere tag a una *risorsa senza tag esistenti*, usare:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Per aggiungere tag a una risorsa che dispone già di tag, recuperare i tag esistenti, riformattare il valore e riapplicare i tag nuovi e già esistenti:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Per applicare tutti i tag da un gruppo di risorse alle risorse e *non salvare i tag esistenti nelle risorse*, usare lo script seguente:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Per applicare tutti i tag da un gruppo di risorse alle risorse e *salvare i tag esistenti nelle risorse*, usare lo script seguente:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Modelli

Per contrassegnare una risorsa durante la distribuzione, `tags` aggiungere l'elemento alla risorsa che si sta distribuendo. e specificare il nome e il valore del tag.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Applicare un valore letterale al nome del tag

L'esempio seguente illustra un account di archiviazione con due tag (`Dept` e `Environment`) impostati su valori letterali:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

Per impostare un tag su un valore DateTime, utilizzare la [funzione UtcNow](resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Applicare un oggetto all'elemento tag

È possibile definire un parametro oggetto in cui vengono memorizzati diversi tag e applicare tale oggetto all'elemento tag. Ogni proprietà nell'oggetto diventa un tag separato per la risorsa. L'esempio seguente include un parametro denominato `tagValues` che viene applicato all'elemento tag.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Applicare una stringa JSON al nome del tag

Per memorizzare più valori in un singolo tag, è possibile applicare una stringa JSON che rappresenta tali valori. L'intera stringa JSON viene archiviata come un tag che non può superare i 256 caratteri. L'esempio seguente include un tag singolo denominato `CostCenter` che contiene più valori da una stringa JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Applicare i tag dal gruppo di risorse

Per applicare i tag da un gruppo di risorse a una risorsa, usare la funzione [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) . Quando si recupera il valore del tag, `tags.[tag-name]` usare la sintassi invece `tags.tag-name` della sintassi, perché alcuni caratteri non vengono analizzati correttamente nella notazione del punto.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>Portale

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

Sia il portale di Azure che PowerShell usano l' [API REST di Gestione Risorse](https://docs.microsoft.com/rest/api/resources/) dietro le quinte. Se è necessario integrare l'assegnazione di tag in un altro ambiente, è possibile ottenere i tag tramite un'operazione **GET** sull'ID di risorsa e aggiornare il set di tag tramite una chiamata **PATCH**.

## <a name="tags-and-billing"></a>Tag e fatturazione

È possibile usare i tag per raggruppare i dati di fatturazione. Se, ad esempio, sono in esecuzione più macchine virtuali per organizzazioni diverse, usare i tag per raggrupparne l'uso in base al centro di costo. È anche possibile usare i tag per classificare i costi in base all'ambiente di runtime; ad esempio, l'uso di fatturazione per le macchine virtuali in esecuzione nell'ambiente di produzione.

Le informazioni sui tag possono essere recuperate tramite l' [API di utilizzo della risorsa di Azure e della Rate Card](../billing/billing-usage-rate-card-overview.md) o il file di utilizzo con valori delimitati da virgole (CSV). Il file d'uso può essere scaricato dal [Centro account di Azure](https://account.azure.com/Subscriptions) o dal portale di Azure. Per altre informazioni, vedere [Scaricare o visualizzare la fattura e i dati di uso giornalieri di Azure](../billing/billing-download-azure-invoice-daily-usage-date.md). Quando si scarica il file d'uso dal Centro account di Azure, selezionare **Versione 2**. Per i servizi che supportano tag con fatturazione, i tag vengono visualizzati nella colonna **Tag**.

Per le operazioni API REST, vedere [Riferimento API REST alla fatturazione di Azure](/rest/api/billing/).

## <a name="next-steps"></a>Passaggi successivi

* Non tutti i tipi di risorse supportano i tag. Per determinare se è possibile applicare un tag a un tipo di risorsa, vedere [Supporto dei tag per le risorse di Azure](tag-support.md).
* Per un'introduzione all'uso del portale, vedere [Uso del portale di Azure per gestire le risorse di Azure](manage-resource-groups-portal.md).  
