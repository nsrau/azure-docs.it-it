---
title: Creare una funzione Python dalla riga di comando - Funzioni di Azure
description: Informazioni su come creare una funzione Python dalla riga di comando e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 5f8bf6fb6f464bcb74d34e2d99fac16c24b12615
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175713"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Avvio rapido: Creare una funzione Python in Azure dalla riga di comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In questo articolo vengono usati gli strumenti da riga di comando per creare una funzione Python che risponde alle richieste HTTP. Dopo aver testato il codice in locale, verrà distribuito nell'ambiente serverless di Funzioni di Azure.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

È inoltre disponibile una [versione basata su Visual Studio Code](create-first-function-vs-code-python.md) di questo articolo.

## <a name="configure-your-local-environment"></a>Configurare l'ambiente locale

Per eseguire le procedure descritte è necessario:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) versione 3.x.
  
+ Uno degli strumenti seguenti per la creazione di risorse di Azure:

    + [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.4 o successiva.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versione 5.0 o successiva.

+ [Python 3.8 (64 bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bit)](https://www.python.org/downloads/release/python-368/), supportati tutti dalla versione 3.x di Funzioni di Azure.

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

Verificare i prerequisiti, che variano a seconda che si usi l'interfaccia della riga di comando di Azure o Azure PowerShell per la creazione di risorse di Azure:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

+ In una finestra di comando o di terminale eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 3.x.

+ Eseguire `az --version` per verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.4 o successiva.

+ Eseguire `az login` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.

+ Eseguire `python --version` (Linux/macOS) o `py --version` (Windows) per verificare che la versione di Python sia 3.8.x, 3.7.x o 3.6.x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ In una finestra di comando o di terminale eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 3.x.

+ Eseguire `(Get-Module -ListAvailable Az).Version` per verificare che la versione sia 5.0 o successiva. 

+ Eseguire `Connect-AzAccount` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.

+ Eseguire `python --version` (Linux/macOS) o `py --version` (Windows) per verificare che la versione di Python sia 3.8.x, 3.7.x o 3.6.x.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Creare e attivare un ambiente virtuale

In una cartella appropriata eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`. Assicurarsi di usare Python 3.8, 3.7 o 3.6, che sono supportati da Funzioni di Azure.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se Python non ha installato il pacchetto venv nella distribuzione Linux, eseguire il comando seguente:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

È possibile eseguire tutti i comandi successivi in questo ambiente virtuale attivato. 

## <a name="create-a-local-function-project"></a>Creare un progetto di funzione locale

In Funzioni di Azure un progetto di funzione è un contenitore per una o più funzioni singole che rispondono ognuna a un trigger specifico. Tutte le funzioni di un progetto condividono le stesse configurazioni locali e di hosting. In questa sezione viene creato un progetto di funzione che contiene una singola funzione.

1. Eseguire il comando `func init` come indicato di seguito per creare un progetto di Funzioni in una cartella denominata *LocalFunctionProj* con il runtime specificato:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Passare alla cartella del progetto:

    ```console
    cd LocalFunctionProj
    ```
    
    Questa cartella contiene vari file per il progetto, inclusi i file di configurazione denominati [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Poiché *local.settings.json* può contenere segreti scaricati da Azure, per impostazione predefinita il file viene escluso dal controllo del codice sorgente nel file con estensione *gitignore*.

1. Aggiungere una funzione al progetto usando il comando seguente, in cui l'argomento `--name` è il nome univoco della funzione (HttpExample) e l'argomento `--template` specifica il trigger della funzione (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` crea una sottocartella corrispondente al nome della funzione che contiene un file di codice appropriato per il linguaggio scelto del progetto e un file di configurazione denominato *function.json*.

### <a name="optional-examine-the-file-contents"></a>(Facoltativo) Esaminare il contenuto del file

Se si vuole, è possibile passare a [Eseguire la funzione localmente](#run-the-function-locally) ed esaminare il contenuto del file in un secondo momento.

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* contiene una funzione Python `main()` attivata in base alla configurazione presente in *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Per un trigger HTTP, la funzione riceve i dati della richiesta nella variabile `req` come definito in *function.json*. `req` è un'istanza della [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). L'oggetto restituito, definito come `$return` in *function.json*, è un'istanza della [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Per altre informazioni, vedere [Trigger e associazioni HTTP di Funzioni di Azure](./functions-bindings-http-webhook.md?tabs=python).

#### <a name="functionjson"></a>function.json

*function.json* è un file di configurazione che definisce gli oggetti `bindings` di input e output per la funzione, incluso il tipo di trigger.

Se si vuole, è possibile modificare `scriptFile` per richiamare un file Python diverso.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Ogni binding richiede una direzione, un tipo e un nome univoco. Il trigger HTTP dispone di un'associazione di input di tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e di un'associazione di output di tipo [`http`](functions-bindings-http-webhook-output.md).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Creare le risorse di Azure di supporto per la funzione

Prima di distribuire il codice della funzione in Azure, è necessario creare tre risorse:

- Un gruppo di risorse, ovvero un contenitore logico di risorse correlate.
- Un account di archiviazione, che mantiene lo stato e altre informazioni sui progetti.
- Un'app per le funzioni, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni si collega al progetto di funzione locale e consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione di risorse.

Usare i comandi seguenti per creare questi elementi. Sono supportati sia l'interfaccia della riga di comando di Azure che PowerShell.

1. Se non è già stato fatto, accedere ad Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Il comando [az login](/cli/azure/reference-index#az-login) consente di accedere all'account Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) consente di accedere all'account Azure.

    ---

1. Creare un gruppo di risorse denominato `AzureFunctionsQuickstart-rg` nell'area `westeurope`. 

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Il comando [az group create](/cli/azure/group#az-group-create) crea un gruppo di risorse. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile restituita dal comando `az account list-locations`.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) crea un gruppo di risorse. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile restituita dal cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

    > [!NOTE]
    > Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `AzureFunctionsQuickstart-rg` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.

1. Creare un account di archiviazione per utilizzo generico nel gruppo di risorse e nell'area:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Il comando [az storage account create](/cli/azure/storage/account#az-storage-account-create) crea l'account di archiviazione. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Il cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) crea l'account di archiviazione.

    ---

    Nell'esempio precedente sostituire `<STORAGE_NAME>` con un nome appropriato e univoco in Archiviazione di Azure. I nomi devono contenere da tre a 24 caratteri costituiti esclusivamente da numeri e lettere in minuscolo. `Standard_LRS` specifica un account per utilizzo generico, che è [supportato da Funzioni](storage-considerations.md#storage-account-requirements).
    
    Per questo argomento di avvio rapido, il costo dell'account di archiviazione è solo di pochi centesimi di dollaro USA.

1. Creare l'app per le funzioni in Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Il comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crea l'app per le funzioni in Azure. Se si usa Python 3.7 o 3.6, sostituire `--runtime-version` rispettivamente con `3.7` o `3.6`.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Il cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) crea l'app per le funzioni in Azure. Se si usa Python 3.7 o 3.6, sostituire `-RuntimeVersion` rispettivamente con `3.7` o `3.6`.

    ---
    
    Nell'esempio precedente, sostituire `<STORAGE_NAME>` con il nome dell'account usato nel passaggio precedente e sostituire `<APP_NAME>` con un nome univoco a livello globale appropriato.  `<APP_NAME>` è anche il dominio DNS predefinito per l'app per le funzioni. 
    
    Questo comando crea un'app per le funzioni che esegue il runtime del linguaggio specificato nel [Piano a consumo di Funzioni di Azure](functions-scale.md#consumption-plan), che è gratuito per la quantità di utilizzo prevista in questo argomento. Il comando effettua anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse, con cui è possibile monitorare l'app per le funzioni e visualizzare i log. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). L'istanza non comporta costi finché non viene attivata.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Eseguire il comando seguente per visualizzare i [log in streaming](functions-run-local.md#enable-streaming-logs) near real-time in Application Insights nel portale di Azure:

```console
func azure functionapp logstream <APP_NAME> --browser
```

In una finestra del terminale separata o nel browser chiamare di nuovo la funzione remota. Nel terminale viene visualizzato un log dettagliato dell'esecuzione della funzione in Azure. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Problemi? Segnalarli](https://aka.ms/python-functions-qs-survey).