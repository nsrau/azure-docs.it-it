---
title: Distribuire risorse con PowerShell e i modelli | Microsoft Docs
description: Utilizzare Azure Resource Manager e Azure PowerShell per distribuire una risorsa in Azure. Le risorse sono definite in un modello di Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5b8b293b5b37365088a3df55581be7b7bf76691c
ms.openlocfilehash: 7bc3421e00215ca4629ea11811c98e581377b24a
ms.lasthandoff: 01/18/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md)
> * [Portale](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Questo articolo illustra come usare Azure PowerShell con modelli di Resource Manager per distribuire risorse in Azure. Il modello può essere un file locale oppure un file esterno disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firma di accesso condiviso in fase di distribuzione.

> [!TIP]
> Per informazioni su come eseguire il debug di un errore durante la distribuzione, vedere:
> 
> * [View deployment operations](resource-manager-deployment-operations.md) (Visualizzare le operazioni di distribuzione) per ottenere informazioni per la risoluzione degli errori
> * [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) per informazioni sulla risoluzione degli errori di distribuzione più comuni
> 
> 

## <a name="quick-steps-to-deployment"></a>Azioni rapide per la distribuzione
Per iniziare a usare rapidamente la distribuzione, usare i comandi seguenti:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json -TemplateParameterFile c:\MyTemplates\example.params.json
```

Questi comandi permettono di creare un gruppo di risorse e distribuirvi un modello. Il file modello e il file dei parametri sono entrambi file locali. Se questo metodo fa al caso proprio, si dispone di tutto il necessario per distribuire le risorse. Altrimenti, sono disponibili altri metodi per specificare le risorse da distribuire. La parte rimanente di questo articolo descrive tutte le opzioni disponibili in fase di distribuzione. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Distribuire
1. Accedere all'account Azure.

   ```powershell
   Login-AzureRmAccount
   ```

    Viene restituito un riepilogo dell'account.
    
   ```powershell
   Environment           : AzureCloud
   Account               : someone@example.com
   TenantId              : {guid}
   SubscriptionId        : {guid}
   SubscriptionName      : Example Subscription
   CurrentStorageAccount :
   ```

2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione da usare per la distribuzione con il comando **Set-AzureRmContext**. 
   
   ```powershell
   Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
   ```
3. Quando si distribuisce un modello, è necessario specificare un gruppo di risorse che conterrà le risorse distribuite. Se è già presente un gruppo di risorse per la distribuzione, è possibile ignorare questo passaggio e usare semplicemente quel gruppo di risorse. 
   
     Per creare un gruppo di risorse, specificare il nome e il percorso del gruppo di risorse. Viene specificato un percorso per il gruppo di risorse perché nel gruppo di risorse vengono archiviati i metadati delle risorse. Per motivi di conformità può essere opportuno specificare dove vengono archiviati i metadati. In generale è consigliabile specificare un percorso in cui risiederà la maggior parte delle risorse. Usando lo stesso percorso è possibile semplificare il modello.
   
   ```powershell
   New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   ```
   
     Viene restituito un riepilogo del nuovo gruppo di risorse.
4. Prima di eseguire la distribuzione, è possibile convalidare le impostazioni di distribuzione. Il cmdlet **Test-AzureRmResourceGroupDeployment** consente di individuare eventuali problemi prima di creare le risorse effettive. L'esempio seguente mostra come convalidare una distribuzione.
   
   ```powershell
   Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>
   ```
5. Per distribuire le risorse al gruppo di risorse, eseguire il comando **New-AzureRmResourceGroupDeployment** e specificare i parametri necessari. I parametri includono un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello e qualsiasi altro parametro necessario per lo scenario. Se il parametro **Mode** non è specificato, viene usato il valore predefinito **Incremental**. Per eseguire una distribuzione completa, impostare **Mode** su **Complete** (Completo). Quando si utilizza la modalità di completamento, fare attenzione a non eliminare inavvertitamente le risorse non presenti nel modello.
   
     Per distribuire un modello locale, usare il parametro **TemplateFile**:
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json
   ```
   
     Per distribuire un modello esterno, usare il parametro **TemplateUri**:
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json
   ```
   
     I due esempi precedenti non includono i valori dei parametri. Informazioni sulle opzioni per la trasmissione dei valori dei parametri nella sezione [Parametri](#parameters). Per il momento, viene chiesto di specificare i valori dei parametri con la sintassi seguente:

   ```powershell  
   cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
   Supply values for the following parameters:
   (Type !? for Help.)
   firstParameter: <type here>
   ```
     
     Una volta distribuite le risorse, viene visualizzato un riepilogo della distribuzione. Il riepilogo include l'informazione **ProvisioningState**, che indica se la distribuzione ha avuto esito positivo.

   ```powershell   
   DeploymentName    : ExampleDeployment
   ResourceGroupName : ExampleResourceGroup
   ProvisioningState : Succeeded
   Timestamp         : 4/14/2015 7:00:27 PM
   Mode              : Incremental
   ```
      
6. Per registrare informazioni aggiuntive sulla distribuzione che potrebbero contribuire a risolvere eventuali errori di distribuzione, usare il parametro **DeploymentDebugLogLevel**. È possibile specificare la registrazione del contenuto della richiesta, del contenuto della risposta o di entrambi con l'operazione di distribuzione.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
   ```
   
     Per altre informazioni sull'uso di questo contenuto di debug per risolvere i problemi relativi alle distribuzioni, vedere [View deployment operations](resource-manager-deployment-operations.md) (Visualizzare le operazioni di distribuzione).

## <a name="deploy-private-template-with-sas-token"></a>Distribuire il modello privato con token SAS
È possibile aggiungere i modelli a un account di archiviazione e collegarli durante la distribuzione con un token SAS.

> [!IMPORTANT]
> Attenendosi alla seguente procedura, il BLOB contenente il modello sarà accessibile solo da parte del proprietario dell'account. Tuttavia, quando si crea un token di firma di accesso condiviso per il BLOB, quest'ultimo sarà accessibile a tutti gli utenti con quell'URI. Se l'URI viene intercettato da un altro utente, quest'ultimo sarà in grado di accedere al modello. Utilizzare un token di firma di accesso condiviso è un buon metodo per limitare l'accesso ai modelli, ma è necessario non includere direttamente nel modello dati sensibili come le password.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Aggiungere un modello privato all'account di archiviazione
Configurare un account di archiviazione per i modelli nel modo seguente:

1. Creare un gruppo di risorse.
   
   ```powershell
   New-AzureRmResourceGroup -Name ManageGroup -Location "West US"
   ```
2. Creare un account di archiviazione. Il nome dell'account di archiviazione deve essere univoco in tutto l'ambiente di Azure, pertanto assegnare all'account il proprio nome.
   
   ```powershell
   New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"
   ```
3. Impostare l'account di archiviazione corrente.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
4. Creare un contenitore L'autorizzazione è impostata su **Off**, pertanto il contenitore è accessibile solo al proprietario.
   
   ```powershell
   New-AzureStorageContainer -Name templates -Permission Off
   ```
5. Aggiungere il proprio modello al contenitore.
   
   ```powershell
   Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Fornire il token SAS in fase di distribuzione
Per distribuire un modello privato in un account di archiviazione, recuperare un token SAS e includerlo nell'URI del modello.

1. Se l'account di archiviazione corrente è stato modificato, impostarlo su quello contenente i propri modelli.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
2. Creare un token di accesso condiviso con autorizzazioni di lettura e un'ora di scadenza per limitare l'accesso. Recuperare l'URI completo del modello che include il token SAS.
   
   ```powershell
   $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
   ```
3. Distribuire il modello fornendo l'URI che include il token SAS.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri
   ```

Per un esempio sull'uso di un token di firma di accesso condiviso con modelli collegati, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>Parametri

Per specificare i valori dei parametri sono disponibili le opzioni seguenti: 
   
- Parametri inline. Includere i nomi dei singoli parametri nel cmdlet (ad esempio, **-NomeParametro**).

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"
   ```
- Oggetto parametro. Includere il parametro **-TemplateParameterObject**.

   ```powershell   
   $parameters = @{"<ParameterName>"="<Parameter Value>"}
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters
   ```
- File dei parametri locali. Includere il parametro **-TemplateParameterFile**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile c:\MyTemplates\example.params.json
   ```
- File dei parametri esterni. Includere il parametro **-TemplateParameterUri**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.params.json
   ```
      
[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)] 

Se il modello include un parametro con lo stesso nome di uno dei parametri nel comando di PowerShell, viene richiesto di fornire un valore per il parametro. Azure PowerShell presenta il parametro incluso nel modello con il suffisso **FromTemplate**. Ad esempio, un parametro denominato **ResourceGroupName** nel modello sarà in conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Verrà quindi richiesto di fornire un valore per **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione non attribuendo ai parametri lo stesso nome dei parametri usati per operazioni di distribuzione.

## <a name="parameter-precedence"></a>Precedenza dei parametri

È possibile usare i parametri inline e un file di parametri locale nella stessa operazione di distribuzione. Ad esempio, è possibile specificare alcuni valori nel file di parametri locale e aggiungere altri valori inline durante la distribuzione. Se si specificano valori per un parametro sia nel file dei parametri locale che inline, il valore inline ha la precedenza.

Tuttavia, quando si usa un file di parametri esterni, non è possibile trasmettere altri valori, né inline né da un file locale. Quando si specifica un file di parametri nel parametro **TemplateParameterUri**, tutti i parametri inline vengono ignorati. È necessario fornire tutti i valori dei parametri presenti nel file esterno. Se il modello include un valore importante che non è possibile includere nel file dei parametri, aggiungere tale valore a un insieme di credenziali delle chiavi oppure fornire inline tutti valori dei parametri in modo dinamico.

## <a name="next-steps"></a>Passaggi successivi
* Per un esempio di distribuzione delle risorse con la libreria client .NET, vedere [Distribuire le risorse usando le librerie .NET e un modello](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per definire i parametri nel modello, vedere [Creazione di modelli](resource-group-authoring-templates.md#parameters).
* Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
* Per una serie di quattro parti sull'automazione della distribuzione, vedere [Automazione della distribuzione di applicazioni nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questa serie illustra argomenti come architettura, accesso e sicurezza, disponibilità, scalabilità e distribuzione delle applicazioni.


