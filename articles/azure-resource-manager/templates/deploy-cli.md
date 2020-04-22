---
title: Distribuire risorse con l'interfaccia della riga di comando di Azure e il modelloDeploy resources with Azure CLI and template
description: Usare Azure Resource Manager e l'interfaccia della riga di comando di Azure per distribuire le risorse in Azure.Use Azure Resource Manager and Azure CLI to deploy resources to Azure. Le risorse sono definite in un modello di Resource Manager.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685694"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Distribuire risorse con modelli ARM e l'interfaccia della riga di comando di AzureDeploy resources with ARM templates and Azure CLI

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure con i modelli di Azure Resource Manager (ARM) per distribuire le risorse in Azure.This article explains how to use Azure Resource Manager (ARM) templates to deploy your resources to Azure. Se non si ha familiarità con i concetti di distribuzione e gestione delle soluzioni di Azure, vedere Panoramica della distribuzione dei [modelli.](overview.md)

I comandi di distribuzione sono stati modificati nell'interfaccia della riga di comando di Azure versione 2.2.0.The deployment commands changed in Azure CLI version 2.2.0. Gli esempi in questo articolo richiedono l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se l'interfaccia della riga di comando di Azure non è installata, è possibile usare [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Ambito di distribuzione

È possibile assegnare la distribuzione a un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. Nella maggior parte dei casi, la distribuzione verrà destinata a un gruppo di risorse. Per applicare criteri e assegnazioni di ruolo in un ambito più ampio, usare distribuzioni di sottoscrizioni, gruppi di gestione o tenant. Quando si esegue la distribuzione in una sottoscrizione, è possibile crearvi un gruppo di risorse e distribuirvi le risorse.

A seconda dell'ambito della distribuzione, si utilizzano comandi diversi.

Per eseguire la distribuzione in un gruppo di **risorse,** usare [az deployment group create](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create):

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Per eseguire la distribuzione in una **sottoscrizione,** usare [az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create):

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Per altre informazioni sulle distribuzioni a livello di sottoscrizione, vedere Creare gruppi di risorse e risorse a livello di [sottoscrizione.](deploy-to-subscription.md)

Per eseguire la distribuzione in un gruppo di **gestione,** usare [az deployment mg create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Per ulteriori informazioni sulle distribuzioni a livello di gruppo di gestione, vedere Creare risorse a livello di gruppo di [gestione.](deploy-to-management-group.md)

Per eseguire la distribuzione in un **tenant,** usare [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Per ulteriori informazioni sulle distribuzioni a livello di tenant, vedere Creare risorse a livello di [tenant.](deploy-to-tenant.md)

Gli esempi in questo articolo usano distribuzioni di gruppi di risorse.

## <a name="deploy-local-template"></a>Distribuire un modello locale

Per distribuire le risorse in Azure, seguire questa procedura:

1. Accedere con l'account Azure
2. Creare un gruppo di risorse che funge da contenitore per le risorse distribuite. Il nome del gruppo di risorse può contenere solo caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi. Può contenere fino a 90 caratteri. Non può terminare con un punto.
3. Distribuire nel gruppo di risorse il modello che definisce le risorse da creare.

Un modello può includere parametri che consentono di personalizzare la distribuzione. Può includere ad esempio valori specifici per un determinato ambiente (di sviluppo, test e produzione). Il modello di esempio definisce un parametro per lo SKU dell'account di archiviazione.

L'esempio seguente crea un gruppo di risorse e distribuisce un modello dal computer locale:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio che include il risultato:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuire il modello remotoDeploy remote template

Anziché archiviare i modelli ARM nel computer locale, è preferibile archiviarli in una posizione esterna. ad esempio in un repository di controllo del codice sorgente come GitHub. È possibile, in alternativa, archiviarli in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione.

Per distribuire un modello esterno, usare il parametro **template-uri**. Usare l'URI indicato nell'esempio per distribuire il modello di esempio da GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

L'esempio precedente richiede l'utilizzo di un URI accessibile pubblicamente per il modello, che funziona per la maggior parte degli scenari. Il proprio modello non deve infatti includere dati sensibili. Se è necessario specificare dati riservati, ad esempio una password di amministratore, passare il valore come parametro protetto. Se invece si preferisce che il modello usato non sia accessibile pubblicamente, è possibile proteggerlo archiviandolo in un contenitore di archiviazione privato. Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso (SAS), vedere [Distribuire un modello privato con un token di firma di accesso condiviso](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

Immettere i comandi seguenti in Cloud Shell:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametri

Per passare i valori dei parametri, è possibile usare i parametri inline o un file di parametri.

### <a name="inline-parameters"></a>Parametri inline

Per passare i parametri inline, specificare i valori in `parameters`. Ad esempio, per passare una stringa e una matrice a un modello in una shell Bash, usare:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se si usa l'interfaccia della riga di comando di Azure con il `exampleArray="['value1','value2']"`prompt dei comandi di Windows (CMD) o PowerShell, passare la matrice nel formato: .

È anche possibile ottenere i contenuti del file e fornire il contenuto come un parametro inline.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Ottenere un valore di parametro da un file è utile quando è necessario fornire i valori di configurazione. Ad esempio, è possibile fornire i valori [cloud-init per una macchina virtuale Linux](../../virtual-machines/linux/using-cloud-init.md).

Il formato arrayContent.json è:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>File dei parametri

Invece di passare i parametri come valori inline nello script, può risultare più facile usare un file JSON che contenga i valori dei parametri. Il file dei parametri deve essere un file locale. I file dei parametri esterni non sono supportati con l'interfaccia della riga di comando di Azure.

Per ulteriori informazioni sul file dei parametri, vedere Creare un file di parametri di [Resource Manager](parameter-files.md).

Per passare un file dei parametri locale, usare `@` per specificare un file locale denominato storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Gestire il formato JSON esteso

Per distribuire un modello con stringhe o commenti su più righe usando l'interfaccia della `--handle-extended-json-format` riga della riga di comando di Azure con la versione 2.3.0 o precedente, è necessario usare l'opzione.  Ad esempio:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Testare una distribuzione del modello

Per testare i valori del modello e dei parametri senza distribuire effettivamente le risorse, utilizzare [az deployment group validate](/cli/azure/group/deployment).

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Se non vengono rilevati errori, il comando restituisce informazioni sulla distribuzione di test. Si noti nello specifico che il valore dell'**errore** è null.

```output
{
  "error": null,
  "properties": {
      ...
```

Se viene rilevato un errore, il comando restituisce un messaggio di errore. Ad esempio, passare un valore non corretto per lo SKU dell'account di archiviazione restituisce l'errore seguente:

```output
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Se il modello contiene un errore di sintassi, il comando restituisce un errore che indica l'impossibilità di analizzare il modello. Il messaggio contiene il numero di riga e la posizione dell'errore di analisi.

```output
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per eseguire il rollback a una distribuzione corretta quando viene visualizzato un errore, vedere Rollback in caso di [errore alla corretta distribuzione](rollback-on-error.md).
- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per informazioni su come definire i parametri nel modello, vedere Informazioni sulla struttura e la [sintassi dei modelli ARM](template-syntax.md).
- Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).
- Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](secure-template-with-sas-token.md).
- Per implementare in modo sicuro il servizio in più di un'area, vedere [Azure Deployment Manager](deployment-manager-overview.md).
