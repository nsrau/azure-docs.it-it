---
title: Contrassegnare risorse, gruppi di risorse e sottoscrizioni per l'organizzazione logica
description: Mostra come applicare i tag per organizzare le risorse Azure per la fatturazione e la gestione.
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9e9ef96a712e5ac2ba483170fb8ef9c89115b4f8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95972564"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Usare i tag per organizzare le risorse di Azure e la gerarchia di gestione

I tag vengono applicati alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarli in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

Per indicazioni su come implementare una strategia di assegnazione di tag, vedere la pagina [relativa alla denominazione delle risorse e all'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> I nomi di tag non fanno distinzione tra maiuscole e minuscole per le operazioni. Un tag con un nome di tag, indipendentemente dalla combinazione di maiuscole e minuscole, viene aggiornato o recuperato. Il provider di risorse, tuttavia, può contenere le maiuscole e minuscole fornite per il nome del tag. Si noterà che le maiuscole e minuscole nei report sui costi.
> 
> I valori dei tag distinguono tra maiuscole e minuscole

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Accesso obbligatorio

Per applicare i tag a una risorsa, è necessario disporre dell'accesso in scrittura al tipo di risorsa **Microsoft. resources/Tags** . Il ruolo di [collaboratore Tag](../../role-based-access-control/built-in-roles.md#tag-contributor) consente di applicare tag a un'entità senza avere accesso all'entità stessa. Attualmente, il ruolo Collaboratore tag non può applicare tag a risorse o gruppi di risorse tramite il portale. Può applicare tag alle sottoscrizioni tramite il portale. Supporta tutte le operazioni sui tag tramite PowerShell e l'API REST.  

Il ruolo [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) concede anche l'accesso richiesto per applicare tag a qualsiasi entità. Per applicare tag a un solo tipo di risorsa, usare il ruolo di collaboratore per tale risorsa. Ad esempio, per applicare tag a macchine virtuali, usare il ruolo di [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Applica tag

Azure PowerShell offre due comandi per l'applicazione di tag- [New-AzTag](/powershell/module/az.resources/new-aztag) e [Update-AzTag](/powershell/module/az.resources/update-aztag). È necessario avere il modulo AZ. resources 1.12.0 o versione successiva. È possibile controllare la versione con `Get-Module Az.Resources` . È possibile installare il modulo o [installare Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 o versione successiva.

**New-AzTag** sostituisce tutti i tag per la risorsa, il gruppo di risorse o la sottoscrizione. Quando si chiama il comando, passare l'ID risorsa dell'entità a cui si vuole assegnare un tag.

Nell'esempio seguente viene applicato un set di tag a un account di archiviazione:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Al termine del comando, si noti che la risorsa ha due tag.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Se si esegue di nuovo il comando, ma questa volta con tag diversi, si noterà che i tag precedenti vengono rimossi.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Per aggiungere tag a una risorsa che dispone già di tag, usare **Update-AzTag**. Impostare il parametro **-Operation** su **merge**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Si noti che i due nuovi tag sono stati aggiunti ai due tag esistenti.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Ogni nome di tag può avere un solo valore. Se si specifica un nuovo valore per un tag, il valore precedente viene sostituito anche se si utilizza l'operazione di Unione. Nell'esempio seguente viene modificato il tag di stato da Normal a Green.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Quando si imposta il parametro **-Operation** su **Replace**, i tag esistenti vengono sostituiti dal nuovo set di tag.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Solo i nuovi tag rimangono nella risorsa.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Gli stessi comandi funzionano anche con i gruppi di risorse o le sottoscrizioni. Si passa l'identificatore per il gruppo di risorse o la sottoscrizione che si vuole contrassegnare.

Per aggiungere un nuovo set di tag a un gruppo di risorse, usare:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Per aggiornare i tag per un gruppo di risorse, usare:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Per aggiungere un nuovo set di tag a una sottoscrizione, usare:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Per aggiornare i tag per una sottoscrizione, usare:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

È possibile avere più di una risorsa con lo stesso nome in un gruppo di risorse. In tal caso, è possibile impostare ogni risorsa con i comandi seguenti:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Elencare tag

Per ottenere i tag per una risorsa, un gruppo di risorse o una sottoscrizione, usare il comando [Get-AzTag](/powershell/module/az.resources/get-aztag) e passare l'ID risorsa per l'entità.

Per visualizzare i tag per una risorsa, usare:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Per visualizzare i tag per un gruppo di risorse, usare:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Per visualizzare i tag per una sottoscrizione, usare:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Elenca per tag

Per ottenere le risorse con un nome e un valore di tag specifici, usare:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Per ottenere le risorse con un nome di tag specifico con qualsiasi valore di tag, usare:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Per ottenere i gruppi di risorse con un nome e un valore di tag specifici, usare:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Rimuovi Tag

Per rimuovere tag specifici, usare **Update-AzTag** e set **-Operation** per **eliminare**. Passare i tag che si desidera eliminare.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

I tag specificati vengono rimossi.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Per rimuovere tutti i tag, usare il comando [Remove-AzTag](/powershell/module/az.resources/remove-aztag) .

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

### <a name="apply-tags"></a>Applica tag

L'interfaccia della riga di comando di Azure offre due comandi per applicare tag: [AZ Tag create](/cli/azure/tag#az_tag_create) e [AZ Tag Update](/cli/azure/tag#az_tag_update). È necessario disporre dell'interfaccia della riga di comando di Azure 2.10.0 o versione successiva. È possibile controllare la versione con `az version` . Per aggiornare o installare, vedere [installare l'interfaccia della](/cli/azure/install-azure-cli)riga di comando di Azure.

**AZ Tag create** sostituisce tutti i tag per la risorsa, il gruppo di risorse o la sottoscrizione. Quando si chiama il comando, passare l'ID risorsa dell'entità a cui si vuole assegnare un tag.

Nell'esempio seguente viene applicato un set di tag a un account di archiviazione:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

Al termine del comando, si noti che la risorsa ha due tag.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

Se si esegue di nuovo il comando, ma questa volta con tag diversi, si noterà che i tag precedenti vengono rimossi.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

Per aggiungere tag a una risorsa che dispone già di tag, usare **AZ Tag Update**. Impostare il parametro **--Operation** per eseguire il **merge**.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

Si noti che i due nuovi tag sono stati aggiunti ai due tag esistenti.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

Ogni nome di tag può avere un solo valore. Se si specifica un nuovo valore per un tag, il valore precedente viene sostituito anche se si utilizza l'operazione di Unione. Nell'esempio seguente viene modificato il tag di stato da Normal a Green.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

Quando si imposta il parametro **--Operation** su **Replace**, i tag esistenti vengono sostituiti dal nuovo set di tag.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

Solo i nuovi tag rimangono nella risorsa.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

Gli stessi comandi funzionano anche con i gruppi di risorse o le sottoscrizioni. Si passa l'identificatore per il gruppo di risorse o la sottoscrizione che si vuole contrassegnare.

Per aggiungere un nuovo set di tag a un gruppo di risorse, usare:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

Per aggiornare i tag per un gruppo di risorse, usare:

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

Per aggiungere un nuovo set di tag a una sottoscrizione, usare:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

Per aggiornare i tag per una sottoscrizione, usare:

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>Elencare tag

Per ottenere i tag per una risorsa, un gruppo di risorse o una sottoscrizione, usare il comando [AZ Tag List](/cli/azure/tag#az_tag_list) e passare l'ID risorsa per l'entità.

Per visualizzare i tag per una risorsa, usare:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

Per visualizzare i tag per un gruppo di risorse, usare:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

Per visualizzare i tag per una sottoscrizione, usare:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>Elenca per tag

Per ottenere le risorse con un nome e un valore di tag specifici, usare:

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

Per ottenere le risorse con un nome di tag specifico con qualsiasi valore di tag, usare:

```azurecli-interactive
az resource list --tag Team --query [].name
```

Per ottenere i gruppi di risorse con un nome e un valore di tag specifici, usare:

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>Rimuovi Tag

Per rimuovere tag specifici, usare **AZ Tag Update** e set **--Operation** to **Delete**. Passare i tag che si desidera eliminare.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

I tag specificati vengono rimossi.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

Per rimuovere tutti i tag, usare il comando [AZ Tag delete](/cli/azure/tag#az_tag_delete) .

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>Gestione di spazi

Se i nomi o i valori dei tag includono spazi, racchiuderli tra virgolette doppie.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="templates"></a>Modelli

È possibile contrassegnare risorse, gruppi di risorse e sottoscrizioni durante la distribuzione con un modello di Gestione risorse.

### <a name="apply-values"></a>Applica valori

Nell'esempio seguente viene distribuito un account di archiviazione con tre tag. Due dei tag ( `Dept` e `Environment` ) sono impostati su valori letterali. Un tag ( `LastDeployed` ) è impostato su un parametro il cui valore predefinito è la data corrente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
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
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
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

### <a name="apply-an-object"></a>Applicare un oggetto

È possibile definire un parametro oggetto in cui vengono memorizzati diversi tag e applicare tale oggetto all'elemento tag. Questo approccio offre una maggiore flessibilità rispetto all'esempio precedente, perché l'oggetto può avere proprietà diverse. Ogni proprietà nell'oggetto diventa un tag separato per la risorsa. L'esempio seguente include un parametro denominato `tagValues` che viene applicato all'elemento tag.

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

### <a name="apply-a-json-string"></a>Applicare una stringa JSON

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

Per applicare i tag da un gruppo di risorse a una risorsa, usare la funzione [resourceGroup ()](../templates/template-functions-resource.md#resourcegroup) . Quando si recupera il valore del tag, usare la `tags[tag-name]` sintassi invece della `tags.tag-name` sintassi, perché alcuni caratteri non vengono analizzati correttamente nella notazione del punto.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Applicare tag a gruppi di risorse o sottoscrizioni

È possibile aggiungere tag a un gruppo di risorse o a una sottoscrizione distribuendo il tipo di risorsa **Microsoft. resources/Tags** . I tag vengono applicati al gruppo di risorse o alla sottoscrizione di destinazione per la distribuzione. Ogni volta che si distribuisce il modello, sostituire eventuali tag applicati in precedenza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Per applicare i tag a un gruppo di risorse, usare PowerShell o l'interfaccia della riga di comando di Azure. Eseguire la distribuzione nel gruppo di risorse che si desidera contrassegnare.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Per applicare i tag a una sottoscrizione, usare PowerShell o l'interfaccia della riga di comando di Azure. Eseguire la distribuzione nella sottoscrizione che si vuole contrassegnare.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Per altre informazioni sulle distribuzioni delle sottoscrizioni, vedere [creare gruppi di risorse e risorse a livello di sottoscrizione](../templates/deploy-to-subscription.md).

Il modello seguente aggiunge i tag da un oggetto a un gruppo di risorse o a una sottoscrizione.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portale

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

Per lavorare con i tag tramite l'API REST di Azure, usare:

* [Tag-crea o Aggiorna nell'ambito](/rest/api/resources/tags/createorupdateatscope) (operazione Put)
* [Tag-aggiornamento nell'ambito](/rest/api/resources/tags/updateatscope) (operazione patch)
* [Tag-Get nell'ambito](/rest/api/resources/tags/getatscope) (operazione get)
* [Tag-Delete nell'ambito](/rest/api/resources/tags/deleteatscope) (operazione di eliminazione)

## <a name="inherit-tags"></a>Eredita Tag

I tag applicati al gruppo di risorse o alla sottoscrizione non vengono ereditati dalle risorse. Per applicare tag da una sottoscrizione o un gruppo di risorse alle risorse, vedere [criteri di Azure-Tag](tag-policies.md).

## <a name="tags-and-billing"></a>Tag e fatturazione

È possibile usare i tag per raggruppare i dati di fatturazione. Se, ad esempio, sono in esecuzione più macchine virtuali per organizzazioni diverse, usare i tag per raggrupparne l'uso in base al centro di costo. È anche possibile usare i tag per classificare i costi in base all'ambiente di runtime; ad esempio, l'uso di fatturazione per le macchine virtuali in esecuzione nell'ambiente di produzione.

È possibile recuperare informazioni sui tag tramite le [API di utilizzo delle risorse di Azure e delle schede tariffarie](../../cost-management-billing/manage/usage-rate-card-overview.md) oppure il file con valori delimitati da virgole (CSV) di utilizzo. Il file di utilizzo viene scaricato dal portale di Azure. Per altre informazioni, vedere [Scaricare o visualizzare la fattura e i dati di uso giornalieri di Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). Quando si scarica il file d'uso dal Centro account di Azure, selezionare **Versione 2**. Per i servizi che supportano tag con fatturazione, i tag vengono visualizzati nella colonna **Tag**.

Per le operazioni API REST, vedere [Riferimento API REST alla fatturazione di Azure](/rest/api/billing/).

## <a name="limitations"></a>Limitazioni

Ai tag si applicano le limitazioni seguenti:

* Non tutti i tipi di risorse supportano i tag. Per determinare se è possibile applicare un tag a un tipo di risorsa, vedere [Supporto dei tag per le risorse di Azure](tag-support.md).
* Ogni risorsa, gruppo di risorse e sottoscrizione può avere un massimo di 50 coppie nome/valore di tag. Se è necessario applicare più tag rispetto al numero massimo consentito, usare una stringa JSON per il valore del tag. La stringa JSON può contenere diversi valori applicati a un singolo nome di tag. Un gruppo di risorse o una sottoscrizione può contenere molte risorse, ognuna delle quali ha 50 coppie nome/valore di tag.
* Il nome del tag è limitato a 512 caratteri e il valore del tag è limitato a 256 caratteri. Per gli account di archiviazione, il nome del tag è limitato a 128 caratteri e il valore a 256 caratteri.
* Non è possibile applicare tag alle risorse classiche, ad esempio Servizi cloud.
* I nomi dei tag non possono contenere i caratteri seguenti: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Attualmente, le zone DNS di Azure e i servizi di gestione traffico non consentono l'uso di spazi nel tag.
   >
   > La porta anteriore di Azure non supporta l'uso di `#` nel nome del tag.
   >
   > Automazione di Azure e la rete CDN di Azure supportano solo 15 tag sulle risorse.

## <a name="next-steps"></a>Passaggi successivi

* Non tutti i tipi di risorse supportano i tag. Per determinare se è possibile applicare un tag a un tipo di risorsa, vedere [Supporto dei tag per le risorse di Azure](tag-support.md).
* Per indicazioni su come implementare una strategia di assegnazione di tag, vedere la pagina [relativa alla denominazione delle risorse e all'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
