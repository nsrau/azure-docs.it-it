## Come distribuire gruppi di risorse con PowerShell

1. Accedere al proprio account Azure.

          Add-AzureAccount

   Una volta specificate le credenziali, il comando restituisce le informazioni relative all'account.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione che si desidera usare per la distribuzione. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Passare al modulo Gestione risorse di Azure.

          Switch-AzureMode AzureResourceManager

4. Se non è presente un gruppo di risorse, crearne uno nuovo. Specificare il nome del gruppo di risorse e il percorso per la soluzione.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

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

5. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando **New-AzureResourceGroupDeployment** e specificare i parametri necessari. I parametri includeranno un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello creato e qualsiasi altro parametro necessario per lo scenario. 
   
   Per specificare i valori dei parametri sono disponibili le opzioni seguenti:
   
   - Usare i parametri inline.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Usare un oggetto parametro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Usare un file di parametri.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Per ottenere informazioni sugli errori di distribuzione.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Per ottenere informazioni dettagliate sugli errori di distribuzione.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

<!---HONumber=Oct15_HO3-->