<properties
   pageTitle="Distribuire le risorse con il modello di gestione risorse | Microsoft Azure"
   services="azure-resource-manager"
   description="Usare Gestione risorse di Azure per distribuire una risorsa in Azure. Un modello è un file JSON e può essere usato dal portale, da PowerShell, dall'interfaccia della riga di comando di Azure per Mac, Linux e Windows o REST."
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Distribuire le risorse con i modelli di Azure Resource Manager

In questo argomento viene illustrato come usare i modelli di Gestione risorse di Azure per distribuire le risorse in Azure. Viene descritto come distribuire le risorse usando Azure PowerShell, l’interfaccia della riga di comando di Azure, l'API REST o il Portale di Azure.

Quando si distribuisce la definizione di un'applicazione con un modello, è possibile fornire valori di parametri per personalizzare la modalità di creazione delle risorse. È possibile specificare i valori di questi parametri inline o in un file di parametri.

## Distribuzioni incrementali e complete

Per impostazione predefinita, Gestione risorse gestisce le distribuzioni come aggiornamenti incrementali al gruppo di risorse. Con la distribuzione incrementale, Gestione risorse:

- **lascia invariate** le risorse presenti nel gruppo di risorse, ma non specificate nel modello
- **aggiunge** le risorse specificate nel modello, ma non presenti nel gruppo di risorse 
- **non esegue nuovamente il provisioning** delle risorse presenti nel gruppo di risorse alle stesse condizioni definite nel modello

Con la distribuzione completa, Gestione risorse:

- **elimina** le risorse presenti nel gruppo di risorse, ma non specificate nel modello
- **aggiunge** le risorse specificate nel modello, ma non presenti nel gruppo di risorse 
- **non esegue nuovamente il provisioning** delle risorse presenti nel gruppo di risorse alle stesse condizioni definite nel modello
 
Specificare il tipo di distribuzione tramite la proprietà **Mode**, come illustrato negli esempi seguenti.

## Distribuire con PowerShell

1. Accedere al proprio account Azure. Una volta specificate le credenziali, il comando restituisce le informazioni relative all'account.

        PS C:\> Login-AzureRmAccount

        Environment : AzureCloud
        Account    : someone@example.com
        ...


2. Se si dispone di più sottoscrizioni, specificare l'ID sottoscrizione che si vuole usare per la distribuzione con il comando **Select-AzureRmSubscription**.

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. Se non si dispone di un gruppo di risorse esistente, creare un nuovo gruppo di risorse con il comando **New-AzureRmResourceGroup**. Specificare il nome del gruppo di risorse e il percorso per la soluzione.

        PS C:\> New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Viene restituito un riepilogo del nuovo gruppo di risorse.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Convalidare la distribuzione prima dell'esecuzione, eseguendo il cmdlet **Test AzureRmResourceGroupDeployment**. Durante il test della distribuzione, specificare i parametri esattamente come quando si esegue la distribuzione (illustrata nel passaggio successivo).

        PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

5. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando **New-AzureRmResourceGroupDeployment** e specificare i parametri necessari. I parametri includeranno un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello creato e qualsiasi altro parametro necessario per lo scenario. Se il parametro **Mode** non è specificato, viene usato il valore predefinito **Incremental**.
   
     Per specificare i valori dei parametri sono disponibili le opzioni seguenti:
   
     - Usare i parametri inline.

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Usare un oggetto parametro.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Usare un file di parametri. Per informazioni sul file di modello, vedere [File di parametri](./#parameter-file).

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Per eseguire una distribuzione completa, impostare **Mode** su **Complete**. Si noti che viene richiesto di confermare se si desidera utilizzare la modalità di completamento, che potrebbe comportare l'eliminazione delle risorse.

        PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -Mode Complete -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> 
        Confirm
        Are you sure you want to use the complete deployment mode? Resources in the resource group 'ExampleResourceGroup' which are not
        included in the template will be deleted.
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

     Se il modello include un parametro con un nome corrispondente a uno dei parametri nel comando per la distribuzione del modello, ad esempio un parametro denominato **ResourceGroupName** nel modello che corrisponde al parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx), verrà richiesto di specificare un valore per un parametro con il suffisso **FromTemplate**, ad esempio **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione non attribuendo ai parametri lo stesso nome dei parametri usati per operazioni di distribuzione.

6. Per ottenere informazioni sugli errori di distribuzione.

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment
        
        
### Video

Ecco una dimostrazione video di utilizzo dei modelli di gestione delle risorse con PowerShell.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Distribuire con l'interfaccia della riga di comando di Azure

Se l’interfaccia della riga comando di Azure non è stata usata in precedenza con Gestione risorse, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](xplat-cli-azure-resource-manager.md).

1. Accedere al proprio account Azure. Una volta specificate le credenziali, il comando restituisce il risultato dell'accesso.

        azure login
  
        ...
        info:    login command OK

2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione che si desidera usare per la distribuzione.

        azure account set <YourSubscriptionNameOrId>

3. Passare al modulo Gestione risorse di Azure. Si riceverà la conferma della nuova modalità.

        azure config mode arm
   
        info:     New mode is arm

4. Se non è presente un gruppo di risorse, crearne uno nuovo. Specificare il nome del gruppo di risorse e il percorso per la soluzione. Viene restituito un riepilogo del nuovo gruppo di risorse.

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Convalidare la distribuzione prima dell'esecuzione, eseguendo il comando **azure group template validate**. Durante il test della distribuzione, specificare i parametri esattamente come quando si esegue la distribuzione (illustrata nel passaggio successivo).

        azure group template vaildate -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup

5. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando seguente e specificare i parametri necessari. I parametri includeranno un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello creato e qualsiasi altro parametro necessario per lo scenario.
   
     Per specificare i valori dei parametri sono disponibili le opzioni seguenti:

     - Usare i parametri inline e un modello locale. Ogni parametro è nel formato: `"ParameterName": { "value": "ParameterValue" }`. Nell'esempio seguente vengono illustrati i parametri con caratteri di escape.

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - Usare i parametri inline e collegamento a un modello.

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - Usare un file di parametri. Per informazioni sul file di modello, vedere [File di parametri](./#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Per eseguire una distribuzione completa, impostare **Mode** su **Complete**.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Per ottenere informazioni sull'ultima distribuzione.

        azure group log show -l ExampleResourceGroup

7. Per ottenere informazioni dettagliate sugli errori di distribuzione.
      
        azure group log show -l -v ExampleResourceGroup

## Distribuire con l'API REST
1. Impostare [parametri e intestazioni comuni](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), tra cui i token di autenticazione.
2. Se non è presente un gruppo di risorse, crearne uno nuovo. Specificare l'ID sottoscrizione, il nome del nuovo gruppo di risorse e il percorso per la soluzione. Per ulteriori informazioni, vedere [Creare un gruppo di risorse](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Convalidare la distribuzione prima dell'esecuzione, eseguendo l’operazione [Convalidare una distribuzione modello](https://msdn.microsoft.com/library/azure/dn790547.aspx). Durante il test della distribuzione, specificare i parametri esattamente come quando si esegue la distribuzione (illustrata nel passaggio successivo).

3. Creare la distribuzione di un nuovo gruppo di risorse. Fornire l'ID sottoscrizione, il nome del gruppo di risorse per la distribuzione, il nome della distribuzione e il percorso del modello. Per informazioni sul file di modello, vedere [File di parametri](./#parameter-file). Per altre informazioni sull'API REST per creare un gruppo di risorse, vedere [Creare la distribuzione di un modello](https://msdn.microsoft.com/library/azure/dn790564.aspx). Si noti che la **modalità** è impostata su **Incremental**. Per eseguire una distribuzione completa, impostare **Mode** su **Complete**.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
4. Ottenere lo stato della distribuzione del modello. Per altre informazioni, vedere [Ottenere informazioni sulla distribuzione di un modello](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## Distribuire con Visual Studio

Con Visual Studio, è possibile creare un progetto del gruppo di risorse e distribuirlo in Azure mediante l'interfaccia utente. Selezionare il tipo di risorse da includere nel progetto e le risorse vengono aggiunte automaticamente al modello di Gestione risorse. Il progetto fornisce anche uno script di PowerShell per distribuire il modello.

Per un'introduzione all'uso di Visual Studio con gruppi di risorse, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Distribuire con il portale

Novità Ogni applicazione creata con il [portale](https://portal.azure.com/) è supportata da un modello di Gestione risorse di Azure. Creando semplicemente una macchina virtuale, una rete virtuale, un account di archiviazione, un servizio app o database tramite il portale, si sfruttano automaticamente i vantaggi di Gestione risorse di Azure. È sufficiente selezionare l'icona **Nuova** e si sarà sulla strada giusta per distribuire un'applicazione con Gestione risorse di Azure.

![Nuovo](./media/resource-group-template-deploy/new.png)

Tutte le distribuzioni tramite il portale vengono convalidate automaticamente prima dell'esecuzione. Per altre informazioni sull'uso del portale con Gestione risorse di Azure, vedere [Uso del portale di Azure per gestire le risorse di Azure](azure-portal/resource-group-portal.md).


## File di parametri

Se si usa un file di parametri per passare i valori di parametro al modello durante la distribuzione, è necessario creare un file JSON con un formato simile all'esempio seguente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            },
            "adminPassword": {
                "reference": {
                   "keyVault": {
                      "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                   }, 
                   "secretName": "sqlAdminPassword" 
                }   
            }
       }
    }

La dimensione del file di parametro non può essere superiore a 64 KB.

Per informazioni su come definire i parametri nel modello, vedere [Creazione di modelli](../resource-group-authoring-templates/#parameters). Per informazioni dettagliate sul riferimento a KeyVault per passare valori sicuri, vedere [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md)

## Passaggi successivi
- Per un esempio di distribuzione delle risorse con la libreria client .NET, vedere [Distribuire le risorse usando le librerie .NET e un modello](virtual-machines/virtual-machines-windows-csharp-template.md).
- Per un esempio dettagliato di distribuzione di un'applicazione, vedere [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).
- Per informazioni sulle sezioni del modello di Gestione risorse di Azure, vedere [Creazione di modelli](resource-group-authoring-templates.md).
- Per un elenco delle funzioni che è possibile usare in un modello di Gestione risorse di Azure, vedere [Funzioni di modello](resource-group-template-functions.md).

 

<!---HONumber=AcomDC_0406_2016-->