---
title: Inviare transazioni con il servizio Azure Blockchain
description: Esercitazione su come usare il servizio Azure Blockchain per distribuire un contratto intelligente e inviare una transazione privata.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9037c7b5498a5e0a37b05e5ee09891bf8066393d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417478"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Esercitazione: Inviare transazioni con il servizio Azure Blockchain

Questa esercitazione descrive come creare nodi di transazioni per testare la privacy di contratti e transazioni.  Verrà usato Truffle per creare l'ambiente di sviluppo locale, distribuire un contratto intelligente e inviare una transazione privata.

Si apprenderà come:

> [!div class="checklist"]
> * Aggiungere nodi di transazioni
> * Usare Truffle per distribuire un contratto intelligente
> * Inviare una transazione
> * Verificare la privacy delle transazioni

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare [Creare un membro della blockchain con il portale di Azure](create-member.md)
* Completare [Avvio rapido: Usare Truffle per connettersi alla rete di un consorzio](connect-truffle.md)
* Installare [Truffle](https://github.com/trufflesuite/truffle). Truffle richiede l'installazione di diversi strumenti, tra cui [Node.js](https://nodejs.org) e [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installare [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python è necessario per Web3.
* Installare [Visual Studio Code](https://code.visualstudio.com/Download)
* Installare l'[estensione Solidity di Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Creare nodi di transazioni

Per impostazione predefinita, è disponibile un nodo di transazioni. Ora ne verranno aggiunti altri due. Uno dei nodi partecipa alla transazione privata. L'altro non è incluso nella transazione privata.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro di Azure Blockchain e selezionare **Transaction nodes > Add** (Nodi di transazioni > Aggiungi).
1. Completare le impostazioni del nuovo nodo di transazioni denominato `alpha`.

    ![Creare il nodo di transazioni](./media/send-transaction/create-node.png)

    | Impostazione | Valore | DESCRIZIONE |
    |---------|-------|-------------|
    | NOME | `alpha` | Nome del nodo di transazioni. Il nome viene usato per creare l'indirizzo DNS per l'endpoint del nodo di transazioni. Ad esempio: `alpha-mymanagedledger.blockchain.azure.com`. |
    | Password | Password complessa | La password viene usata per accedere all'endpoint del nodo di transazioni con l'autenticazione di base.

1. Selezionare **Create** (Crea).

    Il provisioning del nuovo nodo di transazioni richiede circa 10 minuti.

1. Ripetere i passaggi da 2 a 4 per aggiungere un nodo di transazioni denominato `beta`.

Durante il provisioning dei nodi, è possibile continuare con l'esercitazione. Al termine del provisioning, si avranno tre nodi di transazioni.

## <a name="open-truffle-console"></a>Aprire la console di Truffle

1. Aprire una shell o un prompt dei comandi Node.js.
1. Impostare il percorso sulla directory del progetto Truffle creato con il prerequisito [Avvio rapido: Usare Truffle per connettersi alla rete di un consorzio](connect-truffle.md). Ad esempio,

    ```bash
    cd truffledemo
    ```

1. Avviare la console di sviluppo interattiva di Truffle.

    ``` bash
    truffle develop
    ```

    Truffle crea una blockchain di sviluppo locale e fornisce una console interattiva.

## <a name="create-ethereum-account"></a>Creazione di account Ethereum

Usare Web3 per connettersi al nodo di transazioni predefinito e creare un account Ethereum. È possibile ottenere la stringa di connessione di Web3 nel portale di Azure.

1. Nel portale di Azure passare al nodo di transazioni predefinito e selezionare **Transaction nodes > Sample code > Web3** (Nodi di transazioni > Codice di esempio > Web3).
1. Copiare il codice JavaScript da **HTTPS (Access key 1)** ![Web3 sample code](./media/send-transaction/web3-code.png)

1. Incollare il codice JavaScript Web3 per il nodo di transazioni predefinito nella console di sviluppo interattiva di Truffle. Il codice crea un oggetto Web3 connesso al nodo di transazioni del servizio Azure Blockchain.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    È possibile chiamare metodi nell'oggetto Web3 per interagire con il nodo di transazioni.

1. Creare un nuovo account nel nodo di transazioni predefinito. Sostituire il parametro password con la propria password complessa.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Prendere nota dell'indirizzo dell'account restituito e della password. L'indirizzo e la password dell'account Ethereum saranno necessari nella sezione successiva.

1. Uscire dall'ambiente di sviluppo Truffle.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Configurare il progetto Truffle

Per configurare il progetto Truffle, sono necessarie alcune informazioni sul nodo di transazioni disponibili nel portale di Azure.

### <a name="transaction-node-public-key"></a>Chiave pubblica del nodo di transazioni

Ogni nodo di transazioni ha una chiave pubblica. La chiave pubblica consente di inviare una transazione privata al nodo. Per inviare una transazione dal nodo di transazioni predefinito al nodo *alpha*, è necessaria la chiave pubblica del nodo *alpha*.

È possibile ottenere la chiave pubblica nell'elenco di nodi di transazioni. Copiare la chiave pubblica del nodo alpha e salvare il valore per un uso futuro nell'esercitazione.

![Elenco di nodi di transazioni](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Indirizzi degli endpoint dei nodi di transazioni

1. Nel portale di Azure passare a ogni nodo di transazioni e selezionare **Transaction nodes > Connection strings** (Nodi di transazioni > Stringhe di connessione).
1. Copiare e salvare l'URL dell'endpoint da **HTTPS (Chiave di accesso 1)** per ogni nodo della transazione. Gli indirizzi degli endpoint sono necessari per il file di configurazione del contratto intelligente più avanti nell'esercitazione.

    ![Indirizzo dell'endpoint delle transazioni](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Modificare il file di configurazione

1. Avviare Visual Studio Code e aprire la cartella della directory del progetto Truffle usando il menu **File > Apri cartella**.
1. Aprire il file di configurazione di Truffle `truffle-config.js`
1. Sostituire il contenuto del file con le informazioni di configurazione seguenti. Aggiungere le variabili contenenti gli indirizzi degli endpoint e le informazioni dell'account. Sostituire le sezioni tra parentesi uncinate con i valori raccolti nelle sezioni precedenti.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";
    var alpha = "<alpha transaction node connection string>";
    var beta = "<beta transaction node connection string>";
    
    var myAccount = "<Ethereum account address>";
    var myPassword = "<Ethereum account password>";
    
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider:(() =>  {
          const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);
    
          const web3 = new Web3(AzureBlockchainProvider);
          web3.eth.personal.unlockAccount(myAccount, myPassword);
    
          return AzureBlockchainProvider;
          })(),
    
          network_id: "*",
          gas: 0,
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        }
      }
    }
    ```

1. Salvare le modifiche in `truffle-config.js`.

## <a name="create-smart-contract"></a>Creare il contratto intelligente

1. Nella cartella **contracts** creare un nuovo file denominato `SimpleStorage.sol`. Aggiungere il codice seguente.

    ```solidity
    pragma solidity >=0.4.21 <0.6.0;
    
    contract SimpleStorage {
        string public storedData;
    
        constructor(string memory initVal) public {
            storedData = initVal;
        }
    
        function set(string memory x) public {
            storedData = x;
        }
    
        function get() view public returns (string memory retVal) {
            return storedData;
        }
    }
    ```
    
1. Nella cartella **migrations** creare un nuovo file denominato `2_deploy_simplestorage.js`. Aggiungere il codice seguente.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Sostituire i valori racchiusi tra le parentesi uncinate.

    | Valore | DESCRIZIONE
    |-------|-------------
    | \<alpha node public key\> | Chiave pubblica del nodo alpha
    | \<Ethereum account address\> | Indirizzo dell'account Ethereum creato nel nodo di transazioni predefinito

    In questo esempio il valore iniziale di **storeData** è impostato su 42.

    **privateFor** definisce i nodi in cui il contratto è disponibile. In questo esempio l'account del nodo di transazioni predefinito può eseguire il cast delle transazioni private nel nodo **alpha**. È necessario aggiungere chiavi pubbliche per tutti i partecipanti alla transazione privata. Se non si includono i valori per **privateFor:** e **from:** , le transazioni del contratto intelligente sono pubbliche e possono essere viste da tutti i membri del consorzio.

1. Salvare il file selezionando **File > Save All** (File > Salva tutto).

## <a name="deploy-smart-contract"></a>Distribuire il contratto intelligente

Usare Truffle per distribuire `SimpleStorage.sol` nella rete del nodo di transazioni predefinito.

```bash
truffle migrate --network defaultnode
```

Truffle prima compila e poi distribuisce il contratto intelligente **SimpleStorage**.

Output di esempio:

```
admin@desktop:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Verificare la privacy del contratto

A causa della privacy, i valori del contratto possono essere sottoposti a query solo dai nodi dichiarati in **privateFor**. In questo esempio è possibile eseguire query sul nodo di transazioni predefinito perché l'account si trova in tale nodo. 

1. Usando la console di Truffle connettersi al nodo di transazioni predefinito.

    ```bash
    truffle console --network defaultnode
    ```

1. Nella console di Truffle eseguire il codice che restituisce il valore dell'istanza del contratto.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Se la query sul nodo di transazioni predefinito riesce, viene restituito il valore 42. Ad esempio:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Uscire dalla console di Truffle.

    ```bash
    .exit
    ```

Poiché in **privateFor** è stata dichiarata la chiave pubblica del nodo **alpha**, è possibile eseguire query sul nodo **alpha**.

1. Usando la console di Truffle connettersi al nodo **alpha**.

    ```bash
    truffle console --network alpha
    ```

1. Nella console di Truffle eseguire il codice che restituisce il valore dell'istanza del contratto.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Se la query sul nodo **alpha** riesce, viene restituito il valore 42. Ad esempio:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Uscire dalla console di Truffle.

    ```bash
    .exit
    ```

Poiché in **privateFor** non è stata dichiarata la chiave pubblica del nodo **beta**, non è possibile eseguire query sul nodo **beta** a causa della privacy del contratto.

1. Usando la console di Truffle connettersi al nodo **beta**.

    ```bash
    truffle console --network beta
    ```

1. Eseguire il codice che restituisce il valore dell'istanza del contratto.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. L'esecuzione di query sul nodo **beta** non riesce perché il contratto è privato. Ad esempio:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network beta
    truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    Thrown:
    Error: Returned values aren't valid, did it run Out of Gas?
        at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
        at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
        at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
        at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
    ```

1. Uscire dalla console di Truffle.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Inviare una transazione

1. Creare un file denominato `sampletx.js`. Salvarlo nella radice del progetto.
1. Lo script seguente imposta il valore della variabile **storedData** del contratto su 65. Aggiungere il codice al nuovo file.

    ```javascript
    var SimpleStorage = artifacts.require("SimpleStorage");
    
    module.exports = function(done) {
      console.log("Getting deployed version of SimpleStorage...")
      SimpleStorage.deployed().then(function(instance) {
        console.log("Setting value to 65...");
        return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"});
      }).then(function(result) {
        console.log("Transaction:", result.tx);
        console.log("Finished!");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

    Sostituire i valori racchiusi tra le parentesi uncinate e quindi salvare il file.

    | Valore | DESCRIZIONE
    |-------|-------------
    | \<alpha node public key\> | Chiave pubblica del nodo alpha
    | \<Ethereum account address\> | Indirizzo dell'account Ethereum creato nel nodo di transazioni predefinito

    **privateFor** definisce i nodi in cui la transazione è disponibile. In questo esempio l'account del nodo di transazioni predefinito può eseguire il cast delle transazioni private nel nodo **alpha**. È necessario aggiungere chiavi pubbliche per tutti i partecipanti alla transazione privata.

1. Usare Truffle per eseguire lo script per il nodo di transazioni predefinito.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. Nella console di Truffle eseguire il codice che restituisce il valore dell'istanza del contratto.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Se la transazione riesce, viene restituito il valore 65. Ad esempio:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Uscire dalla console di Truffle.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Verificare la privacy delle transazioni

A causa della privacy, è possibile eseguire transazioni solo nei nodi dichiarati in **privateFor**. In questo esempio è possibile eseguire transazioni perché in **privateFor** è stata dichiarata la chiave pubblica del nodo **alpha**. 

1. Usare Truffle per eseguire la transazione nel nodo **alpha**.

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Eseguire il codice che restituisce il valore dell'istanza del contratto.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Se la transazione riesce, viene restituito il valore 65. Ad esempio:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Uscire dalla console di Truffle.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse `myResourceGroup` creato dal servizio Azure Blockchain.

Per eliminare il gruppo di risorse:

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare.
1. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati aggiunti due nodi di transazioni per dimostrare la privacy di contratti e transazioni. È stato usato il nodo predefinito per distribuire un contratto intelligente privato. La privacy è stata testata eseguendo query sui valori del contratto ed eseguendo transazioni nella blockchain.

> [!div class="nextstepaction"]
> [Sviluppo di applicazioni blockchain con il servizio Azure Blockchain](develop.md)
