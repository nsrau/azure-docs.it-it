## Come distribuire gruppi di risorse con l'interfaccia della riga di comando di Azure
1. Accedere al proprio account Azure.
   
        azure login
   
   Una volta specificate le credenziali, il comando restituisce il risultato dell'accesso.
   
        ...
        info:    login command OK
2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione che si desidera usare per la distribuzione.
   
        azure account set <YourSubscriptionNameOrId>
3. Passare al modulo Gestione risorse di Azure
   
        azure config mode arm
   
   Si riceverà la conferma della nuova modalità.
   
        info:     New mode is arm
4. Se non è presente un gruppo di risorse, crearne uno nuovo. Specificare il nome del gruppo di risorse e il percorso per la soluzione.
   
        azure group create -n ExampleResourceGroup -l "West US"
   
   Viene restituito un riepilogo del nuovo gruppo di risorse.
   
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
   
   * Usare i parametri inline e un modello locale.
     
             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment
   * Usare i parametri inline e collegamento a un modello.
     
             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment
   * Usare un file di parametri.
     
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

<!---HONumber=Oct15_HO3-->