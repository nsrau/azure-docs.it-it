<properties
	pageTitle="Distribuire un'app Web usando MSDeploy con il nome host personalizzato e il certificato SSL"
	description="Usare un modello di Gestione risorse di Azure per distribuire un'app Web usando MSDeploy e configurando il nome host personalizzato e un certificato SSL"
	services="app-service\web"
	documentationCenter=""
	authors="jodehavi"
	/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="john.dehavilland"/>

# Distribuire un'app Web con MSDeploy, il nome host personalizzato e il certificato SSL

Questa guida illustra la creazione di una distribuzione end-to-end per un'app Web di Azure, usando MSDeploy e aggiungendo un nome host personalizzato e un certificato SSL al modello di Gestione risorse di Azure.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

###Creare l'applicazione di esempio

Si distribuirà un'applicazione Web ASP.NET. Il primo passaggio prevede la creazione di una semplice applicazione Web. Se invece si sceglie di usarne una esistente, è possibile saltare questo passaggio.

Aprire Visual Studio 2015 e scegliere File > Nuovo progetto. Nella finestra di dialogo visualizzata scegliere Web > Applicazione Web ASP.NET. In Modelli scegliere Web e quindi il modello MVC. Impostare _Modifica tipo di autenticazione_ su _Nessuna autenticazione_. In questo modo l'applicazione di esempio sarà il più semplice possibile.

A questo punto si avrà un'app Web ASP.Net di base da usare durante il processo di distribuzione.

###Creare il pacchetto MSDeploy

Il prossimo passaggio prevede la creazione del pacchetto per distribuire l'app Web in Azure. A questo scopo, salvare il progetto e quindi eseguire quanto segue dalla riga di comando:

	msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Verrà creato un pacchetto compresso nella cartella PackageLocation. L'applicazione ora è pronta per la distribuzione che è possibile eseguire compilando un modello di Gestione risorse di Azure.

###Creare il modello di Gestione risorse di Azure
Iniziare con un modello di Gestione risorse di Azure di base che creerà un'applicazione Web e un piano di hosting. Si noti che i parametri e le variabili non vengono visualizzati per brevità.

	{
		"name": "[parameters('appServicePlanName')]",
		"type": "Microsoft.Web/serverfarms",
		"location": "[resourceGroup().location]",
		"apiVersion": "2014-06-01",
		"dependsOn": [ ],
		"tags": {
		    "displayName": "appServicePlan"
		},
		"properties": {
		    "name": "[parameters('appServicePlanName')]",
		    "sku": "[parameters('appServicePlanSKU')]",
		    "workerSize": "[parameters('appServicePlanWorkerSize')]",
		    "numberOfWorkers": 1
		}
	},
	{
		"name": "[variables('webAppName')]",
		"type": "Microsoft.Web/sites",
		"location": "[resourceGroup().location]",
		"apiVersion": "2015-08-01",
		"dependsOn": [
		    "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		],
		"tags": {
		    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
		    "displayName": "webApp"
		},
		"properties": {
		    "name": "[variables('webAppName')]",
		    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		}
	}

Sarà quindi necessario modificare la risorsa app Web per poter accettare una risorsa MSDeploy annidata. Questo consentirà di fare riferimento al pacchetto creato in precedenza e di indicare a Gestione risorse di Azure di usare MSDeploy per distribuire il pacchetto nell'app Web di Azure. Il codice seguente mostra la risorsa Microsoft.Web/sites con la risorsa MSDeploy annidata:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Si noterà ora che la risorsa MSDeploy include una proprietà **packageUri** definita come segue:

	"packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Questo **packageUri** include l'URI dell'account di archiviazione che punta all'account di archiviazione in cui si caricherà il file zip del pacchetto. Gestione risorse di Azure userà le [firme di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md) per effettuare il pull del pacchetto in locale verso il basso dall'account di archiviazione quando si distribuisce il modello. Questo processo verrà automatizzato con uno script PowerShell che caricherà il pacchetto e chiamerà l'API Gestione di Azure per creare le chiavi necessarie e passarle nel modello come parametri (*\_artifactsLocation* e *\_artifactsLocationSasToken*). Sarà necessario definire i parametri per la cartella e il nome file in cui il pacchetto viene caricato nel contenitore di archiviazione.

Ora è necessario aggiungere un'altra risorsa annidata per configurare le associazioni nome host per sfruttare un dominio personalizzato. Prima sarà necessario assicurarsi di essere proprietari del nome host e configurarlo in modo che Azure ne verifichi il proprietario. Vedere [Configurare un nome di dominio personalizzato nel servizio app di Azure](web-sites-custom-domain-name.md). A questo punto è possibile aggiungere il codice seguente al modello nella sezione della risorsa Microsoft.Web/sites:

	{
		"apiVersion": "2015-08-01",
		"type": "hostNameBindings",
		"name": "www.yourcustomdomain.com",
		"dependsOn": [
		    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
		],
		"properties": {
		    "domainId": null,
		    "hostNameType": "Verified",
		    "siteName": "variables('webAppName')"
		}
	}

Infine è necessario aggiungere un'altra risorsa di primo livello, Microsoft.Web/certificates. Questa risorsa conterrà il certificato SSL ed esisterà nello stesso livello dell'app Web e del piano di hosting.

	{
	    "name": "[parameters('certificateName')]",
	    "apiVersion": "2014-04-01",
	    "type": "Microsoft.Web/certificates",
	    "location": "[resourceGroup().location]",
	    "properties": {
	        "pfxBlob": "pfx base64 blob",
	        "password": "some pass"
	    }
	}

Per configurare questa risorsa, sarà necessario un certificato SSL valido. Una volta disponibile il certificato valido, è necessario estrarre i byte pfx come stringa base64. Per estrarli, è possibile usare il comando PowerShell seguente:

	$fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

	[System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

È quindi possibile passarli come parametro al modello di distribuzione di Gestione risorse di Azure.

A questo punto il modello di Gestione risorse di Azure è pronto.

###Modello di distribuzione

I passaggi finali consentono di realizzare una distribuzione end-to-end completa. Per semplificare la distribuzione, è possibile sfruttare lo script PowerShell **Deploy-AzureResourceGroup.ps1** aggiunto quando si crea un progetto gruppo di risorse di Azure in Visual Studio per rendere più facile il caricamento degli elementi necessari nel modello. È necessario creare in anticipo l'account di archiviazione che si vuole usare. Per questo esempio, è stato creato un account di archiviazione condiviso per il file package.zip da caricare. Lo script userà AzCopy per caricare il pacchetto nell'account di archiviazione. Passando il percorso della cartella degli elementi, lo script caricherà automaticamente tutti i file di tale directory nel contenitore di archiviazione denominato. Dopo avere chiamato Deploy-AzureResourceGroup.ps1, è necessario aggiornare le associazioni SSL per eseguire il mapping del nome host personalizzato al certificato SSL.

Lo script PowerShell seguente illustra la distribuzione completa chiamando Deploy-AzureResourceGroup.ps1:

	#Set resource group name
	$rgName = "Name-of-resource-group"

	#call deploy-azureresourcegroup script to deploy web app

	.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
									-ResourceGroupName $rgName `
									-UploadArtifacts "container-name" `
									-StorageAccountName "name-of-storage-acct-for-package" `
									-StorageAccountResourceGroupName "resource-group-name-storage-acct" `
									-TemplateFile "web-app-deploy.json" `
									-TemplateParametersFile "web-app-deploy-parameters.json" `
									-ArtifactStagingDirectory "C:\path\to\packagefolder"

	#update web app to bind ssl certificate to hostname. This has to be done after creation above.

	$cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

	$ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

	$props = $ar.Properties

	$props.HostNameSslStates[2].'SslState' = 1
	$props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
	$props.hostNameSslStates[2].'toUpdate' = $true

	Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

A questo punto l'applicazione è stata distribuita ed è possibile accedervi tramite https://www.yourcustomdomain.com

<!---HONumber=AcomDC_0107_2016-->