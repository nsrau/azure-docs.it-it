---
title: Distribuire le risorse con il modello e l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Usare Azure Resource Manager e l'interfaccia della riga di comando di Azure per distribuire le risorse in Azure. Le risorse sono definite in un modello di Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: bd43e919cc0b2bcf1d130c7e616b7da064abcc65
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971021"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure con modelli di Resource Manager per distribuire risorse in Azure. Per comprendere i concetti di distribuzione e gestione delle soluzioni di Azure, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Se l'interfaccia della riga di comando di Azure non è installata, è possibile usare [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Ambito di distribuzione

La distribuzione può essere destinata a una sottoscrizione di Azure o a un gruppo di risorse all'interno di una sottoscrizione. Nella maggior parte dei casi, la distribuzione verrà destinata a un gruppo di risorse. Usare le distribuzioni di sottoscrizione per applicare i criteri e le assegnazioni di ruolo nella sottoscrizione. È anche possibile usare le distribuzioni delle sottoscrizioni per creare un gruppo di risorse e distribuirvi risorse. A seconda dell'ambito della distribuzione, si utilizzano comandi diversi.

Per eseguire la distribuzione in un **gruppo di risorse**, usare [AZ Group Deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Per eseguire la distribuzione in una **sottoscrizione**, usare [AZ Deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Attualmente, le distribuzioni di gruppi di gestione sono supportate solo tramite l'API REST. Vedere [distribuire le risorse con gestione risorse modelli e gestione risorse API REST](resource-group-template-deploy-rest.md).

Gli esempi in questo articolo usano le distribuzioni di gruppi di risorse. Per altre informazioni sulle distribuzioni delle sottoscrizioni, vedere [creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Distribuire un modello locale

Per distribuire le risorse in Azure, seguire questa procedura:

1. Accedere con l'account Azure
2. Creare un gruppo di risorse che funge da contenitore per le risorse distribuite. Il nome del gruppo di risorse può contenere solo caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi. Può contenere fino a 90 caratteri. Non può terminare con un punto.
3. Distribuire nel gruppo di risorse il modello che definisce le risorse da creare.

Un modello può includere parametri che consentono di personalizzare la distribuzione. Può includere ad esempio valori specifici per un determinato ambiente (di sviluppo, test e produzione). Il modello di esempio definisce un parametro per lo SKU dell'account di archiviazione. 

L'esempio seguente crea un gruppo di risorse e distribuisce un modello dal computer locale:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio che include il risultato:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuisci modello remoto

Anziché archiviare i modelli di Resource Manager nel computer locale, è consigliabile archiviarli in una posizione esterna, ad esempio in un repository di controllo del codice sorgente come GitHub. È possibile, in alternativa, archiviarli in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione.

Per distribuire un modello esterno, usare il parametro **template-uri**. Usare l'URI indicato nell'esempio per distribuire il modello di esempio da GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

L'esempio precedente richiede l'utilizzo di un URI accessibile pubblicamente per il modello, che funziona per la maggior parte degli scenari. Il proprio modello non deve infatti includere dati sensibili. Se è necessario specificare dati riservati, ad esempio una password di amministratore, passare il valore come parametro protetto. Se invece si preferisce che il modello usato non sia accessibile pubblicamente, è possibile proteggerlo archiviandolo in un contenitore di archiviazione privato. Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso (SAS), vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Immettere i comandi seguenti in Cloud Shell:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>Eseguire nuovamente la distribuzione se non è riuscita

Questa funzionalità è nota anche come *rollback in errore*. Quando una distribuzione non riesce, è possibile ridistribuire automaticamente una distribuzione precedente con esito positivo dalla cronologia della distribuzione. Per specificare la ridistribuzione, usare il parametro `--rollback-on-error` nel comando di distribuzione. Questa funzionalità è utile se si ha uno stato valido noto per la distribuzione dell'infrastruttura e si vuole ripristinare questo stato. Esistono alcune avvertenze e restrizioni:

- La ridistribuzione viene eseguita esattamente come è stata eseguita in precedenza con gli stessi parametri. Non è possibile modificare i parametri.
- La distribuzione precedente viene eseguita utilizzando la [modalità completa](./deployment-modes.md#complete-mode). Tutte le risorse non incluse nella distribuzione precedente verranno eliminate e le configurazioni delle risorse verranno impostate sullo stato precedente. Assicurarsi di comprendere completamente le [modalità di distribuzione](./deployment-modes.md).
- La ridistribuzione influisce solo sulle risorse. le modifiche apportate ai dati non sono interessate.
- Questa funzionalità è supportata solo per le distribuzioni di gruppi di risorse e non per le distribuzioni a livello di sottoscrizione. Per altre informazioni sulla distribuzione a livello di sottoscrizione, vedere [creare gruppi di risorse e risorse a livello di sottoscrizione](./deploy-to-subscription.md).

Per usare questa opzione, le distribuzioni devono avere nomi univoci in modo che sia possibile identificarle nella cronologia. Se non si dispone di nomi univoci, la distribuzione non riuscita corrente potrebbe sovrascrivere quella eseguita in modo corretto nella cronologia. È possibile usare questa opzione solo con le distribuzioni a livello di radice. Le distribuzioni di un modello annidato non sono disponibili per la ridistribuzione.

Per eseguire nuovamente l'ultima distribuzione con esito positivo, aggiungere il parametro `--rollback-on-error` come contrassegno.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Per eseguire nuovamente una distribuzione specifica, usare il parametro `--rollback-on-error` e specificare il nome della distribuzione.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

La distribuzione specificata deve aver avuto esito positivo.

## <a name="parameters"></a>Parametri

Per passare i valori dei parametri, è possibile usare i parametri inline o un file di parametri.

### <a name="inline-parameters"></a>Parametri inline

Per passare i parametri inline, specificare i valori in `parameters`. Ad esempio, per passare una stringa e una matrice a un modello in una shell Bash, usare:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se si usa l'interfaccia della riga di comando di Azure con il prompt dei comandi di Windows (CMD) o PowerShell `exampleArray="['value1','value2']"`, passare la matrice nel formato:.

È anche possibile ottenere i contenuti del file e fornire il contenuto come un parametro inline.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Ottenere un valore di parametro da un file è utile quando è necessario fornire i valori di configurazione. Ad esempio, è possibile fornire i valori [cloud-init per una macchina virtuale Linux](../virtual-machines/linux/using-cloud-init.md).

Il formato arrayContent.json è:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>File dei parametri

Invece di passare i parametri come valori inline nello script, può risultare più facile usare un file JSON che contenga i valori dei parametri. Il file dei parametri deve essere un file locale. I file dei parametri esterni non sono supportati con l'interfaccia della riga di comando di Azure.

Per altre informazioni sul file dei parametri, vedere [creare Gestione risorse file di parametri](resource-manager-parameter-files.md).

Per passare un file dei parametri locale, usare `@` per specificare un file locale denominato storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Testare una distribuzione del modello

Per testare il modello e i valori dei parametri senza distribuire effettivamente le risorse, usare il comando [az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Se non vengono rilevati errori, il comando restituisce informazioni sulla distribuzione di test. Si noti nello specifico che il valore dell'**errore** è null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Se viene rilevato un errore, il comando restituisce un messaggio di errore. Ad esempio, passare un valore non corretto per lo SKU dell'account di archiviazione restituisce l'errore seguente:

```azurecli
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

```azurecli
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

- Gli esempi inclusi in questo articolo distribuiscono risorse a un gruppo di risorse nella sottoscrizione predefinita. Per usare una sottoscrizione diversa, vedere [Gestire più sottoscrizioni di Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
- Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-cli-sas-token.md).
- Per implementare in modo sicuro il servizio in più di un'area, vedere [Azure Deployment Manager](deployment-manager-overview.md).
