<properties
	pageTitle="Personalizzare il modello di Resource Manager esportato | Microsoft Azure"
	description="Aggiungere parametri a un modello di Azure Resource Manager esportato e ridistribuirlo tramite Azure PowerShell o l'interfaccia della riga di comando di Azure."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/10/2016"
	ms.author="tomfitz"/>

# Personalizzare un modello di Azure Resource Manager esportato

Questo argomento illustra come modificare un modello esportato per consentire il passaggio di valori aggiuntivi come parametri. Si basa sui passaggi eseguiti nell'articolo [Esportare il modello di Resource Manager](resource-manager-export-template.md), ma non è essenziale completare prima la procedura illustrata in quell'articolo. Il modello e gli script necessari sono disponibili in questo articolo.

## Visualizzare un modello esportato

Se sono state completate le procedure di [Esportare il modello di Resource Manager](resource-manager-export-template.md), aprire il modello scaricato. Il nome del modello è **template.json**. Se è disponibile Visual Studio o Visual Code, è possibile usarlo per modificare il modello. In caso contrario, è possibile usare qualsiasi editor JSON o qualsiasi editor di testo.

Se non è stata completata la procedura dettagliata precedente, creare un file denominato **template.json** e aggiungere il contenuto seguente dal modello esportato al file.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

Il modello template.json è ottimale se si vuole creare lo stesso tipo di account di archiviazione nella stessa area con una rete virtuale che usa lo stesso prefisso di indirizzo e lo stesso prefisso di subnet per ogni distribuzione. Le opzioni disponibili in Resource Manager consentono, tuttavia, di distribuire modelli con maggiore flessibilità. Ad esempio, durante la distribuzione, si potrebbe voler specificare il tipo di account di archiviazione da creare o i valori da usare per il prefisso di indirizzo della rete virtuale e per il prefisso di subnet.

## Personalizzare il modello

In questa sezione verranno aggiunti parametri al modello esportato, per poterlo usare di nuovo durante la distribuzione di queste risorse in altri ambienti. Verranno aggiunte anche alcune funzionalità al modello per ridurre la probabilità che si verifichi un errore durante la distribuzione del modello. Non sarà più necessario cercare di specificare un nome univoco per l'account di archiviazione. Il nome univoco verrà invece creato dal modello. Sarà possibile limitare i valori che possono essere specificati per il tipo di account di archiviazione, consentendo solo le opzioni valide.

1. Per poter passare i valori da specificare durante la distribuzione, sostituire la sezione **parameters** con le definizioni seguenti dei parametri. Si notino i valori di **allowedValues** per **storageAccount\_accountType**. Se si specifica accidentalmente un valore non corretto, l'errore viene riconosciuto prima dell'avvio della distribuzione. Si noti anche che si sta specificando solo un prefisso per il nome dell'account di archiviazione e che il prefisso è limitato a 11 caratteri. La limitazione del prefisso a 11 caratteri fa in modo che il nome completo non superi il numero massimo di caratteri per un account di archiviazione. Il prefisso consente di applicare una convenzione di denominazione agli account di archiviazione. Nel passaggio successivo verrà illustrato come creare un nome univoco.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. La sezione **variables** del modello è attualmente vuota. Sostituire questa sezione con il codice seguente. La sezione **variables** consente all'autore del modello di creare valori che semplificano la sintassi per il resto del modello. La variabile **storageAccount\_name** consente di concatenare il prefisso del parametro a una stringa univoca generata in base all'identificatore del gruppo di risorse.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Per usare i parametri e la variabile nelle definizioni delle risorse, sostituire la sezione **resources** con le definizioni seguenti. Si noti che sono state apportate effettivamente poche modifiche alle definizioni delle risorse. Sono stati essenzialmente modificati i valori assegnati alle proprietà delle risorse. Le proprietà sono esattamente uguali alle proprietà del modello esportato. Si stanno semplicemente assegnando proprietà a valori di parametro, anziché a valori hardcoded. La posizione delle risorse viene impostata in modo da usare la stessa località del gruppo di risorse con l'espressione **resourceGroup().location**. L'espressione **variables** consente di fare riferimento alla variabile creata per il nome dell'account di archiviazione.

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## Correggere il file dei parametri

Il file dei parametri scaricato non corrisponde più ai parametri nel modello. L'uso di un file dei parametri non è necessario, ma può semplificare il processo di ridistribuzione di un ambiente. Per molti parametri verranno usati i valori predefiniti indicati nel modello, quindi il file dei parametri necessita solo di due valori.

Sostituire i contenuti del file parameters.json con:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

Il file dei parametri aggiornato fornisce solo valori per i parametri privi di un valore predefinito. È possibile specificare valori per gli altri parametri, se si vuole usare un valore diverso dal valore predefinito.

## Distribuire il modello

Per distribuire il modello personalizzato e i file dei parametri è possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure. Se necessario, installare [Azure PowerShell](powershell-install-configure.md) o l'[interfaccia della riga di comando di Azure](xplat-cli-install.md). Per la distribuzione del modello è possibile usare gli script scaricati con il modello durante l'esportazione del modello originale oppure è possibile scrivere script personalizzati. Entrambe le opzioni sono illustrate in questo articolo.

2. Per eseguire la distribuzione con uno script personalizzato, usare uno degli approcci seguenti.

     Per PowerShell eseguire:

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     Per l'interfaccia della riga di comando di Azure eseguire:

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. Se sono stati scaricati il modello esportato e gli script, trovare il file **deploy.ps1**, per PowerShell, o **deploy.sh**, per l'interfaccia della riga di comando di Azure.

     Per PowerShell eseguire:

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     Per l'interfaccia della riga di comando di Azure eseguire:

        .\deploy.sh

## Passaggi successivi

- La [Procedura dettagliata per un modello di Resource Manager](resource-manager-template-walkthrough.md) si basa sui concetti appresi in questo articolo e permette di creare un modello per una soluzione più complessa. Consente una migliore comprensione delle risorse disponibili e illustra come determinare i valori da fornire.
- Per informazioni su come esportare un modello tramite PowerShell, vedere [Uso di Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
- Per informazioni su come esportare un modello tramite l'interfaccia della riga di comando di Azure, vedere [Usare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).
- Per altre informazioni sulla struttura dei modelli, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0518_2016-->