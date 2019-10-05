---
title: Distribuire risorse con PowerShell e i modelli | Microsoft Docs
description: Usare Azure Resource Manager e Azure PowerShell per distribuire le risorse in Azure. Le risorse sono definite in un modello di Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: 88aabb676d3a15dd2efff3acd751818301519ae1
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972701"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell

Si vedrà ora come usare Azure PowerShell con i modelli di Resource Manager per distribuire risorse in Azure. Per altre informazioni sui concetti di distribuzione e gestione delle soluzioni di Azure, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).

## <a name="deployment-scope"></a>Ambito di distribuzione

La distribuzione può essere destinata a una sottoscrizione di Azure o a un gruppo di risorse all'interno di una sottoscrizione. Nella maggior parte dei casi, la distribuzione verrà destinata a un gruppo di risorse. Usare le distribuzioni di sottoscrizione per applicare i criteri e le assegnazioni di ruolo nella sottoscrizione. È anche possibile usare le distribuzioni delle sottoscrizioni per creare un gruppo di risorse e distribuirvi risorse. A seconda dell'ambito della distribuzione, si utilizzano comandi diversi.

Per eseguire la distribuzione in un **gruppo di risorse**, usare [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Per eseguire la distribuzione in una **sottoscrizione**, usare [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Attualmente, le distribuzioni di gruppi di gestione sono supportate solo tramite l'API REST. Vedere [distribuire le risorse con gestione risorse modelli e gestione risorse API REST](resource-group-template-deploy-rest.md).

Gli esempi in questo articolo usano le distribuzioni di gruppi di risorse. Per altre informazioni sulle distribuzioni delle sottoscrizioni, vedere [creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md).

## <a name="prerequisites"></a>Prerequisiti

È necessario un modello per la distribuzione. Se non si dispone già di un modello, scaricare e salvare un [modello di esempio](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) dal repository dei modelli di avvio rapido di Azure. Il nome del file locale usato in questo articolo è **c:\MyTemplates\azuredeploy.json**.

A meno che non si usi Azure cloud Shell per distribuire i modelli, è necessario installare Azure PowerShell e connettersi ad Azure:

- **Installare i cmdlet di Azure PowerShell nel computer locale.** Per altre informazioni, vedere [Get started with Azure PowerShell](/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).
- **Connettersi ad Azure tramite [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Se sono disponibili più sottoscrizioni di Azure, potrebbe anche essere necessario eseguire [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Per altre informazioni, vedere [Use multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Usare più sottoscrizioni di Azure).

## <a name="deploy-local-template"></a>Distribuire un modello locale

L'esempio seguente crea un gruppo di risorse e distribuisce un modello dal computer locale. Il nome del gruppo di risorse può contenere solo caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi. Può contenere fino a 90 caratteri. Non può terminare con un punto.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti.

## <a name="deploy-remote-template"></a>Distribuisci modello remoto

Anziché archiviare i modelli di Resource Manager nel computer locale, è consigliabile archiviarli in una posizione esterna, ad esempio in un repository di controllo del codice sorgente come GitHub. o, in alternativa, archiviarli in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione.

Per distribuire un modello esterno, usare il parametro **TemplateUri**. Usare l'URI indicato nell'esempio per distribuire il modello di esempio da GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

L'esempio precedente richiede l'utilizzo di un URI accessibile pubblicamente per il modello, che funziona per la maggior parte degli scenari. Il proprio modello non deve infatti includere dati sensibili. Se è necessario specificare dati riservati, ad esempio una password di amministratore, passare il valore come parametro protetto. Se invece si preferisce che il modello usato non sia accessibile pubblicamente, è possibile proteggerlo archiviandolo in un contenitore di archiviazione privato. Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso (SAS), vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-powershell-sas-token.md). Per eseguire un'esercitazione, vedere [Esercitazione: Integrare Azure Key Vault in Distribuzione modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Distribuire da Azure cloud Shell

È possibile usare [Azure Cloud Shell](https://shell.azure.com) per distribuire il modello. Per distribuire un modello esterno, specificare l'URI del modello. Per distribuire un modello locale, è innanzitutto necessario caricare il modello nell'account di archiviazione per Cloud Shell. Per caricare i file nella shell, selezionare l'icona del menu **Carica/Scarica file** dalla finestra della shell.

Per aprire Cloud Shell, passare a [https://shell.azure.com](https://shell.azure.com) oppure selezionare **Prova** dalla sezione di codice seguente:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Per incollare il codice nella shell, fare clic con il pulsante destro del mouse all'interno della shell e quindi scegliere **Incolla**.

## <a name="pass-parameter-values"></a>Passare i valori dei parametri

Per passare i valori dei parametri, è possibile usare i parametri inline o un file di parametri.

### <a name="inline-parameters"></a>Parametri inline

Per passare i parametri inline, specificare i nomi del parametro con il comando `New-AzResourceGroupDeployment`. Ad esempio, per passare una stringa e una matrice a un modello, usare:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

È anche possibile ottenere i contenuti del file e fornire il contenuto come un parametro inline.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Ottenere un valore di parametro da un file è utile quando è necessario fornire i valori di configurazione. Ad esempio, è possibile fornire i valori [cloud-init per una macchina virtuale Linux](../virtual-machines/linux/using-cloud-init.md).

Se è necessario passare una matrice di oggetti, creare tabelle hash in PowerShell e aggiungerle a una matrice. Passare la matrice come parametro durante la distribuzione.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>File dei parametri

Invece di passare i parametri come valori inline nello script, può risultare più facile usare un file JSON che contenga i valori dei parametri. Il file dei parametri può essere un file locale o un file esterno con un URI accessibile.

Per altre informazioni sul file dei parametri, vedere [creare Gestione risorse file di parametri](resource-manager-parameter-files.md).

Per passare un file dei parametri locale, usare il parametro **TemplateParameterFile**:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Per passare un file dei parametri esterno, usare il parametro **TemplateParameterUri**:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Testare le distribuzioni del modello

Per testare il modello e i valori dei parametri senza distribuire effettivamente le risorse, usare [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Se non vengono rilevati errori, il comando termina senza una risposta. Se viene rilevato un errore, il comando restituisce un messaggio di errore. Ad esempio, passare un valore non corretto per lo SKU dell'account di archiviazione restituisce l'errore seguente:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Se il modello contiene un errore di sintassi, il comando restituisce un errore che indica l'impossibilità di analizzare il modello. Il messaggio contiene il numero di riga e la posizione dell'errore di analisi.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Passaggi successivi

- Per eseguire il rollback a una distribuzione corretta quando viene visualizzato un errore, vedere eseguire [il rollback in caso di errore di distribuzione riuscita](rollback-on-error.md).
- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
- Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-powershell-sas-token.md).
