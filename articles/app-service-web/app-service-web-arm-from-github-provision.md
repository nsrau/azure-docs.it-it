<properties 
	pageTitle="Distribuire un'app Web collegata a un repository GitHub" 
	description="Usare un modello di Gestione risorse di Azure per distribuire un'app Web che contiene un progetto da un repository GitHub." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# Distribuire un'app Web collegata a un repository GitHub

In questo argomento si apprenderà come creare un modello di gestione risorse di Azure che consente di distribuire un'app Web collegata a un progetto in un repository GitHub. Verrà illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per il modello completo, vedere [App Web collegata al modello GitHub](https://github.com/tfitzmac/AppServiceTemplates/blob/master/WebAppLinkedToGithub.json).

## Elementi distribuiti

Con questo modello verrà distribuita un'app Web che contiene il codice da un progetto in GitHub.

## Parametri

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]
    
## Risorse da distribuire

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### App Web

Crea l'app Web collegata al progetto in GitHub.

Il nome dell'app Web viene specificato tramite il parametro **siteName** e il percorso dell'app Web tramite il parametro **siteLocation**. Nell'elemento **dependsOn** il modello consente di definire l'app Web dipendente dal piano di hosting del servizio. Dipendendo dal piano di hosting, l'app Web non viene creata prima del termine della creazione del piano di hosting. L'elemento **dependsOn** viene usato solo per specificare l'ordine di distribuzione. Se non si contrassegna l'app Web come dipendente dal piano di hosting, Gestione risorse di Azure tenterà di creare entrambe le risorse contemporaneamente. È possibile che si verifichi un errore se l'app Web viene creata prima del piano di hosting.

L'app Web dispone anche di una risorsa figlio che viene definita nella sezione delle **risorse** riportata di seguito. Questa risorsa figlio consente di definire il controllo del codice sorgente per il progetto distribuito con l'app Web. In questo modello, il controllo del codice sorgente è collegato a un determinato repository GitHub. Il repository GitHub viene definito con il codice **"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"** È possibile impostare come hardcoded l'URL del repository, se si desidera creare un modello che distribuisce ripetutamente un progetto singolo richiedendo il numero minimo di parametri. Anziché impostare come hardcoded l'URL del repository, è possibile aggiungere un parametro per l'URL del repository e usare tale valore per la proprietà **RepoUrl**. È possibile vedere un esempio del parametro dell'URL del repository nel [modello dell'app Web con processi Web](../app-service-web-deploy-web-app-with-webjobs.md).

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Interfaccia della riga di comando di Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json


 

<!---HONumber=62-->