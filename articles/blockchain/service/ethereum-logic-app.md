---
title: Come usare il connettore blockchain per Ethereum con App per la logica di Azure
description: Come usare il connettore blockchain per Ethereum con App per la logica di Azure per attivare funzioni di contratto intelligente e rispondere agli eventi di contratto intelligente.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720674"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>Come usare il connettore blockchain per Ethereum con App per la logica di Azure

Usare il [connettore blockchain per Ethereum](https://docs.microsoft.com/connectors/blockchainethereum/) con [App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/) per eseguire azioni di contratto intelligente e rispondere agli eventi di contratto intelligente. Ad esempio, si vuole creare un microservizio basato su REST che restituisce informazioni da un libro mastro della blockchain. Con un'app per la logica, è possibile accettare richieste HTTP che eseguono query per recuperare le informazioni archiviate in un libro mastro della blockchain.

## <a name="prerequisites"></a>Prerequisiti

* Completare il prerequisito facoltativo [Avvio rapido: Usare Visual Studio Code per connettersi alla rete di un consorzio del servizio Azure Blockchain](connect-vscode.md). Le guide di avvio rapido guidano l'utente attraverso l'installazione dell'estensione [Azure Blockchain Development Kit per Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e la configurazione dell'ambiente di sviluppo blockchain.

## <a name="create-a-logic-app"></a>Creare un'app per la logica

Le app per la logica consentono di pianificare e automatizzare i processi e i flussi di lavoro aziendali quando occorre integrare sistemi e servizi. In primo luogo, è necessario creare una logica che usa il connettore blockchain per Ethereum.

1. Nel [portale di Azure](https://portal.azure.com) scegliere **Crea una risorsa > Integrazione > App per la logica**.
1. In **Crea app per la logica** specificare i dettagli sulla località in cui creare l'app per la logica. Al termine, selezionare **Crea**.

    Per altre informazioni sulla creazione di App per la logica di Azure, vedere [Creare App per la logica di Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Dopo che l'app è stata distribuita in Azure, selezionare la risorsa app per la logica.
1. Nella sezione **Modelli** di Progettazione app per la logica scegliere **App per la logica vuota**.

Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica che si avvia ed esegue il flusso di lavoro.

Il connettore blockchain per Ethereum contiene un trigger e numerose azioni. Il trigger o l'azione usata dipendono dallo scenario in uso.

Scegliere una delle sezioni seguenti se il flusso di lavoro:

* Viene attivato al verificarsi di un evento nella blockchain, [usare il trigger di evento](#use-the-event-trigger).
* Esegue query su un contratto intelligente o ne distribuisce uno, [usare le azioni](#use-actions).
* Segue uno scenario comune, [generare un flusso di lavoro con il kit di sviluppo](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Usare il trigger di evento

Usare i trigger di evento della blockchain Ethereum quando l'app per la logica deve essere eseguita dopo che si verifica un evento di contratto intelligente. Ad esempio, per inviare un messaggio di posta elettronica quando viene chiamata una funzione di contratto intelligente.

1. In Progettazione app per la logica scegliere il connettore blockchain per Ethereum.
1. Nella scheda **Trigger** scegliere **When a smart contract event occurs** (Quando si verifica un evento di contratto intelligente).
1. Modificare o [creare una connessione API](#create-an-api-connection) al servizio Azure Blockchain.
1. Immettere i dettagli relativi al contratto intelligente per il quale dovranno essere controllati gli eventi.

    ![Proprietà del trigger di evento](./media/ethereum-logic-app/event-properties.png)

    | Proprietà | DESCRIZIONE |
    |----------|-------------|
    | **Contract ABI** (Interfaccia ABI contratto) | L'interfaccia ABI (Application Binary Interface) del contratto definisce le interfacce del contratto intelligente. Come [ottenere l'interfaccia ABI del contratto](#get-contract-abi). |
    | **Smart contract address** (Indirizzo contratto intelligente) | L'indirizzo del contratto è l'indirizzo di destinazione del contratto intelligente nella blockchain Ethereum. Come [ottenere l'indirizzo del contratto](#get-contract-address). |
    | **Nome evento** | Scegliere un evento di contratto intelligente da controllare. L'evento attiva l'app per la logica. |
    | **Intervallo** e **Frequenza** | Scegliere la frequenza di controllo dell'evento. |

1. Selezionare **Salva**.

Per completare l'app per la logica, è possibile aggiungere un nuovo passaggio che esegue un'azione in base al trigger di evento della blockchain Ethereum. Ad esempio, inviare un messaggio di posta elettronica.

## <a name="use-actions"></a>Usare le azioni

Usare le azioni della blockchain Ethereum quando si vuole che un'app per la logica esegua un'azione sul libro mastro della blockchain. Ad esempio, per creare un microservizio basato su REST che chiama una funzione di contratto intelligente quando viene inviata una richiesta HTTP a un'app per la logica.

Le azioni del connettore richiedono un trigger. È possibile usare un'azione del connettore blockchain per Ethereum come passaggio successivo dopo un trigger. Ad esempio, un trigger di richiesta HTTP per un microservizio.

1. In Progettazione app per la logica, sotto il trigger, selezionare **Nuovo passaggio**.
1. Scegliere il connettore blockchain per Ethereum.
1. Nella scheda **Azioni** scegliere una delle azioni disponibili.

    ![Proprietà delle azioni](./media/ethereum-logic-app/action-properties.png)

1. Modificare o [creare una connessione API](#create-an-api-connection) al servizio Azure Blockchain.
1. A seconda dell'azione scelta, specificare i dettagli seguenti relativi alla funzione di contratto intelligente.

    | Proprietà | DESCRIZIONE |
    |----------|-------------|
    | **Contract ABI** (Interfaccia ABI contratto) | L'interfaccia ABI (Application Binary Interface) del contratto definisce le interfacce del contratto intelligente. Come [ottenere l'interfaccia ABI del contratto](#get-contract-abi). |
    | **Contract bytecode** (Bytecode del contratto) | Il bytecode del contratto intelligente compilato. Come [ottenere il bytecode del contratto](#get-contract-bytecode). |
    | **Smart contract address** (Indirizzo contratto intelligente) | L'indirizzo del contratto è l'indirizzo di destinazione del contratto intelligente nella blockchain Ethereum. Come [ottenere l'indirizzo del contratto](#get-contract-address). |
    | **Smart contract function name** (Nome funzione contratto intelligente) | Scegliere il nome della funzione di contratto intelligente per l'azione. L'elenco viene popolato con i dettagli presenti nell'interfaccia ABI del contratto. |

    Dopo aver scelto un nome per la funzione di contratto intelligente, potrebbero essere visualizzati i campi obbligatori per i parametri di funzione. Specificare i valori o il contenuto dinamico necessari per lo scenario in uso.

A questo punto, è possibile usare l'app per la logica. Quando viene attivato l'evento dell'app per la logica, viene eseguita l'azione della blockchain Ethereum. Ad esempio un trigger di richiesta HTTP esegue un'azione della blockchain Ethereum per eseguire una query e ottenere il valore dello stato del contratto intelligente corrispondente in una risposta HTTP che restituisce il valore.

## <a name="generate-a-workflow"></a>Generare un flusso di lavoro

L'estensione di Visual Studio Code Azure Blockchain Development Kit per Ethereum può generare flussi di lavoro di app per la logica per gli scenari comuni. Sono disponibili quattro scenari:

* Pubblicazione dei dati in un database SQL di Azure
* Pubblicazione di eventi in una Griglia di eventi di Azure o nel bus di servizio di Azure
* Pubblicazione di report
* Microservizio basato su REST

 Azure Blockchain Development Kit usa Truffle per semplificare lo sviluppo di blockchain. Per generare un'app per la logica basata su un contratto intelligente, è necessaria una soluzione Truffle per il contratto intelligente. Occorre inoltre una connessione alla rete del consorzio del servizio Azure Blockchain. Per altre informazioni, vedere [Avvio rapido: Usare Visual Studio Code per connettersi alla rete di un consorzio del servizio Azure Blockchain](connect-vscode.md).

Ad esempio, la procedura seguente genera un'app per la logica di microservizio basato su REST secondo il contratto intelligente **HelloBlockchain** dell'avvio rapido.

1. Nella barra laterale di Esplora risorse di Visual Studio Code espandere la cartella **contracts** nel progetto.
1. Fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Generate microservices for smart contracts** (Genera microservizi per contratti intelligenti) dal menu.

    ![Generare un'app per la logica](./media/ethereum-logic-app/generate-logic-app.png)

1. Nel riquadro comandi scegliere **App per la logica**.
1. Immettere **l'indirizzo del contratto**. Per altre informazioni, vedere [come ottenere l'indirizzo del contratto](#get-contract-address).
1. Scegliere la sottoscrizione di Azure e il gruppo di risorse per l'app per la logica.

    I file di configurazione e del codice dell'app per la logica vengono generati nella directory **generatedLogicApp**.

1. Visualizzare la directory **generatedLogicApp/HelloBlockchain**. È presente un file JSON dell'app per la logica per ogni funzione, evento e proprietà di contratto intelligente.
1. Aprire il file **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json** e copiare il contenuto.

    ![JSON per la proprietà RequestMessage](./media/ethereum-logic-app/requestmessage.png)

1. Nell'app per la logica selezionare **Visualizzazione codice app per la logica**. Sostituire il codice JSON esistente con il codice JSON dell'app per la logica generato.

    ![Sostituire la configurazione dell'app per la logica nella Visualizzazione codice.](./media/ethereum-logic-app/code-view.png)

1. Selezionare **Progettazione** per passare alla visualizzazione Progettazione.
1. L'app per la logica include i passaggi di base per lo scenario. È tuttavia necessario aggiornare i dettagli della configurazione per il connettore blockchain per Ethereum.
1. Selezionare il passaggio [Connessioni](#create-an-api-connection) e modificare o **creare una connessione API** al servizio Azure Blockchain.

    ![App per la logica microservizio](./media/ethereum-logic-app/microservice-logic-app.png)

1. A questo punto, è possibile usare l'app per la logica. Per testare il microservizio basato su REST, inviare una richiesta POST HTTP all'URL di richiesta dell'app per la logica. Copiare l'**URL POST HTTP** dal passaggio **Alla ricezione di una richiesta HTTP**.

    ![URL POST HTTP](./media/ethereum-logic-app/post-url.png)

1. Usare il comando cURL per creare una richiesta HTTP POST. Sostituire il testo segnaposto in **\<URL HTTP POST\>** con l'URL fornito nel passaggio precedente.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Il comando cURL restituisce una risposta dall'app per la logica. In questo caso, l'output dalla funzione di contratto intelligente **RequestMessage**

    ![Output della proprietà RequestMessage](./media/ethereum-logic-app/curl.png)

Per altre informazioni su come usare il kit di sviluppo, vedere la pagina [wiki di Azure Blockchain Development Kit per Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Creare una connessione API

Il connettore blockchain per Ethereum richiede una connessione API a una blockchain. È possibile usare il connettore API per più app per la logica. Alcune proprietà sono obbligatorie e altre dipendono dallo scenario in uso.

> [!IMPORTANT]
> Per creare transazioni in una blockchain sono necessari una chiave privata o un indirizzo dell'account e una password. È necessaria solo una forma di autenticazione. Non occorre specificare sia la chiave privata che i dettagli dell'account. L'esecuzione di query sui contratti non richiede una transazione. Se si usano azioni che eseguono query sullo stato del contratto, la chiave privata o l'indirizzo dell'account e la password non sono necessari.

Per configurare una connessione a un membro del servizio Azure Blockchain è disponibile l'elenco seguente contenente le proprietà necessarie a seconda dello scenario.

| Proprietà | DESCRIZIONE |
|----------|-------------|
|**Nome connessione** | Nome della connessione API. Richiesto. |
|**Ethereum RPC endpoint** (Endpoint RPC Ethereum) | Indirizzo HTTP del nodo della transazione del servizio Azure Blockchain. Richiesto. Come [ottenere l'endpoint RPC](#get-rpc-endpoint). |
|**Chiave privata** | Chiave privata dell'account Ethereum. Per le transazioni sono necessari la chiave privata o l'indirizzo dell'account e la password. Come [ottenere la chiave privata](#get-private-key). |
|**Account address** | Indirizzo dell'account del membro del servizio Azure Blockchain. Per le transazioni sono necessari la chiave privata o l'indirizzo dell'account e la password. Come [ottenere l'indirizzo dell'account](#get-account-address). |
|**Password account** | La password dell'account viene impostata durante la creazione del membro. Per informazioni su come reimpostare la password, vedere [Account Ethereum](consortium.md#ethereum-account).|

## <a name="get-rpc-endpoint"></a>Ottenere l'endpoint RPC

L'indirizzo dell'endpoint RPC del servizio Azure Blockchain è necessario per connettersi a una rete blockchain. È possibile ottenere l'indirizzo dell'endpoint tramite Azure Blockchain Development Kit per Ethereum o il portale di Azure.

**Con il kit di sviluppo:**

1. Nella sezione **Servizio Azure Blockchain** in Visual Studio Code fare clic con il pulsante destro del mouse sul consorzio e
1. scegliere **Copy RPC endpoint** (Copia endpoint RPC).

    ![Copia endpoint RPC](./media/ethereum-logic-app/devkit-rpc.png)

    L'endpoint RPC viene copiato negli Appunti.

**Con il portale di Azure:**

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure Blockchain. Selezionare **Transaction nodes** (Nodi transazione) e il collegamento al nodo della transazione predefinito.

    ![Selezionare il nodo transazione predefinito](./media/ethereum-logic-app/transaction-nodes.png)

1. Selezionare **Connection strings > Access keys** (Stringhe di connessione > Chiavi di accesso).
1. Copiare l'indirizzo endpoint da **HTTPS (chiave di accesso 1)** o dalla chiave di accesso 2.

    ![Stringa di connessione](./media/ethereum-logic-app/connection-string.png)

    L'endpoint RPC è l'URL HTTPS che include l'indirizzo e la chiave di accesso del nodo della transazione del membro del servizio Azure Blockchain.

## <a name="get-private-key"></a>Ottenere la chiave privata

È possibile usare la chiave privata dell'account Ethereum per eseguire l'autenticazione quando si invia una transazione alla blockchain. Le chiavi pubbliche e private dell'account Ethereum vengono generate da un promemoria di 12 parole. Azure Blockchain Development Kit per Ethereum genera un promemoria quando viene stabilita la connessione a un membro del consorzio del servizio Azure Blockchain. È possibile ottenere l'indirizzo dell'endpoint con l'estensione kit di sviluppo.

1. In Visual Studio Code aprire il riquadro comandi (F1).
1. Scegliere **Azure Blockchain: Retrieve private key** (Recupera chiave privata).
1. Selezionare il promemoria salvato durante la connessione al membro del consorzio.

    ![Selezione del promemoria](./media/ethereum-logic-app/private-key.png)

    La chiave privata viene copiata negli Appunti.

## <a name="get-account-address"></a>Ottenere l'indirizzo dell'account

È possibile usare l'account e la password del membro per eseguire l'autenticazione quando si invia una transazione alla blockchain. La password viene impostata durante la creazione del membro.

1. Nel portale di Azure passare alla pagina di panoramica del servizio Azure Blockchain.
1. Copiare l'indirizzo dell'account membro.

    ![Copia dell'account membro](./media/ethereum-logic-app/member-account.png)

Per altre informazioni sull'indirizzo e sulla password dell'account, vedere [Account Ethereum](consortium.md#ethereum-account).

## <a name="get-contract-abi"></a>Ottenere l'interfaccia ABI del contratto

L'interfaccia ABI (Application Binary Interface) del contratto definisce le interfacce del contratto intelligente. Descrive come interagire con il contratto intelligente. È possibile ottenere l'interfaccia ABI del contratto tramite Azure Blockchain Development Kit per Ethereum o dal file dei metadati del contratto del compilatore Solidity.

**Con il kit di sviluppo:**

Se per creare il contratto intelligente è stato usato il kit di sviluppo o Truffle, è possibile usare l'estensione per copiare l'interfaccia ABI del contratto negli Appunti.

1. Nel riquadro di Esplora risorse di Visual Studio Code espandere la cartella **build/contracts** del progetto Solidity.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Scegliere **Copy contract ABI** (Copia interfaccia ABI contratto).

    ![Copia dell'interfaccia ABI del contratto con il kit di sviluppo](./media/ethereum-logic-app/abi-devkit.png)

    L'interfaccia ABI del contratto viene copiata negli Appunti.

**Con il file di metadati del contratto:**

1. Aprire il file di metadati del contratto disponibile nella cartella **build/contracts** del progetto Solidity. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Individuare la sezione **abi** nel file JSON.
1. Copiare la matrice JSON **abi**.

    ![Sezione ABI contratto nei metadati](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>Ottenere il bytecode del contratto

Il bytecode del contratto è il contratto intelligente compilato eseguito dalla macchina virtuale Ethereum. È possibile ottenere il bytecode del contratto tramite Azure Blockchain Development Kit per Ethereum o dal compilatore Solidity.

**Con il kit di sviluppo:**

Se per creare il contratto intelligente è stato usato il kit di sviluppo o Truffle, è possibile usare l'estensione per copiare il bytecode del contratto negli Appunti.

1. Nel riquadro di Esplora risorse di Visual Studio Code espandere la cartella **build/contracts** del progetto Solidity.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Scegliere **Copy contract bytecode** (Copia bytecode contratto).

    ![Copia del bytecode del contratto con il kit di sviluppo](./media/ethereum-logic-app/bytecode-devkit.png)

    Il bytecode del contratto viene copiato negli Appunti.

**Con il file di metadati del contratto:**

1. Aprire il file di metadati del contratto disponibile nella cartella **build/contracts** del progetto Solidity. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Individuare l'elemento **bytecode** nel file JSON.
1. Copiare il valore dell'elemento **bytecode**.

    ![Copia del bytecode con i metadati](./media/ethereum-logic-app/bytecode-metadata.png)

**Con il compilatore Solidity:**

Usare il comando `solc --bin <smart contract>.sol` per generare il bytecode del contratto.

## <a name="get-contract-address"></a>Ottenere l'indirizzo del contratto

L'indirizzo del contratto è l'indirizzo di destinazione del contratto intelligente nella blockchain Ethereum. Questo indirizzo consente di inviare una transazione o eseguire una query sullo stato di un contratto intelligente. È possibile ottenere l'indirizzo del contratto dall'output della migrazione Truffle o dal file di metadati del contratto.

**Con l'output della migrazione Truffle:**

Truffle visualizza l'indirizzo del contratto dopo la distribuzione del contratto intelligente. Copiare l'**indirizzo del contratto** dall'output.

![Indirizzo del contratto dall'output di Truffle](./media/ethereum-logic-app/contract-address-truffle.png)

**Con il file di metadati del contratto:**

1. Aprire il file di metadati del contratto disponibile nella cartella **build/contracts** del progetto Solidity. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Individuare la sezione **networks** nel file JSON.
1. Le reti private sono identificate tramite un ID di rete intero. Individuare il valore dell'indirizzo all'interno della sezione network.
1. Copiare il valore di **address**.

![Indirizzo del contratto dai metadati](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Passaggi successivi

Guardare gli [Scenari comuni di connessione di blockchain con App per la logica di Azure](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
