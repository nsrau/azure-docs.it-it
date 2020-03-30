---
title: Configurare Blockchain Data Manager usando l'interfaccia della riga di comando di Azure - Servizio Blockchain di AzureConfigure Blockchain Data Manager using Azure CLI - Azure Blockchain Service
description: Creare e gestire un servizio Blockchain Data Manager per Azure Blockchain usando l'interfaccia della riga di comando di AzureCreate and manage a Blockchain Data Manager for Azure Blockchain Service using Azure CLI
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455932"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Configurare Blockchain Data Manager con l'interfaccia della riga di comando di Azure

Configurare Blockchain Data Manager per il servizio Blockchain di Azure per acquisire i dati blockchain inviarli a un argomento griglia di eventi di Azure.Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data send it to an Azure Event Grid Topic.

Per configurare un'istanza di Blockchain Data Manager, è necessario:

* Creare un'istanza di Blockchain ManagerCreate a Blockchain Manager instance
* Creare un input per un nodo di transazione del servizio Blockchain di AzureCreate an input to an Azure Blockchain Service transaction node
* Creare un output in un argomento Griglia di eventi di AzureCreate an output to an Azure Event Grid Topic
* Aggiungere un'applicazione blockchain
* Avviare un'istanza

## <a name="prerequisites"></a>Prerequisiti

* Installare [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli) di Azure più recente e accedere tramite `az login`.
* [Guida introduttiva completa: Usare il codice di Visual Studio per connettersi a una rete di consorzii di servizi Blockchain di AzureComplete Quickstart: Use Visual Studio Code to connect to a Azure Blockchain Service consortium network](connect-vscode.md)
* Creare un [argomento della griglia di eventiCreate](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) an Event Grid Topic
* Informazioni su [Gestori eventi di Griglia di eventi di Azure](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È inoltre possibile avviare Cloud Shell in [https://shell.azure.com/bash](https://shell.azure.com/bash)una scheda separata del browser accedendo a . Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.51 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli)di Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato myResourceGroup nella posizione *eastus:The* following example creates a resource group named *myResourceGroup* in the eastus location:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Creare un'istanza

Un'istanza di Blockchain Data Manager esegue il monitoraggio di un nodo di transazione del servizio Blockchain di Azure.A Blockchain Data Manager instance monitors an Azure Blockchain Service transaction node. Un'istanza acquisisce tutti i dati di blocchi e transazioni non elaborati dal nodo della transazione.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parametro | Descrizione |
|-----------|-------------|
| resource-group | Nome del gruppo di risorse in cui creare l'istanza di Blockchain Data Manager.Resource group name where to create the Blockchain Data Manager instance. |
| name | Nome dell'istanza di Blockchain Data Manager. |
| tipo di risorsa | Il tipo di risorsa per un'istanza di Blockchain Data Manager è **Microsoft.blockchain/watchers**. |
| is-full-object | Indica che le proprietà contengono opzioni per la risorsa Watcher. |
| properties | Stringa in formato JSON contenente le proprietà per la risorsa Watcher. Può essere passato come stringa o file.  |

### <a name="create-instance-examples"></a>Creare esempi di istanzeCreate instance examples

Esempio di configurazione JSON per creare un'istanza di Blockchain Manager nell'area **Stati Uniti orientali.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elemento | Descrizione |
|---------|-------------|
| posizione | Area in cui creare la risorsa Watcher |
| properties | Proprietà da impostare durante la creazione della risorsa WatcherProperties to set when creating the watcher resource |

Creare un'istanza di Blockchain Data Manager *denominata mywatcher* usando una stringa JSON per la configurazione.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Creare un'istanza di Blockchain Data Manager *denominata mywatcher* usando un file di configurazione JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Creare input

Un input connette Blockchain Data Manager a un nodo di transazione del servizio Blockchain di Azure.An input connects Blockchain Data Manager to an Azure Blockchain Service transaction node. Solo gli utenti con accesso al nodo della transazione possono creare una connessione.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parametro | Descrizione |
|-----------|-------------|
| resource-group | Nome del gruppo di risorse in cui creare la risorsa di input. |
| name | Nome dell'input. |
| namespace | Utilizzare lo spazio dei nomi del provider **Microsoft.Blockchain.Use the Microsoft.Blockchain** provider namespace. |
| tipo di risorsa | Il tipo di risorsa per un input Blockchain Data Manager è **input**. |
| padre | Percorso del watcher a cui è associato l'input. Ad esempio, **watchers/mywatcher**. |
| is-full-object | Indica che le proprietà contengono opzioni per la risorsa di input. |
| properties | Stringa in formato JSON contenente le proprietà per la risorsa di input. Può essere passato come stringa o file. |

### <a name="input-examples"></a>Esempi di input

Esempio JSON di configurazione per creare una risorsa di \<input\>nell'area Stati *Uniti orientali* connessa al membro Blockchain .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Elemento | Descrizione |
|---------|-------------|
| posizione | Area in cui creare la risorsa di input. |
| inputType (Tipo di input) | Tipo di contabilità generale del membro Servizio Blockchain di Azure.Ledger type of the Azure Blockchain Service member. Attualmente, **Ethereum** è supportato. |
| resourceId | Nodo della transazione a cui è connesso l'input. Sostituire \<id\> \<sottoscrizione\>, \<gruppo\> di risorse e membro Blockchain con i valori per la risorsa del nodo della transazione. L'input si connette al nodo di transazione predefinito per il membro del servizio Blockchain di Azure.The input connects to the default transaction node for the Azure Blockchain Service member. |

Creare un input denominato *myInput* per *mywatcher* usando una stringa JSON per la configurazione.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Creare un input denominato *myInput* per *mywatcher* usando un file di configurazione JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Crea output

Una connessione in uscita invia i dati della blockchain a Griglia di eventi di Azure. È possibile inviare i dati della blockchain a una o più destinazioni. Blockchain Data Manager supporta più connessioni in uscita per gli argomenti di Griglia di eventi per qualsiasi istanza di Blockchain Data Manager specificata.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parametro | Descrizione |
|-----------|-------------|
| resource-group | Nome del gruppo di risorse in cui creare la risorsa di output. |
| name | Nome dell'output. |
| namespace | Utilizzare lo spazio dei nomi del provider **Microsoft.Blockchain.Use the Microsoft.Blockchain** provider namespace. |
| tipo di risorsa | Il tipo di risorsa per un output di Blockchain Data Manager è **output .** |
| padre | Percorso del watcher a cui è associato l'output. Ad esempio, **watchers/mywatcher**. |
| is-full-object | Indica che le proprietà contengono opzioni per la risorsa di output. |
| properties | Stringa in formato JSON contenente le proprietà per la risorsa di output. Può essere passato come stringa o file. |

### <a name="output-examples"></a>Esempi di output

Esempio JSON di configurazione per creare una risorsa di output nell'area Stati\>Uniti *orientali* connessa a un argomento della griglia di eventi denominato \<argomento della griglia di eventi .

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Elemento | Descrizione |
|---------|-------------|
| posizione | Area in cui creare la risorsa di output. |
| outputType (Tipo di output) | Tipo di uscita. Attualmente, **EventGrid** è supportato. |
| resourceId | Risorsa a cui è connesso l'output. Sostituire \<i\> \<membri\> \>ID \<sottoscrizione , Gruppo di risorse e Blockchain con i valori per la risorsa griglia di eventi. |

Creare un output denominato *myoutput* per *mywatcher* che si connette a un argomento della griglia di eventi usando una stringa di configurazione JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Creare un output denominato *myoutput* per *mywatcher* che si connette a un argomento della griglia di eventi usando un file di configurazione JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Aggiungi applicazione blockchain

Se si aggiunge un'applicazione blockchain, Blockchain Data Manager decodifica lo stato dell'evento e della proprietà per l'applicazione. In caso contrario, vengono inviati solo i dati non elaborati relativi ai blocchi e alle transazioni non elaborati. Blockchain Data Manager scopre anche gli indirizzi del contratto quando il contratto viene distribuito. È possibile aggiungere più applicazioni blockchain a un'istanza di Blockchain Data Manager.You can add multiple blockchain applications to a Blockchain Data Manager instance.


> [!IMPORTANT]
> Attualmente, le applicazioni blockchain che dichiarano tipi di [matrice](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) Solidity o tipi di [mapping](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) non sono completamente supportate. Le proprietà dichiarate come tipi di matrice o mapping non verranno decodificate nei messaggi *ContractPropertiesMsg* o *DecodedContractEventsMsg.*

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parametro | Descrizione |
|-----------|-------------|
| resource-group | Nome del gruppo di risorse in cui creare la risorsa dell'applicazione. |
| name | Nome dell'applicazione. |
| namespace | Utilizzare lo spazio dei nomi del provider **Microsoft.Blockchain.Use the Microsoft.Blockchain** provider namespace. |
| tipo di risorsa | Il tipo di risorsa per un'applicazione Blockchain Data Manager è **artifacts**. |
| padre | Percorso del watcher a cui è associata l'applicazione. Ad esempio, **watchers/mywatcher**. |
| is-full-object | Indica che le proprietà contengono opzioni per la risorsa dell'applicazione. |
| properties | Stringa in formato JSON contenente le proprietà per la risorsa dell'applicazione. Può essere passato come stringa o file. |

### <a name="blockchain-application-examples"></a>Esempi di applicazioni Blockchain

Esempio JSON di configurazione per creare una risorsa dell'applicazione nell'area *Stati Uniti orientali* che monitora un contratto intelligente definito dall'interfaccia ABI del contratto e dal bytecode.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Elemento | Descrizione |
|---------|-------------|
| posizione | Area in cui creare la risorsa dell'applicazione. |
| artefactType | Tipo di applicazione. Attualmente, **EthereumSmartContract** è supportato. |
| abiFileUrl (informazioni in base al nome) | URL per il file JSON ABI del contratto intelligente. Per ulteriori informazioni su come ottenere l'ABI del contratto e creare un URL, vedere [Ottenere ABI del contratto e bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e [Creare il contratto ABI e bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL per il file JSON del bytecode distribuito dal contratto intelligente. Per ulteriori informazioni su come ottenere il bytecode distribuito dal contratto intelligente e sulla creazione di un URL, vedere [Ottenere l'ABI del contratto e il bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e [Creare l'ABI del contratto e l'URL del bytecode](data-manager-portal.md#create-contract-abi-and-bytecode-url). Nota: Blockchain Data Manager richiede il **bytecode distribuito.** |
| QueryTargetTypes (Tipididi di oggetto<a | Tipi di messaggi pubblicati. Specificando **ContractProperties** pubblica il tipo di messaggio *ContractPropertiesMsg.* Specificando **ContractEvents** pubblica il tipo di messaggio *DecodedContractEventsMsg.* Nota: i tipi di messaggio *RawBlockAndTransactionMsg* e *RawTransactionContractCreationMsg* sono sempre pubblicati. |

Creare un'applicazione denominata *myApplication* per *mywatcher* che monitora un contratto intelligente definito da una stringa JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Creare un'applicazione denominata *myApplication* per *mywatcher* che controlla un contratto intelligente definito usando un file di configurazione JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Istanza iniziale

Durante l'esecuzione, un'istanza di Blockchain Manager monitora gli eventi blockchain dagli input definiti e invia i dati agli output definiti.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametro | Descrizione |
|-----------|-------------|
| action | Utilizzare **start** per eseguire l'osservatore. |
| ids | ID risorsa Watcher. Sostituire \<ID\> \<sottoscrizione\>, \<Gruppo\> di risorse e Nome Watcher con i valori per la risorsa Watcher.|

### <a name="start-instance-example"></a>Esempio di istanza inizialeStart instance example

Avviare un'istanza Di Blockchain Data Manager denominata *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Interrompi istanza

Interrompere un'istanza di Blockchain Data Manager.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametro | Descrizione |
|-----------|-------------|
| action | Utilizzare **stop** per arrestare l'osservatore. |
| ids | Nome dell'osservatore. Sostituire \<ID\> \<sottoscrizione\>, \<Gruppo\> di risorse e Nome Watcher con i valori per la risorsa Watcher. |

### <a name="stop-watcher-example"></a>Esempio di Stop Watcher

Interrompere un'istanza denominata *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Elimina istanza

Eliminare un'istanza di Blockchain Data Manager.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parametro | Descrizione |
|-----------|-------------|
| resource-group | Nome del gruppo di risorse del watcher da eliminare. |
| name | Nome del watcher da eliminare. |
| tipo di risorsa | Il tipo di risorsa per un watcher di Blockchain Data Manager è **Microsoft.blockchain/watchers**. |

### <a name="delete-instance-example"></a>Esempio di istanza di eliminazioneDelete instance example

Eliminare un'istanza *denominata mywatcher* nel gruppo di risorse *myRG.*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione successiva sulla creazione di un esploramessaggi di transazioni blockchain usando Blockchain Data Manager e Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Usare Blockchain Data Manager per inviare dati ad Azure Cosmos DB](data-manager-cosmosdb.md)
