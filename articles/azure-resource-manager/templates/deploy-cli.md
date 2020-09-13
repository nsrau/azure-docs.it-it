---
title: Distribuire le risorse con l'interfaccia della riga di comando di Azure
description: Usare Azure Resource Manager e l'interfaccia della riga di comando di Azure per distribuire le risorse in Azure. Le risorse sono definite in un modello di Resource Manager.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 7e8ae7e8c568f5f0ebb85f434e33f142b5fe94e8
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566161"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure con modelli di Azure Resource Manager (modelli ARM) per distribuire le risorse in Azure. Se non si ha familiarità con i concetti relativi alla distribuzione e alla gestione delle soluzioni di Azure, vedere [Panoramica della distribuzione dei modelli](overview.md).

I comandi di distribuzione modificati nell'interfaccia della riga di comando di Azure versione 2.2.0. Gli esempi in questo articolo richiedono l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se l'interfaccia della riga di comando di Azure non è installata, è possibile usare [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Ambito della distribuzione

La distribuzione può essere destinata a un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. Nella maggior parte dei casi, la distribuzione verrà destinata a un gruppo di risorse. Per applicare criteri e assegnazioni di ruolo in un ambito più ampio, usare la sottoscrizione, il gruppo di gestione o le distribuzioni tenant. Quando si esegue la distribuzione in una sottoscrizione, è possibile creare un gruppo di risorse e distribuirvi risorse.

A seconda dell'ambito della distribuzione, vengono usati comandi diversi.

* Per eseguire la distribuzione in un **gruppo di risorse**, usare [AZ Deployment Group create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Per eseguire la distribuzione in una **sottoscrizione**, usare [AZ Deployment Sub create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Per altre informazioni sulle distribuzioni a livello di sottoscrizione, vedere [Creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md).

* Per eseguire la distribuzione in un **gruppo di gestione**, usare [AZ Deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Per altre informazioni sulle distribuzioni a livello di gruppo di gestione, vedere [Creare risorse a livello di gruppo di gestione](deploy-to-management-group.md).

* Per eseguire la distribuzione in un **tenant**, usare [AZ Deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Per altre informazioni sulle distribuzioni a livello di tenant, vedere [Creare risorse a livello di tenant](deploy-to-tenant.md).

Gli esempi contenuti in questo articolo usano distribuzioni di gruppi di risorse.

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

## <a name="deployment-name"></a>Nome distribuzione

Nell'esempio precedente, la distribuzione è stata denominata `ExampleDeployment` . Se non si specifica un nome per la distribuzione, viene usato il nome del file modello. Se, ad esempio, si distribuisce un modello denominato `azuredeploy.json` e non si specifica un nome di distribuzione, la distribuzione verrà denominata `azuredeploy` .

Ogni volta che si esegue una distribuzione, viene aggiunta una voce alla cronologia di distribuzione del gruppo di risorse con il nome della distribuzione. Se si esegue un'altra distribuzione e si assegna lo stesso nome, la voce precedente viene sostituita con la distribuzione corrente. Se si desidera mantenere voci univoche nella cronologia di distribuzione, assegnare a ogni distribuzione un nome univoco.

Per creare un nome univoco, è possibile assegnare un numero casuale.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

In alternativa, aggiungere un valore di data.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Se si eseguono distribuzioni simultanee nello stesso gruppo di risorse con lo stesso nome di distribuzione, solo l'ultima distribuzione viene completata. Tutte le distribuzioni con lo stesso nome che non sono state completate vengono sostituite dall'ultima distribuzione. Ad esempio, se si esegue una distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage1` e allo stesso tempo esegue un'altra distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage2` , si distribuisce un solo account di archiviazione. L'account di archiviazione risultante è denominato `storage2` .

Tuttavia, se si esegue una distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage1` e immediatamente dopo aver completato l'esecuzione di un'altra distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage2` , si avranno due account di archiviazione. Uno è denominato `storage1` e l'altro è denominato `storage2` . Tuttavia, è presente una sola voce nella cronologia di distribuzione.

Quando si specifica un nome univoco per ogni distribuzione, è possibile eseguirli simultaneamente senza conflitti. Se si esegue una distribuzione denominata `newStorage1` che distribuisce un account di archiviazione denominato `storage1` e allo stesso tempo esegue un'altra distribuzione denominata `newStorage2` che distribuisce un account di archiviazione denominato `storage2` , si avranno due account di archiviazione e due voci nella cronologia di distribuzione.

Per evitare conflitti con le distribuzioni simultanee e per garantire voci univoche nella cronologia di distribuzione, assegnare a ogni distribuzione un nome univoco.

## <a name="deploy-remote-template"></a>Distribuisci modello remoto

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

## <a name="deploy-template-spec"></a>Distribuire la specifica di modello

Anziché distribuire un modello locale o remoto, è possibile creare una [specifica del modello](template-specs.md). La specifica del modello è una risorsa nella sottoscrizione di Azure che contiene un modello ARM. Consente di condividere in modo sicuro il modello con gli utenti dell'organizzazione. Usare il controllo degli accessi in base al ruolo (RBAC) per concedere l'accesso alla specifica del modello. Questa funzionalità è attualmente disponibile in anteprima.

Gli esempi seguenti illustrano come creare e distribuire una specifica del modello. Questi comandi sono disponibili solo se è stata effettuata [l'iscrizione per l'anteprima](https://aka.ms/templateSpecOnboarding).

Per prima cosa, è necessario creare la specifica del modello fornendo il modello ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Ottenere quindi l'ID per la specifica del modello e distribuirlo.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Per altre informazioni, vedere [Azure Resource Manager specifiche del modello (anteprima)](template-specs.md).

## <a name="preview-changes"></a>Anteprima modifiche

Prima di distribuire il modello, è possibile visualizzare in anteprima le modifiche che il modello apporterà all'ambiente. Usare l' [operazione](template-deploy-what-if.md) di simulazione per verificare che il modello apporti le modifiche previste. Cosa-se anche il modello viene convalidato per gli errori.

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

Se si usa l'interfaccia della riga di comando di Azure con il prompt dei comandi di Windows (CMD) o PowerShell, passare la matrice nel formato: `exampleArray="['value1','value2']"` .

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

Per passare un oggetto, ad esempio per impostare i tag, usare JSON. Ad esempio, il modello potrebbe includere un parametro come il seguente:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

In questo caso, è possibile passare una stringa JSON per impostare il parametro come illustrato nello script bash seguente:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Usare le virgolette doppie intorno al file JSON che si vuole passare all'oggetto.

### <a name="parameter-files"></a>File dei parametri

Invece di passare i parametri come valori inline nello script, può risultare più facile usare un file JSON che contenga i valori dei parametri. Il file dei parametri deve essere un file locale. I file dei parametri esterni non sono supportati con l'interfaccia della riga di comando di Azure.

Per altre informazioni sul file dei parametri, vedere [Creare il file di parametri di Resource Manager](parameter-files.md).

Per passare un file dei parametri locale, usare `@` per specificare un file locale denominato storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Gestire il formato JSON esteso

Per distribuire un modello con stringhe a più righe o commenti usando l'interfaccia della riga di comando di Azure con la versione 2.3.0 o precedente, è necessario usare l' `--handle-extended-json-format` opzione.  Ad esempio:

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

## <a name="next-steps"></a>Passaggi successivi

- Per eseguire il rollback a una distribuzione corretta quando viene restituito un errore, vedere [Rollback alla distribuzione corretta in caso di errore](rollback-on-error.md).
- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per informazioni su come definire i parametri nel modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
- Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).
- Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](secure-template-with-sas-token.md).
