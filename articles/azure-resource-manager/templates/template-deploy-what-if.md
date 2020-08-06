---
title: Distribuzione modelli simulazione (anteprima)
description: Determinare quali modifiche si verificheranno nelle risorse prima di distribuire un modello di Azure Resource Manager.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: tomfitz
ms.openlocfilehash: 27efe1e03b8a0d373d566106a53a41007731973e
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810072"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Operazione di simulazione della distribuzione del modello ARM (anteprima)

Prima di distribuire un modello di Azure Resource Manager (modello ARM), è possibile visualizzare in anteprima le modifiche che si verificheranno. Azure Resource Manager fornisce l'operazione di simulazione per visualizzare il modo in cui le risorse vengono modificate se si distribuisce il modello. L'operazione di simulazione non consente di apportare modifiche alle risorse esistenti. Vengono invece stimate le modifiche se il modello specificato viene distribuito.

> [!NOTE]
> L'operazione di simulazione è attualmente in anteprima. Come versione di anteprima, i risultati possono a volte indicare che una risorsa cambierà quando in realtà non si verifica alcuna modifica. Ci stiamo impegnando per ridurre questi problemi, ma è necessario aiutarti. Segnala questi problemi all'indirizzo [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .

È possibile usare l'operazione di simulazione con Azure PowerShell, l'interfaccia della riga di comando di Azure o le operazioni dell'API REST. Cosa-se è supportato per le distribuzioni di gruppi di risorse, sottoscrizioni, gruppi di gestione e a livello di tenant.

## <a name="install-azure-powershell-module"></a>Installare il modulo Azure PowerShell

Per usare simulazione in PowerShell, è necessario avere **la versione 4,2 o successiva del modulo AZ**.

Tuttavia, prima di installare il modulo necessario, assicurarsi di avere PowerShell Core (6. x o 7. x). Se si dispone di PowerShell 5. x o versioni precedenti, [aggiornare la versione di PowerShell](/powershell/scripting/install/installing-powershell). Non è possibile installare il modulo richiesto in PowerShell 5. x o versioni precedenti.

### <a name="install-latest-version"></a>Installare la versione più recente

Per installare il modulo, usare:

```powershell
Install-Module -Name Az -Force
```

Per ulteriori informazioni sull'installazione dei moduli, vedere [Install Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="uninstall-alpha-version"></a>Disinstalla versione Alpha

Se in precedenza è stata installata una versione Alpha del modulo simulazione, disinstallare il modulo. La versione Alpha era disponibile solo per gli utenti che si sono iscritti per un'anteprima anticipata. Se l'anteprima non è stata installata, è possibile ignorare questa sezione.

1. Eseguire PowerShell come amministratore
1. Controllare le versioni installate del modulo AZ. resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Se si dispone di una versione installata con un numero di versione nel formato **2. x. x-Alpha**, disinstallare tale versione.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Annullare la registrazione del repository di simulazione usato per installare l'anteprima.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

A questo punto si è pronti per usare simulazione.

## <a name="install-azure-cli-module"></a>Installare il modulo CLI di Azure

Per usare l'interfaccia della riga di comando di Azure, è necessario disporre dell'interfaccia della riga di comando di Azure 2.5.0 o versione successiva. Se necessario, [installare la versione più recente dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="see-results"></a>See results (Visualizza risultati)

Quando si usa simulazione in PowerShell o nell'interfaccia della riga di comando di Azure, l'output include risultati codificati a colori che consentono di visualizzare i diversi tipi di modifiche.

![Gestione risorse l'operazione di simulazione della distribuzione del modello fullresourcepayload e i tipi di modifica](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> L'operazione di simulazione non può risolvere la [funzione di riferimento](template-functions-resource.md#reference). Ogni volta che si imposta una proprietà su un'espressione di modello che include la funzione di riferimento, cosa-se segnala la proprietà che cambierà. Questo comportamento si verifica perché viene confrontato il valore corrente della proprietà (ad esempio `true` o `false` per un valore booleano) con l'espressione di modello non risolta. Ovviamente, questi valori non corrisponderanno. Quando si distribuisce il modello, la proprietà viene modificata solo quando l'espressione modello viene risolta in un valore diverso.

## <a name="what-if-commands"></a>Comandi di simulazione

### <a name="azure-powershell"></a>Azure PowerShell

Per visualizzare in anteprima le modifiche prima di distribuire un modello, usare [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) o [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Aggiungere il `-Whatif` parametro switch al comando Deployment.

* `New-AzResourceGroupDeployment -Whatif`per le distribuzioni di gruppi di risorse
* `New-AzSubscriptionDeployment -Whatif`e `New-AzDeployment -Whatif` per le distribuzioni a livello di sottoscrizione

È possibile usare il `-Confirm` parametro switch per visualizzare l'anteprima delle modifiche e ottenere la richiesta di continuare con la distribuzione.

* `New-AzResourceGroupDeployment -Confirm`per le distribuzioni di gruppi di risorse
* `New-AzSubscriptionDeployment -Confirm`e `New-AzDeployment -Confirm` per le distribuzioni a livello di sottoscrizione

I comandi precedenti restituiscono un riepilogo di testo che è possibile ispezionare manualmente. Per ottenere un oggetto che è possibile controllare a livello di codice le modifiche, usare [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) o [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult).

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`per le distribuzioni di gruppi di risorse
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`o `$results = Get-AzDeploymentWhatIfResult` per le distribuzioni a livello di sottoscrizione

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per visualizzare in anteprima le modifiche prima di distribuire un modello, usare:

* [AZ Deployment Group-se](/cli/azure/deployment/group#az-deployment-group-what-if) per le distribuzioni di gruppi di risorse
* [AZ Deployment Sub What-If](/cli/azure/deployment/sub#az-deployment-sub-what-if) per le distribuzioni a livello di sottoscrizione
* [AZ Deployment mg-se](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-what-if) per le distribuzioni di gruppi di gestione
* [AZ Deployment tenant](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-what-if) simulazione per le distribuzioni tenant

È possibile usare l' `--confirm-with-what-if` opzione (o la relativa forma breve `-c` ) per visualizzare l'anteprima delle modifiche e ricevere la richiesta di continuare con la distribuzione. Aggiungere questa opzione a:

* [AZ Deployment Group create](/cli/azure/deployment/group#az-deployment-group-create)
* [AZ Deployment Sub create](/cli/azure/deployment/sub#az-deployment-sub-create).
* [AZ Deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create)
* [AZ Deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create)

Ad esempio, usare `az deployment group create --confirm-with-what-if` o `-c` per le distribuzioni di gruppi di risorse.

I comandi precedenti restituiscono un riepilogo di testo che è possibile ispezionare manualmente. Per ottenere un oggetto JSON che è possibile controllare a livello di codice le modifiche, usare l' `--no-pretty-print` opzione. Ad esempio, usare `az deployment group what-if --no-pretty-print` per le distribuzioni di gruppi di risorse.

Se si vogliono restituire i risultati senza colori, aprire il file di [configurazione dell'interfaccia](/cli/azure/azure-cli-configuration) della riga di comando di Azure. Impostare **no_color** su **Sì**.

### <a name="azure-rest-api"></a>API REST di Azure

Per l'API REST, usare:

* [Distribuzioni-What If](/rest/api/resources/deployments/whatif) per le distribuzioni di gruppi di risorse
* [Distribuzioni-What If nell'ambito della sottoscrizione](/rest/api/resources/deployments/whatifatsubscriptionscope) per le distribuzioni di sottoscrizioni
* [Distribuzioni-What If nell'ambito del gruppo di gestione](/rest/api/resources/deployments/whatifatmanagementgroupscope) per le distribuzioni di gruppi di gestione
* [Distribuzioni: What If nell'ambito del tenant](/rest/api/resources/deployments/whatifattenantscope) per le distribuzioni tenant.

## <a name="change-types"></a>Tipi di modifiche

L'operazione di simulazione elenca sei tipi diversi di modifiche:

- **Create**: la risorsa attualmente non esiste ma è definita nel modello. La risorsa verrà creata.

- **Elimina**: questo tipo di modifica si applica solo quando si usa la [modalità completa](deployment-modes.md) per la distribuzione. La risorsa esiste, ma non è definita nel modello. Con la modalità completa, la risorsa verrà eliminata. Solo le risorse che [supportano l'eliminazione in modalità completa](complete-mode-deletion.md) sono incluse in questo tipo di modifica.

- **Ignore**: la risorsa esiste, ma non è definita nel modello. La risorsa non verrà distribuita o modificata.

- **NoChange**: la risorsa esiste e viene definita nel modello. La risorsa verrà ridistribuita, ma le proprietà della risorsa non verranno modificate. Questo tipo di modifica viene restituito quando [ResultFormat](#result-format) è impostato su `FullResourcePayloads` , che corrisponde al valore predefinito.

- **Modifica**: la risorsa esiste e viene definita nel modello. La risorsa verrà ridistribuita e le proprietà della risorsa verranno modificate. Questo tipo di modifica viene restituito quando [ResultFormat](#result-format) è impostato su `FullResourcePayloads` , che corrisponde al valore predefinito.

- **Deploy**: la risorsa esiste e viene definita nel modello. La risorsa verrà ridistribuita. Le proprietà della risorsa possono essere modificate o meno. Tramite l'operazione viene restituito questo tipo di modifica quando non sono disponibili informazioni sufficienti per determinare se le proprietà cambiano. Questa condizione viene visualizzata solo quando [ResultFormat](#result-format) è impostato su `ResourceIdOnly` .

## <a name="result-format"></a>Formato del risultato

È possibile controllare il livello di dettaglio restituito sulle modifiche previste. Sono disponibili due opzioni:

* **FullResourcePayloads** : restituisce un elenco di risorse che cambieranno e dettagli sulle proprietà che cambieranno
* **ResourceIdOnly** : restituisce un elenco di risorse che cambieranno

Il valore predefinito è **FullResourcePayloads**.

Per i comandi di distribuzione di PowerShell, usare il `-WhatIfResultFormat` parametro. Nei comandi dell'oggetto a livello di codice, usare il `ResultFormat` parametro.

Per l'interfaccia della riga di comando di Azure, usare il parametro `--result-format`.
 
I risultati seguenti mostrano i due diversi formati di output:

- Payload di risorse completi

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Solo ID risorsa

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Eseguire un'operazione di simulazione

### <a name="set-up-environment"></a>Set up environment (Configurare l'ambiente)

Per verificarne il funzionamento, è possibile eseguire alcuni test. Per prima cosa, distribuire un modello per la [creazione di una rete virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Questa rete virtuale verrà usata per verificare il modo in cui vengono segnalate le modifiche da simulazione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Modifica test

Al termine della distribuzione, si è pronti per testare l'operazione di simulazione. Questa volta si distribuisce un [modello che modifica la rete virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Mancano uno dei tag originali, una subnet è stata rimossa e il prefisso dell'indirizzo è stato modificato.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

L'output di simulazione appare simile al seguente:

![Gestione risorse l'output dell'operazione di simulazione della distribuzione del modello](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Si noti che nella parte superiore dell'output vengono definiti i colori per indicare il tipo di modifiche.

Nella parte inferiore dell'output è indicato che il proprietario del tag è stato eliminato. Il prefisso dell'indirizzo è stato modificato da 10.0.0.0/16 a 10.0.0.0/15. La subnet denominata subnet001 è stata eliminata. Tenere presente che queste modifiche non sono state distribuite. Viene visualizzata un'anteprima delle modifiche che si verificheranno se si distribuisce il modello.

Alcune delle proprietà elencate come eliminate non cambiano effettivamente. Le proprietà possono essere segnalate erroneamente come eliminate quando non sono incluse nel modello, ma vengono impostate automaticamente durante la distribuzione come valori predefiniti. Questo risultato viene considerato "Noise" nella risposta di simulazione. La risorsa finale distribuita avrà i valori impostati per le proprietà. Quando l'operazione di simulazione è matura, queste proprietà verranno filtrate fuori dal risultato.

## <a name="programmatically-evaluate-what-if-results"></a>Valutare i risultati di simulazione a livello di codice

A questo punto, è possibile valutare i risultati di simulazione a livello di codice impostando il comando su una variabile.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

È possibile visualizzare un riepilogo di ogni modifica.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Confermare l'eliminazione

L'operazione di simulazione supporta l'uso della [modalità di distribuzione](deployment-modes.md). Quando viene impostata la modalità completa, le risorse non presenti nel modello vengono eliminate. Nell'esempio seguente viene distribuito un [modello privo di risorse definite](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) in modalità completa.

Per visualizzare in anteprima le modifiche prima di distribuire un modello, usare il parametro Confirm switch con il comando Deployment. Se le modifiche sono quelle previste, confermare che si desidera completare la distribuzione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Poiché nel modello non sono definite risorse e la modalità di distribuzione è impostata su completa, la rete virtuale verrà eliminata.

![Modalità di distribuzione dell'output dell'operazione di distribuzione del modello di Gestione risorse completamento](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Vengono visualizzate le modifiche previste e è possibile confermare che si desidera eseguire la distribuzione.

## <a name="sdks"></a>SDK

È possibile usare l'operazione di simulazione tramite gli SDK di Azure.

* Per Python [, usare simulazione](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations?view=azure-python#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* Per Java, usare la [classe DeploymentWhatIf](/java/api/com.microsoft.azure.management.resources.deploymentwhatif?view=azure-java-stable).

* Per .NET, usare la [classe DeploymentWhatIf](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif?view=azure-dotnet).

## <a name="next-steps"></a>Passaggi successivi

- Se si notano risultati non corretti dalla versione di anteprima di simulazione, segnalare i problemi all'indirizzo [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Per distribuire i modelli con Azure PowerShell, vedere [distribuire le risorse con i modelli e i Azure PowerShell ARM](deploy-powershell.md).
- Per distribuire modelli con l'interfaccia della riga di comando di Azure, vedere [distribuire risorse con modelli ARM e l'interfaccia](deploy-cli.md)della riga di comando
- Per distribuire i modelli con REST, vedere [distribuire le risorse con i modelli ARM e gestione risorse API REST](deploy-rest.md).
