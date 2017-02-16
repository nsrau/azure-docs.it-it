---
title: Distribuire le risorse con il modello e l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: Utilizzare Azure Resource Manager e l&quot;interfaccia della riga di comando di Azure per distribuire una risorsa in Azure. Le risorse sono definite in un modello di Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4851e872349fa6483e1f1a340d0968e845a3518
ms.openlocfilehash: ccbb918a3377094395a27a9b7a63f213c5085027


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md)
> * [Portale](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure con modelli di Resource Manager per distribuire risorse in Azure.  Il modello può essere un file locale oppure un file esterno disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firma di accesso condiviso in fase di distribuzione.

> [!TIP]
> Per informazioni su come eseguire il debug di un errore durante la distribuzione, vedere:
> 
> * [View deployment operations](resource-manager-deployment-operations.md) (Visualizzare le operazioni di distribuzione) per ottenere informazioni per la risoluzione degli errori
> * [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) per informazioni sulla risoluzione degli errori di distribuzione più comuni
> 
> 

## <a name="quick-steps-to-deployment"></a>Azioni rapide per la distribuzione
Per iniziare a usare rapidamente la distribuzione, usare i comandi seguenti:

```
azure group create -n examplegroup -l "West US"
azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g examplegroup -n exampledeployment
```

Questi comandi permettono di creare un gruppo di risorse e distribuirvi un modello. Il file modello e il file dei parametri sono entrambi file locali. Se questo metodo fa al caso proprio, si dispone di tutto il necessario per distribuire le risorse. Altrimenti, sono disponibili altri metodi per specificare le risorse da distribuire. La parte rimanente di questo articolo descrive tutte le opzioni disponibili in fase di distribuzione. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Distribuisci
Se l’interfaccia della riga comando di Azure non è stata usata in precedenza con Gestione risorse, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](xplat-cli-azure-resource-manager.md).

1. Accedere all'account Azure. Una volta specificate le credenziali, il comando restituisce il risultato dell'accesso.
   
   ```
   azure login
   ```
2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione da usare per la distribuzione.
   
   ```
   azure account set <YourSubscriptionNameOrId>
   ```
4. Quando si distribuisce un modello, è necessario specificare un gruppo di risorse che conterrà le risorse distribuite. Se è già presente un gruppo di risorse per la distribuzione, è possibile ignorare questo passaggio e usare semplicemente quel gruppo di risorse. 
   
     Per creare un gruppo di risorse, specificare il nome e il percorso del gruppo di risorse. Viene specificato un percorso per il gruppo di risorse perché nel gruppo di risorse vengono archiviati i metadati delle risorse. Per motivi di conformità può essere opportuno specificare dove vengono archiviati i metadati. In generale è consigliabile specificare un percorso in cui risiederà la maggior parte delle risorse. Usando lo stesso percorso è possibile semplificare il modello.
   
   ```
   azure group create -n ExampleResourceGroup -l "West US"
   ```

     Viene restituito un riepilogo del nuovo gruppo di risorse.
   
5. Convalidare la distribuzione prima di eseguirla usando il comando **azure group template validate** . Durante il test della distribuzione, specificare i parametri esattamente come quando si esegue la distribuzione (illustrata nel passaggio successivo).
   
   ```
   azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup
   ```
6. Per distribuire le risorse al gruppo di risorse, eseguire il comando **azure group deployment create** e specificare i parametri necessari. I parametri includono un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello e qualsiasi altro parametro necessario per lo scenario. Se il parametro **mode** non è specificato, viene usato il valore predefinito **Incremental** (Incrementale). Per eseguire una distribuzione completa, impostare **mode** su **Complete** (Completo). Quando si utilizza la modalità di completamento, fare attenzione a non eliminare inavvertitamente le risorse non presenti nel modello.
   
     Per distribuire un modello locale, usare il parametro **template-file**:
   
   ```
   azure group deployment create --resource-group examplegroup --template-file "c:\MyTemplates\example.json"
   ```
   
     Per distribuire un modello esterno, usare il parametro **template-uri**:
   
   ```
   azure group deployment create --resource-group examplegroup --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json"
   ```
   
     I due esempi precedenti non includono i valori dei parametri. Informazioni sulle opzioni per la trasmissione dei valori dei parametri nella sezione [Parametri](#parameters). Per il momento, viene chiesto di specificare i valori dei parametri con la sintassi seguente:

   ```
   info:    Executing command group deployment create
   info:    Supply values for the following parameters
   firstParameters:  <type here>
   ```

      Una volta distribuite le risorse, viene visualizzato un riepilogo della distribuzione. Il riepilogo include l'informazione **ProvisioningState**, che indica se la distribuzione ha avuto esito positivo.

   ```
   + Initializing template configurations and parameters
   + Creating a deployment
   info:    Created template deployment "example"
   + Waiting for deployment to complete
   +
   +
   data:    DeploymentName     : example
   data:    ResourceGroupName  : examplegroup
   data:    ProvisioningState  : Succeeded
   ```
7. Per registrare informazioni aggiuntive sulla distribuzione che potrebbero contribuire a risolvere eventuali errori di distribuzione, usare il parametro **debug-setting** . È possibile specificare la registrazione del contenuto della richiesta, del contenuto della risposta o di entrambi con l'operazione di distribuzione.
   
   ```
   azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g examplegroup -n exampleDeployment
   ```

## <a name="deploy-template-from-storage-with-sas-token"></a>Distribuire modelli dall'archiviazione con token SAS
È possibile aggiungere i modelli a un account di archiviazione e collegarli durante la distribuzione con un token SAS.

> [!IMPORTANT]
> Attenendosi alla seguente procedura, il BLOB contenente il modello sarà accessibile solo da parte del proprietario dell'account. Tuttavia, quando si crea un token di firma di accesso condiviso per il BLOB, quest'ultimo sarà accessibile a tutti gli utenti con quell'URI. Se l'URI viene intercettato da un altro utente, quest'ultimo sarà in grado di accedere al modello. Utilizzare un token di firma di accesso condiviso è un buon metodo per limitare l'accesso ai modelli, ma è necessario non includere direttamente nel modello dati sensibili come le password.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Aggiungere un modello privato all'account di archiviazione
Configurare un account di archiviazione per i modelli nel modo seguente:

1. Creare un gruppo di risorse.
   
   ```
   azure group create -n "ManageGroup" -l "westus"
   ```
2. Creare un account di archiviazione. Il nome dell'account di archiviazione deve essere univoco in tutto l'ambiente di Azure, pertanto assegnare all'account il proprio nome.
   
   ```
   azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates
   ```
3. Impostare le variabili della chiave e dell'account di archiviazione.
   
   ```
   export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
   export AZURE_STORAGE_ACCESS_KEY={storage_account_key}
   ```
4. Creare un contenitore L'autorizzazione è impostata su **Off**, pertanto il contenitore è accessibile solo al proprietario.
   
   ```
   azure storage container create --container templates -p Off 
   ```
5. Aggiungere il proprio modello al contenitore.
   
   ```
   azure storage blob upload --container templates -f c:\MyTemplates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Fornire il token SAS in fase di distribuzione
Per distribuire un modello privato in un account di archiviazione, recuperare un token SAS e includerlo nell'URI del modello.

1. Creare un token di accesso condiviso con autorizzazioni di lettura e un'ora di scadenza per limitare l'accesso. Impostare l'ora di scadenza in modo da garantire un tempo sufficiente per completare la distribuzione. Recuperare l'URI completo del modello che include il token SAS.
   
   ```
   expiretime=$(date -I'minutes' --date "+30 minutes")
   fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")
   ```
2. Distribuire il modello fornendo l'URI che include il token SAS.
   
   ```
   azure group deployment create --template-uri $fullurl -g ExampleResourceGroup
   ```

Per un esempio sull'uso di un token di firma di accesso condiviso con modelli collegati, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>Parametri

Per specificare i valori dei parametri sono disponibili le opzioni seguenti: 
   
- Usare i parametri inline. Ogni parametro è nel formato: `"ParameterName": { "value": "ParameterValue" }`. L'esempio seguente mostra i parametri con caratteri di escape.

   ```   
   azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment
   ```
- Usare un file di parametri.

  ```    
  azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g ExampleResourceGroup -n ExampleDeployment
  ```
      

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Passaggi successivi
* Per un esempio di distribuzione delle risorse con la libreria client .NET, vedere [Distribuire le risorse usando le librerie .NET e un modello](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per definire i parametri nel modello, vedere [Creazione di modelli](resource-group-authoring-templates.md#parameters).
* Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).
* Per informazioni dettagliate sull'uso di un riferimento KeyVault per passare valori protetti, vedere [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
* Per una serie di quattro parti sull'automazione della distribuzione, vedere [Automazione della distribuzione di applicazioni nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questa serie illustra argomenti come architettura, accesso e sicurezza, disponibilità, scalabilità e distribuzione delle applicazioni.




<!--HONumber=Jan17_HO2-->


