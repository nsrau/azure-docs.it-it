# [Documentazione di Azure Resource Manager](index.md)

# Panoramica
## [Informazioni su Resource Manager](resource-group-overview.md)
## [Provider e tipi di risorse](resource-manager-supported-services.md)
## [Distribuzione Resource Manager e classica](resource-manager-deployment-model.md)
## [Governance per le sottoscrizioni](resource-manager-subscription-governance.md)
## [Organizzare le risorse con i gruppi di gestione](management-groups-overview.md)

# Attività iniziali
## [Creare e distribuire il modello](resource-manager-create-first-template.md)
## [Estensione di Visual Studio Code per i modelli](resource-manager-vscode-extension.md)
## [Visual Studio con Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Procedure
## Creare modelli
### [Sezioni di modelli](resource-group-authoring-templates.md)
#### [Parameters](resource-manager-templates-parameters.md)
#### [Variabili](resource-manager-templates-variables.md)
#### [Risorse](resource-manager-templates-resources.md)
#### [Outputs](resource-manager-templates-outputs.md)
### [Modelli collegati e annidati](resource-group-linked-templates.md)
### [Definire la dipendenza tra risorse](resource-group-define-dependencies.md)
### [Creare più istanze](resource-group-create-multiple.md)
### [Aggiornare una risorsa](/azure/architecture/building-blocks/extending-templates/update-resource)

## Distribuire
### Azure PowerShell
#### [Distribuire un modello](resource-group-template-deploy.md)
#### [Distribuire un modello privato con token SAS](resource-manager-powershell-sas-token.md)
#### [Esportare il modello e ridistribuirlo](resource-manager-export-template-powershell.md)
### Interfaccia della riga di comando di Azure
#### [Distribuire un modello](resource-group-template-deploy-cli.md)
#### [Distribuire un modello privato con token SAS](resource-manager-cli-sas-token.md)
#### [Esportare il modello e ridistribuirlo](resource-manager-export-template-cli.md)
### Portale di Azure
#### [Distribuire le risorse](resource-group-template-deploy-portal.md)
#### [Esportare il modello](resource-manager-export-template.md)
### [API REST](resource-group-template-deploy-rest.md)
### [Più gruppi di risorse o sottoscrizioni](resource-manager-cross-resource-group-deployment.md)
### [Integrazione continua con Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md)

## Gestisci
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)
### [Azure portal](resource-group-portal.md)
### [Usare tag per organizzare le risorse](resource-group-using-tags.md)
### [Spostare le risorse in un nuovo gruppo o sottoscrizione](resource-group-move-resources.md)
### [Creare gruppi di gestione](management-groups-create.md)
### [Gestire i gruppi di gestione](management-groups-manage.md)
### [Esempi di governance](resource-manager-subscription-examples.md)

## Controllare l'accesso
### Creare un'entità servizio
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure portal](resource-group-create-service-principal-portal.md)
### [API di autenticazione per accedere alle sottoscrizioni](resource-manager-api-authentication.md)
### [Bloccare le risorse](resource-group-lock-resources.md)

## Audit
### [Visualizzare log di attività](resource-group-audit.md)
### [Visualizzare le operazioni di distribuzione](resource-manager-deployment-operations.md)

## Risolvere problemi
### [Errori di distribuzione comuni](resource-manager-common-deployment-errors.md)
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [Problemi di distribuzione di Linux](../virtual-machines/linux/troubleshoot-deploy-vm.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [Problemi di provisioning e allocazione per Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
#### [Problemi di provisioning e allocazione per Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ReservedResourceName](resource-manager-reserved-resource-name.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
#### [Problemi di distribuzione di Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)
### [Informazioni sugli errori di distribuzione](resource-manager-troubleshoot-tips.md)

# Riferimenti
## [Formato del modello](/azure/templates/)
## [Funzioni di modello](resource-group-template-functions.md)
### [Matrici e funzioni oggetto](resource-group-template-functions-array.md)
### [Funzioni di confronto](resource-group-template-functions-comparison.md)
### [Funzioni di distribuzione](resource-group-template-functions-deployment.md)
### [Funzioni logiche](resource-group-template-functions-logical.md)
### [Funzioni numeriche](resource-group-template-functions-numeric.md)
### [Funzioni delle risorse](resource-group-template-functions-resource.md)
### [Funzioni stringa](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Interfaccia della riga di comando di Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Limitazione delle richieste](resource-manager-request-limits.md)
## [Tenere traccia delle operazioni asincrone](resource-manager-async-operations.md)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
