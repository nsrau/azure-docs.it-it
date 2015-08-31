<properties
   pageTitle="Distribuire un'applicazione con un modello di Gestione risorse di Azure"
   services="azure-resource-manager"
   description="Usare Gestione risorse di Azure per distribuire un’applicazione in Azure. Un modello è un file JSON e può essere usato dal portale, da PowerShell, dall'interfaccia della riga di comando di Azure per Mac, Linux e Windows o REST."
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
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Distribuire un'applicazione con un modello di Gestione risorse di Azure

In questo argomento viene illustrato come usare i modelli di Gestione risorse di Azure per distribuire l'applicazione in Azure. Viene descritto come distribuire l'applicazione usando Azure PowerShell, l’interfaccia della riga di comando di Azure, API REST o il portale di anteprima di Microsoft Azure.

I modelli di Gestione risorse di Azure consentono di eseguire rapidamente e facilmente il provisioning delle applicazioni in Azure tramite un modello JSON dichiarativo. In un unico modello JSON, è possibile distribuire più servizi, quali macchine virtuali, reti virtuali, archiviazione, servizi app e database. Lo stesso modello viene usato per distribuire più volte e in modo coerente l'applicazione durante ogni fase del ciclo di vita dell'applicazione.

Per semplificare la gestione dell'applicazione, è possibile organizzare tutte le risorse che condividono un ciclo di vita comune in un singolo gruppo di risorse. I gruppi di risorse semplificano la distribuzione, l’aggiornamento e l’eliminazione di tutte le risorse correlate tra loro. Nella maggior parte dei casi, un gruppo di risorse viene associato a una singola applicazione o a un livello di applicazione (per le applicazioni di grandi dimensioni). La risorsa distribuita tramite un modello risiede all'interno di gruppi di una singola risorsa, ma può includere dipendenze in altri gruppi di risorse.

All'interno di un gruppo di risorse, è possibile tenere traccia dell'esecuzione di una distribuzione e visualizzare lo stato della distribuzione e l'output dell’esecuzione del modello.

Quando si distribuisce un'applicazione con un modello, è possibile fornire valori di parametri per personalizzare la modalità di creazione delle risorse. È possibile specificare i valori di questi parametri inline o in un file di parametri.

## Concetti

- Gruppo di risorse: raccolta di entità che condividono un ciclo di vita comune
- Modello di Gestione risorse: file JSON dichiarativo che definisce lo stato obiettivo di una distribuzione
- Distribuzione: operazione che tiene traccia dell'esecuzione di un modello di Gestione risorse
- Parametri: valori forniti dall'utente che esegue la distribuzione per personalizzare le risorse distribuite
- File di parametri: file JSON in cui sono archiviati nomi e valori di parametri 

## Scenari

Con i modelli di Gestione risorse di Azure è possibile:

- Distribuire applicazioni a più livelli complesse, quali Microsoft SharePoint.
- Distribuire le applicazioni più volte e in modo coerente.
- Supportare ambienti di sviluppo, di test e di produzione.
- Visualizzare lo stato delle distribuzioni.
- Risolvere i problemi di distribuzione usando i registri di controllo di distribuzione.

## Distribuire con il portale di anteprima

Novità Ogni applicazione creata con il [portale di anteprima](https://portal.azure.com/) è supportata da un modello di Gestione risorse di Azure. Creando semplicemente una macchina virtuale, una rete virtuale, un account di archiviazione, un servizio app o database tramite il portale, si sfruttano automaticamente i vantaggi di Gestione risorse di Azure. È sufficiente selezionare l'icona **Nuova** e si sarà sulla strada giusta per distribuire un'applicazione con Gestione risorse di Azure.

![Nuovo](./media/resource-group-template-deploy/new.png)

Per altre informazioni sull'uso del portale con Gestione risorse di Azure, vedere [Uso del portale di anteprima di Azure per gestire le risorse di Azure](resource-group-portal.md).


## Distribuire con PowerShell

Se Azure PowerShell non è stato usato in precedenza con Gestione risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).

1. Accedere al proprio account Azure. Una volta specificate le credenziali, il comando restituisce le informazioni relative all'account.

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione che si desidera usare per la distribuzione.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Passare al modulo Gestione risorse di Azure.

        PS C:\> Switch-AzureMode AzureResourceManager

4. Se non è presente un gruppo di risorse, crearne uno nuovo. Specificare il nome del gruppo di risorse e il percorso per la soluzione. Viene restituito un riepilogo del nuovo gruppo di risorse.

        PS C:\> New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando **New-AzureResourceGroupDeployment** e specificare i parametri necessari. I parametri includeranno un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello creato e qualsiasi altro parametro necessario per lo scenario.
   
     Per specificare i valori dei parametri sono disponibili le opzioni seguenti:
   
     - Usare i parametri inline.

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Usare un oggetto parametro.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Usare un file di parametri. Per informazioni sul file di modello, vedere [File di parametri](./#parameter-file).

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. Per ottenere informazioni sugli errori di distribuzione.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Per ottenere informazioni dettagliate sugli errori di distribuzione.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
        
### Video

Ecco una dimostrazione video di utilizzo dei modelli di gestione delle risorse con PowerShell.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Distribuire con l'interfaccia della riga di comando di Azure per Mac, Linux e Windows

Se l’interfaccia della riga comando di Azure non è stata usata in precedenza con Gestione risorse, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md).

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

5. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando seguente e specificare i parametri necessari. I parametri includeranno un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello creato e qualsiasi altro parametro necessario per lo scenario.
   
     Per specificare i valori dei parametri sono disponibili le opzioni seguenti:

     - Usare i parametri inline e un modello locale.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Usare i parametri inline e collegamento a un modello.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Usare un file di parametri. Per informazioni sul file di modello, vedere [File di parametri](./#parameter-file).
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


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
   
3. Creare la distribuzione di un nuovo gruppo di risorse. Fornire l'ID sottoscrizione, il nome del gruppo di risorse per la distribuzione, il nome della distribuzione e il percorso del modello. Per informazioni sul file di modello, vedere [File di parametri](./#parameter-file). Per altre informazioni sull'API REST per creare un gruppo di risorse, vedere [Creare la distribuzione di un modello](https://msdn.microsoft.com/library/azure/dn790564.aspx).
    
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
            }
       }
    }

## Passaggi successivi
- Per un esempio di distribuzione delle risorse con la libreria client .NET, vedere[Distribuire le risorse usando le librerie .NET e un modello](../arm-template-deployment.md)
- Per un esempio dettagliato di distribuzione di un'applicazione, vedere [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](../app-service-web/app-service-deploy-complex-application-predictably.md)
- Per altre informazioni sulle sezioni del modello di Gestione risorse di Azure, vedere [Creazione di modelli](../resource-group-authoring-templates.md)
- Per un elenco delle funzioni che è possibile usare in un modello di Gestione risorse di Azure, vedere [Funzioni di modello](../resource-group-template-functions.md)

 

<!---HONumber=August15_HO8-->