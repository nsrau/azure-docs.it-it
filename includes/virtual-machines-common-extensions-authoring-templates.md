## <a name="overview-of-azure-resource-manager-templates"></a>Panoramica dei modelli di Gestione risorse di Azure
I modelli di Azure Resource Manager consentono di specificare in modo dichiarativo l'infrastruttura IaaS di Azure in linguaggio JSON, definendo le dipendenze tra risorse. Per una panoramica dettagliata dei modelli di Azure Resource Manager, vedere gli articoli seguenti:

[Panoramica del gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md)

## <a name="sample-template-snippet-for-vm-extensions"></a>Frammento di modello di esempio per le estensioni della macchina virtuale.
Per la distribuzione di estensioni della macchina virtuale come parte del modello di Azure Resource Manager è necessario specificare in modo dichiarativo la configurazione dell'estensione nel modello.
Di seguito è riportato il formato per specificare la configurazione dell'estensione.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Come si può notare da quanto sopra, il modello dell'estensione contiene due parti principali:

1. Nome dell'estensione, editore e versione.
2. Configurazione dell'estensione.

## <a name="identifying-the-publisher-type-and-typehandlerversion-for-any-extension"></a>Identificazione dell'editore, del tipo e del valore typeHandlerVersion per le estensioni.
Le estensioni della macchina virtuale di Azure vengono pubblicate da Microsoft ed editori di terze parti attendibili e ogni estensione è identificata in modo univoco dall'editore, dal tipo e dal valore typeHandlerVersion. Questi possono essere determinati come segue:  

