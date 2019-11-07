---
title: Configurare blockchain Data Manager-interfaccia della riga di comando di Azure
description: Come creare e gestire Data Manager blockchain usando l'interfaccia della riga di comando di Azure
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 9f408b090db40e5145b424034c39cdba4de14a8f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605909"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Configurare Data Manager blockchain usando l'interfaccia della riga di comando di Azure

Configurare blockchain Data Manager per il servizio blockchain di Azure per acquisire i dati blockchain inviarli a un argomento di griglia di eventi di Azure.

Per configurare un'istanza di Data Manager blockchain, è necessario:

* Creare un'istanza di blockchain Manager
* Creare un input per un nodo di transazione del servizio blockchain di Azure
* Creare un output in un argomento di griglia di eventi di Azure
* Aggiungere un'applicazione blockchain
* Avviare un'istanza

## <a name="prerequisites"></a>Prerequisiti

* Installare la versione più recente dell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e accedere usando `az login`.
* [Guida introduttiva completa: usare Visual Studio Code per connettersi a una rete di Azure blockchain Service Consortium](connect-vscode.md)
* Creare un [argomento di griglia di eventi](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informazioni sui [gestori eventi in griglia di eventi di Azure](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.51 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Crea istanza

Un'istanza di Data Manager blockchain monitora un nodo di transazione del servizio blockchain di Azure. Un'istanza acquisisce tutti i dati del blocco non elaborato e della transazione non elaborati dal nodo della transazione.

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
| resource-group | Nome del gruppo di risorse in cui creare l'istanza di blockchain Data Manager. |
| name | Nome dell'istanza di Data Manager blockchain. |
| tipo di risorsa | Il tipo di risorsa per un'istanza di blockchain Data Manager è **Microsoft. blockchain/Watcher**. |
| is-full-object | Indica che le proprietà contengono opzioni per la risorsa Watcher. |
| properties | Stringa in formato JSON che contiene le proprietà per la risorsa Watcher. Può essere passato come stringa o come file.  |

### <a name="create-instance-examples"></a>Esempi di creazione di istanze

Esempio di configurazione JSON per creare un'istanza di blockchain Manager nell'area **Stati Uniti orientali** .

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elemento | Descrizione |
|---------|-------------|
| location | Area in cui creare la risorsa Watcher |
| properties | Proprietà da impostare quando si crea la risorsa Watcher |

Creare un'istanza di Data Manager blockchain denominata *controllore* usando una stringa JSON per la configurazione.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Creare un'istanza di Data Manager blockchain denominata *controllore* usando un file di configurazione JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Creare input

Un input connette blockchain Data Manager a un nodo di transazione del servizio blockchain di Azure. Solo gli utenti con accesso al nodo di transazione possono creare una connessione.

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
| namespace | Utilizzare lo spazio dei nomi del provider **Microsoft. blockchain** . |
| tipo di risorsa | Il tipo di risorsa per un input di Data Manager **blockchain è input**. |
| padre | Percorso al Watcher a cui è associato l'input. Ad esempio, **Watcher/** controllo. |
| is-full-object | Indica che le proprietà contengono opzioni per la risorsa di input. |
| properties | Stringa in formato JSON che contiene le proprietà per la risorsa di input. Può essere passato come stringa o come file. |

### <a name="input-examples"></a>Esempi di input

Esempio JSON di configurazione per creare una risorsa di input nell'area *Stati Uniti orientali* connessa a \<membro blockchain\>.

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
| location | Area in cui creare la risorsa di input. |
| inputType | Tipo di Ledger del membro del servizio Azure blockchain. Attualmente, **Ethereum** è supportato. |
| resourceId | Nodo della transazione a cui è connesso l'input. Sostituire \<ID sottoscrizione\>, \<\>del gruppo di risorse e \<membro blockchain\> con i valori per la risorsa del nodo della transazione. L'input si connette al nodo di transazione predefinito per il membro del servizio Azure blockchain. |

Creare un input denominato input per il *controllo* tramite una stringa *JSON per la* configurazione.

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

Creare un *input denominato input* per il *controllo* tramite un file di configurazione JSON.

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

Una connessione in uscita invia i dati di blockchain a griglia di eventi di Azure. È possibile inviare i dati di blockchain a una singola destinazione o inviare dati blockchain a più destinazioni. Blockchain Data Manager supporta le connessioni in uscita per più argomenti di griglia di eventi per qualsiasi istanza di blockchain Data Manager specificata.

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
| namespace | Utilizzare lo spazio dei nomi del provider **Microsoft. blockchain** . |
| tipo di risorsa | Il tipo di risorsa per un output di blockchain **Data Manager è**Outputs. |
| padre | Percorso al Watcher a cui è associato l'output. Ad esempio, **Watcher/** controllo. |
| is-full-object | Indica che le proprietà contengono opzioni per la risorsa di output. |
| properties | Stringa in formato JSON che contiene le proprietà per la risorsa di output. Può essere passato come stringa o come file. |

### <a name="output-examples"></a>Esempi di output

Esempio di JSON di configurazione per creare una risorsa di output nell'area *Stati Uniti orientali* connessa a un argomento di griglia di eventi denominato \<\>argomento di griglia di eventi.

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
| location | Area in cui creare la risorsa di output. |
| outputType | Tipo di output. Attualmente, **EventGrid** è supportato. |
| resourceId | Risorsa a cui è connesso l'output. Sostituire \<ID sottoscrizione\>, \<\>del gruppo di risorse e \<membro blockchain\> con i valori per la risorsa griglia di eventi. |

Creare un output denominato output per il *controllo* che si connette a un *argomento di griglia* di eventi usando una stringa di configurazione JSON.

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

Creare un output denominato output per il *controllo* che si connette a un argomento di griglia di eventi usando un *file di configurazione* JSON.

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

Se si aggiunge un'applicazione blockchain, blockchain Data Manager decodifica l'evento e lo stato della proprietà per l'applicazione. In caso contrario, vengono inviati solo i dati delle transazioni RAW e i blocchi Raw. Blockchain Data Manager individua anche gli indirizzi del contratto quando il contratto viene distribuito. È possibile aggiungere più applicazioni blockchain a un'istanza di blockchain Data Manager.


> [!IMPORTANT]
> Attualmente, le applicazioni blockchain che dichiarano [tipi di matrici](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) di solidità o [tipi di mapping](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) non sono completamente supportate. Le proprietà dichiarate come matrici o tipi di mapping non verranno decodificate nei messaggi *ContractPropertiesMsg* o *DecodedContractEventsMsg* .

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
| namespace | Utilizzare lo spazio dei nomi del provider **Microsoft. blockchain** . |
| tipo di risorsa | Il tipo di risorsa per un'applicazione blockchain Data Manager è **artefatti**. |
| padre | Percorso al Watcher a cui è associata l'applicazione. Ad esempio, **Watcher/** controllo. |
| is-full-object | Indica che le proprietà contengono opzioni per la risorsa dell'applicazione. |
| properties | Stringa in formato JSON che contiene le proprietà per la risorsa dell'applicazione. Può essere passato come stringa o come file. |

### <a name="blockchain-application-examples"></a>Esempi di applicazioni blockchain

Esempio JSON di configurazione per creare una risorsa dell'applicazione nell'area *Stati Uniti orientali* che monitora un contratto intelligente definito dall'ABI e dal bytecode del contratto.

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
| location | Area in cui creare la risorsa dell'applicazione. |
| artifactType | Tipo di applicazione. Attualmente, **EthereumSmartContract** è supportato. |
| abiFileUrl | URL per il file JSON ABI Smart Contract. Per ulteriori informazioni su come ottenere l'ABI del contratto e creare un URL, vedere [Get Contract Abi and bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e [create Contract Abi and bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL del file JSON bytecode distribuito con Smart Contract. Per ulteriori informazioni su come ottenere il bytecode distribuito con Smart contract e creare un URL, vedere [Get Contract Abi and bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e [create Contract Abi and bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Nota: blockchain Data Manager richiede il **bytecode distribuito**. |
| queryTargetTypes | Tipi di messaggio pubblicati. Se si specifica **ContractProperties** , viene pubblicato il tipo di messaggio *ContractPropertiesMsg* . Se si specifica **ContractEvents** , viene pubblicato il tipo di messaggio *DecodedContractEventsMsg* . Nota: i tipi di messaggi *RawBlockAndTransactionMsg* e *RawTransactionContractCreationMsg* vengono sempre pubblicati. |

Creare un'applicazione denominata *MyApplication* per il *controllo* che monitora un contratto intelligente definito da una stringa JSON.

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

Creare un'applicazione denominata *MyApplication* per il *controllo* che controlla un contratto intelligente definito usando un file di configurazione JSON.

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

## <a name="start-instance"></a>Avvia istanza

Quando è in esecuzione, un'istanza di blockchain Manager monitora gli eventi blockchain dagli input definiti e invia i dati agli output definiti.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametro | Descrizione |
|-----------|-------------|
| action | Usare **Start** per eseguire il Watcher. |
| ids | ID risorsa Watcher. Sostituire \<ID sottoscrizione\>, \<\>del gruppo di risorse e \<nome del Watcher\> con i valori per la risorsa Watcher.|

### <a name="start-instance-example"></a>Esempio di istanza di avvio

Avviare un'istanza di Data Manager blockchain denominata *controllo*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Arresta istanza

Arrestare un'istanza di Data Manager blockchain.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametro | Descrizione |
|-----------|-------------|
| action | Utilizzare **Interrompi** per arrestare il Watcher. |
| ids | Nome del Watcher. Sostituire \<ID sottoscrizione\>, \<\>del gruppo di risorse e \<nome del Watcher\> con i valori per la risorsa Watcher. |

### <a name="stop-watcher-example"></a>Esempio di arresta Watcher

Arrestare un'istanza denominata *controllo*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Elimina istanza

Eliminare un'istanza di Data Manager blockchain.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parametro | Descrizione |
|-----------|-------------|
| resource-group | Nome del gruppo di risorse del Watcher da eliminare. |
| name | Nome del Watcher da eliminare. |
| tipo di risorsa | Il tipo di risorsa per un blockchain Data Manager Watcher è **Microsoft. blockchain/Watchers**. |

### <a name="delete-instance-example"></a>Esempio di eliminazione dell'istanza

Eliminare un'istanza denominata *controllo* nel gruppo di risorse *myRG* .

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Passaggi successivi

Provare a creare un Visualizzatore messaggi di transazione blockchain usando blockchain Data Manager e Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Esercitazione: usare blockchain Data Manager per inviare dati a Azure Cosmos DB](data-manager-cosmosdb.md)
