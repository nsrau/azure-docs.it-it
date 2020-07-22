---
title: 'Avvio rapido: Eseguire la prima query Python'
description: In questa guida introduttiva vengono descritti i passaggi per abilitare la libreria Resource Graph per Python ed eseguire la prima query.
ms.date: 07/15/2020
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: f7ca6ce5fab687e26007949898ad72c75d036782
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511921"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Avvio rapido: Eseguire la prima query di Resource Graph usando Python

Il primo passaggio per usare Azure Resource Graph consiste nel verificare che le librerie necessarie per Python siano installate. Questa guida introduttiva illustra il processo di aggiunta delle librerie all'installazione di Python.

Al termine del processo, le librerie risulteranno aggiunte all'installazione di Python ed è stata eseguita la prima query di Resource Graph.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Aggiungere la libreria Resource Graph

Per consentire a Python di eseguire query in Azure Resource Graph, è necessario aggiungere la libreria. Questa libreria funziona ovunque sia possibile usare Python, sia con [bash in Windows 10](/windows/wsl/install-win10) o con installazione locale.

1. Controllare che sia installata la versione di Python più recente o almeno la versione **3.8**. Se non è ancora installata, scaricarla dal sito [Python.org](https://www.python.org/downloads/).

1. Controllare che sia installata l'interfaccia della riga di comando di Azure più recente o almeno la versione **2.5.1**. Se non è ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > L'interfaccia della riga di comando di Azure è necessaria per consentire a Python di usare **l'autenticazione basata sull'interfaccia della riga di comando** negli esempi seguenti. Per informazioni su altre opzioni, vedere [Eseguire l'autenticazione tramite le librerie di gestione di Azure per Python](/azure/developer/python/azure-sdk-authenticate).

1. Eseguire l'autenticazione tramite l'interfaccia della riga di comando di Azure.

   ```azurecli
   az login
   ```

1. Nell'ambiente Python preferito installare le librerie necessarie per Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se Python è installato per tutti gli utenti, questi comandi devono essere eseguiti da una console con privilegi elevati.

1. Verificare che le librerie siano state installate. `azure-mgmt-resourcegraph` deve essere **2.0.0** o versione successiva, `azure-mgmt-resource` deve essere **9.0.0** o versione successiva e `azure-cli-core` deve essere **2.5.0** o versione successiva.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Dopo che le librerie di Python sono state aggiunte all'ambiente desiderato, è possibile provare una semplice query di Resource Graph. La query restituisce le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

1. Eseguire la prima query di Azure Resource Graph usando le librerie installate e il metodo `resources`:

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Poiché questo esempio di query non fornisce un modificatore di ordine, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Aggiornare la chiamata a `getresources` e modificare la query per applicare `order by` alla proprietà **Name**:

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. Questo ordine dei comandi prima limita i risultati della query e successivamente li ordina.

1. Aggiornare la chiamata a `getresources` e modificare la query per applicare prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende rimuovere le librerie installate dall'ambiente Python, è possibile farlo con il comando seguente:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state aggiunte le librerie di Resource Graph nell'ambiente Python ed è stata eseguita la prima query. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)
