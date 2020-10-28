---
title: Distribuire le risorse con PowerShell e il modello
description: Usare Azure Resource Manager e Azure PowerShell per distribuire le risorse in Azure. Le risorse sono definite in un modello di Resource Manager.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 5266aa51422dce6dfa4b82238e905f4f630ccf48
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668560"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell

Questo articolo illustra come usare Azure PowerShell con i modelli di Azure Resource Manager (modelli ARM) per distribuire le risorse in Azure. Se non si ha familiarità con i concetti relativi alla distribuzione e alla gestione delle soluzioni di Azure, vedere [Panoramica della distribuzione dei modelli](overview.md).

## <a name="prerequisites"></a>Prerequisiti

È necessario un modello per la distribuzione. Se non si dispone già di un modello, scaricare e salvare un [modello di esempio](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) dal repository dei modelli di avvio rapido di Azure. Il nome del file locale usato in questo articolo è **c:\MyTemplates\azuredeploy.json** .

È necessario installare Azure PowerShell e connettersi ad Azure:

- **Installare i cmdlet di Azure PowerShell nel computer locale.** Per altre informazioni, vedere [Get started with Azure PowerShell](/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).
- **Connettersi ad Azure tramite [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Se sono disponibili più sottoscrizioni di Azure, potrebbe anche essere necessario eseguire [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Per altre informazioni, vedere [Use multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Usare più sottoscrizioni di Azure).

Se PowerShell non è installato, è possibile usare la Cloud Shell. Per altre informazioni, vedere [distribuire modelli ARM da cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Ambito della distribuzione

La distribuzione può essere destinata a un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. A seconda dell'ambito della distribuzione, vengono usati comandi diversi.

* Per eseguire la distribuzione in un **gruppo di risorse** , usare [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* Per eseguire la distribuzione in una **sottoscrizione** , usare New-AzSubscriptionDeployment:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Per altre informazioni sulle distribuzioni a livello di sottoscrizione, vedere [Creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md).

* Per eseguire la distribuzione in un **gruppo di gestione** , usare [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Per altre informazioni sulle distribuzioni a livello di gruppo di gestione, vedere [Creare risorse a livello di gruppo di gestione](deploy-to-management-group.md).

* Per eseguire la distribuzione in un **tenant** , usare [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Per altre informazioni sulle distribuzioni a livello di tenant, vedere [Creare risorse a livello di tenant](deploy-to-tenant.md).

Per ogni ambito, l'utente che distribuisce il modello deve disporre delle autorizzazioni necessarie per creare risorse.

## <a name="deploy-local-template"></a>Distribuire un modello locale

È possibile distribuire un modello dal computer locale o da uno archiviato esternamente. In questa sezione viene descritta la distribuzione di un modello locale.

Se si esegue la distribuzione in un gruppo di risorse che non esiste, creare il gruppo di risorse. Il nome del gruppo di risorse può contenere solo caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi. Può contenere fino a 90 caratteri. Il nome non può terminare con un punto.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Per distribuire un modello locale, usare il `-TemplateFile` parametro nel comando di distribuzione. Nell'esempio seguente viene anche illustrato come impostare un valore di parametro che deriva dal modello.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti.

## <a name="deploy-remote-template"></a>Distribuisci modello remoto

Anziché archiviare i modelli ARM nel computer locale, è preferibile archiviarli in una posizione esterna. ad esempio in un repository di controllo del codice sorgente come GitHub. È possibile, in alternativa, archiviarli in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione.

Se si esegue la distribuzione in un gruppo di risorse che non esiste, creare il gruppo di risorse. Il nome del gruppo di risorse può contenere solo caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi. Può contenere fino a 90 caratteri. Il nome non può terminare con un punto.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Per distribuire un modello esterno, usare il parametro `-TemplateUri`.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

L'esempio precedente richiede l'utilizzo di un URI accessibile pubblicamente per il modello, che funziona per la maggior parte degli scenari. Il proprio modello non deve infatti includere dati sensibili. Se è necessario specificare dati riservati, ad esempio una password di amministratore, passare il valore come parametro protetto. Tuttavia, se si desidera gestire l'accesso al modello, è consigliabile utilizzare le [specifiche del modello](#deploy-template-spec).

## <a name="deployment-name"></a>Nome distribuzione

Quando si distribuisce un modello ARM, è possibile assegnare un nome alla distribuzione. Questo nome può essere utile per recuperare la distribuzione dalla cronologia di distribuzione. Se non si specifica un nome per la distribuzione, viene usato il nome del file modello. Se, ad esempio, si distribuisce un modello denominato `azuredeploy.json` e non si specifica un nome di distribuzione, la distribuzione verrà denominata `azuredeploy` .

Ogni volta che si esegue una distribuzione, viene aggiunta una voce alla cronologia di distribuzione del gruppo di risorse con il nome della distribuzione. Se si esegue un'altra distribuzione e si assegna lo stesso nome, la voce precedente viene sostituita con la distribuzione corrente. Se si desidera mantenere voci univoche nella cronologia di distribuzione, assegnare a ogni distribuzione un nome univoco.

Per creare un nome univoco, è possibile assegnare un numero casuale.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

In alternativa, aggiungere un valore di data.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Se si eseguono distribuzioni simultanee nello stesso gruppo di risorse con lo stesso nome di distribuzione, solo l'ultima distribuzione viene completata. Tutte le distribuzioni con lo stesso nome che non sono state completate vengono sostituite dall'ultima distribuzione. Ad esempio, se si esegue una distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage1` e allo stesso tempo esegue un'altra distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage2` , si distribuisce un solo account di archiviazione. L'account di archiviazione risultante è denominato `storage2` .

Tuttavia, se si esegue una distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage1` e immediatamente dopo aver completato l'esecuzione di un'altra distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage2` , si avranno due account di archiviazione. Uno è denominato `storage1` e l'altro è denominato `storage2` . Tuttavia, è presente una sola voce nella cronologia di distribuzione.

Quando si specifica un nome univoco per ogni distribuzione, è possibile eseguirli simultaneamente senza conflitti. Se si esegue una distribuzione denominata `newStorage1` che distribuisce un account di archiviazione denominato `storage1` e allo stesso tempo esegue un'altra distribuzione denominata `newStorage2` che distribuisce un account di archiviazione denominato `storage2` , si avranno due account di archiviazione e due voci nella cronologia di distribuzione.

Per evitare conflitti con le distribuzioni simultanee e per garantire voci univoche nella cronologia di distribuzione, assegnare a ogni distribuzione un nome univoco.

## <a name="deploy-template-spec"></a>Distribuire la specifica di modello

Anziché distribuire un modello locale o remoto, è possibile creare una [specifica del modello](template-specs.md). La specifica del modello è una risorsa nella sottoscrizione di Azure che contiene un modello ARM. Consente di condividere in modo sicuro il modello con gli utenti dell'organizzazione. Usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per concedere l'accesso alla specifica del modello. Questa funzionalità è attualmente disponibile in anteprima.

Gli esempi seguenti illustrano come creare e distribuire una specifica del modello. Questi comandi sono disponibili solo se è stata effettuata [l'iscrizione per l'anteprima](https://aka.ms/templateSpecOnboarding).

Per prima cosa, creare la specifica del modello fornendo il modello ARM.

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0 `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateJsonFile ./mainTemplate.json
```

Ottenere quindi l'ID per la specifica del modello e distribuirlo.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Per altre informazioni, vedere [Azure Resource Manager specifiche del modello (anteprima)](template-specs.md).

## <a name="preview-changes"></a>Anteprima modifiche

Prima di distribuire il modello, è possibile visualizzare in anteprima le modifiche che il modello apporterà all'ambiente. Usare l' [operazione](template-deploy-what-if.md) di simulazione per verificare che il modello apporti le modifiche previste. Cosa-se anche il modello viene convalidato per gli errori.

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

Ottenere un valore di parametro da un file è utile quando è necessario fornire i valori di configurazione. Ad esempio, è possibile fornire i valori [cloud-init per una macchina virtuale Linux](../../virtual-machines/linux/using-cloud-init.md).

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

Per altre informazioni sul file dei parametri, vedere [Creare il file di parametri di Resource Manager](parameter-files.md).

Per passare un file dei parametri locale, usare il parametro **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Per passare un file dei parametri esterno, usare il parametro **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Passaggi successivi

- Per eseguire il rollback a una distribuzione corretta quando viene restituito un errore, vedere [Rollback alla distribuzione corretta in caso di errore](rollback-on-error.md).
- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per informazioni su come definire i parametri nel modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
- Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](secure-template-with-sas-token.md).
