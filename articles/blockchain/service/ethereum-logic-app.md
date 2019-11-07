---
title: Usare il connettore Ethereum blockchain con app per la logica di Azure
description: Usare il connettore Ethereum blockchain con le app per la logica di Azure per attivare le funzioni Smart contract e rispondere agli eventi del contratto Smart.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: bb23d6b9b42e1c51646765255870a14a1b5d39f7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579933"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Usare il connettore Ethereum blockchain con app per la logica di Azure

Usare il [connettore blockchain di Ethereum](https://docs.microsoft.com/connectors/blockchainethereum/) con app per la [logica di Azure](https://docs.microsoft.com/azure/logic-apps/) per eseguire azioni smart contract e rispondere agli eventi del contratto Smart. Si immagini, ad esempio, di voler creare un microservizio basato su REST che restituisce informazioni da un Ledger blockchain. Usando un'app per la logica, è possibile accettare richieste HTTP che eseguono query sulle informazioni archiviate in un Ledger blockchain.

## <a name="prerequisites"></a>Prerequisiti

Completare la Guida introduttiva al prerequisito facoltativo [: usare Visual Studio Code per connettersi a una rete di Azure blockchain Service Consortium](connect-vscode.md). La Guida introduttiva illustra [come installare Azure blockchain Development Kit per Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e configurare l'ambiente di sviluppo di blockchain.

## <a name="create-a-logic-app"></a>Creare un'app per la logica

App per la logica di Azure consente di pianificare e automatizzare i processi e i flussi di lavoro di business quando è necessario integrare sistemi e servizi. In primo luogo, si crea una logica che usa il connettore Ethereum blockchain.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.
1. In **Crea app**per la logica specificare i dettagli sulla posizione in cui creare l'app per la logica. Al termine, selezionare **Crea**.

    Per altre informazioni sulla creazione di app per la logica, vedere [creare flussi di lavoro automatizzati con app per la logica di Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Quando Azure distribuisce l'app, selezionare la risorsa dell'app per la logica.
1. Nella finestra di progettazione di app per la logica, in **modelli**, selezionare app per la **logica vuota**.

Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica che si avvia ed esegue il flusso di lavoro.

Il connettore Ethereum blockchain include un trigger e diverse azioni. Il trigger o l'azione da usare dipende dallo scenario.

Se il flusso di lavoro:

* Viene attivato quando si verifica un evento in blockchain, [utilizzare il trigger di evento](#use-the-event-trigger).
* Esegue una query o distribuisce un contratto intelligente, [utilizzare le azioni](#use-actions).
* Segue uno scenario comune, che [genera un flusso di lavoro tramite il kit per sviluppatori](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Usare il trigger di evento

Usare i trigger di evento blockchain di Ethereum quando si vuole che un'app per la logica venga eseguita dopo un evento di Smart Contract. Si desidera, ad esempio, inviare un messaggio di posta elettronica quando viene chiamata una funzione di Smart Contract.

1. Nella finestra di progettazione di app per la logica selezionare il connettore Ethereum blockchain.
1. Nella scheda **trigger** selezionare **quando si verifica un evento di Smart Contract**.
1. Modificare o [creare una connessione API](#create-an-api-connection) al servizio Azure blockchain.
1. Immettere i dettagli relativi al contratto intelligente di cui si desidera verificare la presenza di eventi.

    ![Progettazione app per la logica con proprietà trigger evento](./media/ethereum-logic-app/event-properties.png)

    | Proprietà | Descrizione |
    |----------|-------------|
    | **ABI contratto** | L'interfaccia ABI (Contract Application Binary Interface) definisce le interfacce smart Contract. Per altre informazioni, vedere [ottenere l'ABI del contratto](#get-the-contract-abi). |
    | **Indirizzo del contratto Smart** | L'indirizzo del contratto è l'indirizzo di destinazione del contratto Smart in Ethereum blockchain. Per ulteriori informazioni, vedere [ottenere l'indirizzo del contratto](#get-the-contract-address). |
    | **Nome evento** | Selezionare un evento di contratto intelligente da verificare. L'evento attiva l'app per la logica. |
    | **Intervallo** e **frequenza** | Selezionare la frequenza con cui si desidera controllare l'evento. |

1. Selezionare **Salva**.

Per completare l'app per la logica, è possibile aggiungere un nuovo passaggio che esegue un'azione in base al trigger di evento blockchain di Ethereum. Ad esempio, inviare un messaggio di posta elettronica.

## <a name="use-actions"></a>Usare le azioni

Usare le azioni blockchain di Ethereum quando si vuole che un'app per la logica esegua un'azione sul Ledger blockchain. Ad esempio, si vuole creare un microservizio basato su REST che chiama una funzione del contratto intelligente quando viene effettuata una richiesta HTTP a un'app per la logica.

Le azioni del connettore richiedono un trigger. È possibile usare un'azione del connettore Ethereum blockchain come passaggio successivo dopo un trigger, ad esempio un trigger di richiesta HTTP per un microservizio.

1. Nella finestra di progettazione di app per la logica selezionare **nuovo passaggio** dopo un trigger.
1. Selezionare il connettore Ethereum blockchain.
1. Nella scheda **azioni** selezionare una delle azioni disponibili.

    ![Finestra di progettazione di app per la logica con proprietà Actions](./media/ethereum-logic-app/action-properties.png)

1. Modificare o [creare una connessione API](#create-an-api-connection) al servizio Azure blockchain.
1. A seconda dell'azione scelta, fornire i dettagli seguenti sulla funzione del contratto Smart.

    | Proprietà | Descrizione |
    |----------|-------------|
    | **ABI contratto** | Il contratto ABI definisce le interfacce smart Contract. Per altre informazioni, vedere [ottenere l'ABI del contratto](#get-the-contract-abi). |
    | **Bytecode del contratto** | Bytecode di Smart Contract compilato. Per altre informazioni, vedere [ottenere il bytecode del contratto](#get-the-contract-bytecode). |
    | **Indirizzo del contratto Smart** | L'indirizzo del contratto è l'indirizzo di destinazione del contratto Smart in Ethereum blockchain. Per ulteriori informazioni, vedere [ottenere l'indirizzo del contratto](#get-the-contract-address). |
    | **Nome della funzione di contratto intelligente** | Selezionare il nome della funzione del contratto Smart per l'azione. L'elenco viene popolato dai dettagli nell'ABI del contratto. |

    Dopo aver selezionato un nome di funzione del contratto intelligente, è possibile che vengano visualizzati i campi obbligatori per i parametri della funzione. Immettere i valori o il contenuto dinamico necessario per lo scenario.

È ora possibile usare l'app per la logica. Quando viene attivato l'evento dell'app per la logica, viene eseguita l'azione Ethereum blockchain. Un trigger di richiesta HTTP, ad esempio, esegue un'azione blockchain Ethereum per eseguire una query su un valore dello stato del contratto Smart. Questa query genera una risposta HTTP che restituisce il valore.

## <a name="generate-a-workflow"></a>Generare un flusso di lavoro

Il kit di sviluppo di Azure blockchain per Ethereum Visual Studio Code estensione può generare flussi di lavoro di app per la logica per scenari comuni. Sono disponibili quattro scenari:

* Pubblicazione di dati in un'istanza del database SQL di Azure
* Pubblicazione di eventi in un'istanza di griglia di eventi di Azure o del bus di servizio di Azure
* Pubblicazione di report
* Microservizio basato su REST

 Azure blockchain Development Kit usa il tartufo per semplificare lo sviluppo di blockchain. Per generare un'app per la logica basata su un contratto intelligente, è necessaria una soluzione di tartufo per il contratto Smart. È necessaria anche una connessione alla rete di Azure blockchain Service Consortium. Per altre informazioni, vedere [usare Visual Studio Code per connettersi a una guida introduttiva alla rete di Azure blockchain Service Consortium](connect-vscode.md).

Ad esempio, la procedura seguente genera un'app per la logica di microservizio basata su REST basata sul contratto Smart **HelloBlockchain** di avvio rapido:

1. Nella barra laterale di Visual Studio Code Explorer espandere la cartella **contratti** nella soluzione.
1. Fare clic con il pulsante destro del mouse su **HelloBlockchain. Sol** e selezionare **genera Microservizi per Smart Contract** dal menu.

    ![Riquadro Visual Studio Code con la selezione genera microservizi per contratti intelligenti](./media/ethereum-logic-app/generate-logic-app.png)

1. Nel riquadro comandi selezionare app per la **logica**.
1. Immettere l' **indirizzo del contratto**. Per ulteriori informazioni, vedere [ottenere l'indirizzo del contratto](#get-the-contract-address).
1. Selezionare la sottoscrizione di Azure e il gruppo di risorse per l'app per la logica.

    I file di codice e di configurazione dell'app per la logica vengono generati nella directory **generatedLogicApp**

1. Visualizzare la directory **generatedLogicApp/HelloBlockchain** . È disponibile un file JSON dell'app per la logica per ogni funzione, evento e proprietà del contratto Smart.
1. Aprire il **generatedLogicApp/HelloBlockchain/servizio/proprietà. File RequestMessage. logicapp. JSON** e copiare il contenuto.

    ![File JSON con codice da copiare](./media/ethereum-logic-app/requestmessage.png)

1. Nell'app per la logica selezionare **visualizzazione codice app**per la logica. Sostituire il codice JSON esistente con l'app per la logica JSON generata.

    ![Visualizzazione codice app per la logica con nuovo codice app sostituito](./media/ethereum-logic-app/code-view.png)

1. Selezionare **finestra di progettazione** per passare alla visualizzazione di progettazione.
1. L'app per la logica include i passaggi di base per lo scenario. Tuttavia, è necessario aggiornare i dettagli di configurazione per il connettore Ethereum blockchain.
1. Selezionare il passaggio **Connections (connessioni** ) e modificare o [creare una connessione API](#create-an-api-connection) al servizio Azure blockchain.

    ![Visualizzazione progettazione con la selezione connessioni](./media/ethereum-logic-app/microservice-logic-app.png)

1. È ora possibile usare l'app per la logica. Per testare il microservizio basato su REST, inviare una richiesta HTTP POST all'URL della richiesta dell'app per la logica. Copiare il contenuto dell' **URL post http** dal passaggio **quando viene ricevuta una richiesta http** .

    ![Riquadro Progettazione app per la logica con URL POST HTTP](./media/ethereum-logic-app/post-url.png)

1. Usare cURL per creare una richiesta HTTP POST. Sostituire il testo segnaposto *\<URL http POST\>* con l'URL del passaggio precedente.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Il comando cURL restituisce una risposta dall'app per la logica. In questo caso, la risposta è l'output della funzione **RequestMessage** Smart Contract.

    ![Output del codice dalla funzione RequestMessage Smart Contract](./media/ethereum-logic-app/curl.png)

Per altre informazioni sull'uso del kit di sviluppo, vedere la [pagina wiki di Azure blockchain Development Kit per Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Creare una connessione API

Per il connettore blockchain di Ethereum è necessaria una connessione API a un blockchain. È possibile usare il connettore API per più app per la logica. Alcune proprietà sono obbligatorie e altre dipendono dallo scenario.

> [!IMPORTANT]
> Per la creazione di transazioni in un blockchain sono necessarie una chiave privata o un indirizzo di account e una password. È necessaria solo una forma di autenticazione. Non è necessario specificare la chiave privata e i dettagli dell'account. Per eseguire query sui contratti non è necessaria una transazione. Se si usano azioni che eseguono query sullo stato del contratto, la chiave privata o l'indirizzo dell'account e la password non sono obbligatori.

Per semplificare la configurazione di una connessione a un membro del servizio blockchain di Azure, l'elenco seguente contiene le possibili proprietà che potrebbero essere necessarie a seconda dello scenario.

| Proprietà | Descrizione |
|----------|-------------|
|**Nome connessione** | Nome della connessione API. Obbligatorio. |
|**Endpoint RPC Ethereum** | Indirizzo HTTP del nodo di transazione del servizio blockchain di Azure. Obbligatorio. Per ulteriori informazioni, vedere [Get the RPC endpoint](#get-the-rpc-endpoint). |
|**Chiave privata** | Chiave privata dell'account Ethereum. Per le transazioni sono necessarie la chiave privata o l'indirizzo dell'account e la password. Per ulteriori informazioni, vedere [ottenere la chiave privata](#get-the-private-key). |
|**Account address** | Indirizzo dell'account membro del servizio Azure blockchain. Per le transazioni sono necessarie la chiave privata o l'indirizzo dell'account e la password. Per ulteriori informazioni, vedere [ottenere l'indirizzo dell'account](#get-the-account-address). |
|**Password account** | La password dell'account viene impostata quando si crea il membro. Per informazioni sulla reimpostazione della password, vedere [account Ethereum](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Ottenere l'endpoint RPC

Per la connessione a una rete blockchain è necessario l'indirizzo endpoint RPC del servizio blockchain di Azure. È possibile ottenere l'indirizzo endpoint usando Azure blockchain Development Kit per Ethereum o il portale di Azure.

**Per utilizzare il kit di sviluppo:**

1. Nel **servizio blockchain di Azure** , in Visual Studio Code, fare clic con il pulsante destro del mouse sul Consorzio.
1. Selezionare **Copia indirizzo endpoint RPC**.

    ![Riquadro Visual Studio Code che mostra il consorzio con la selezione dell'indirizzo dell'endpoint di copia RPC](./media/ethereum-logic-app/devkit-rpc.png)

    L'endpoint RPC viene copiato negli Appunti.

**Per usare la portale di Azure:**

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure blockchain. Selezionare **Transaction nodes** (Nodi transazione) e il collegamento al nodo della transazione predefinito.

    ![Pagina nodi transazione con selezione (nodo predefinito)](./media/ethereum-logic-app/transaction-nodes.png)

1. Selezionare le **stringhe di connessione** > chiavi di **accesso**.
1. Copiare l'indirizzo endpoint da **https (chiave di accesso 1)** o **https (chiave di accesso 2)** .

    ![portale di Azure con le chiavi di accesso della stringa di connessione](./media/ethereum-logic-app/connection-string.png)

    L'endpoint RPC è l'URL HTTPS, che include l'indirizzo e la chiave di accesso del nodo di transazione membro del servizio blockchain di Azure.

## <a name="get-the-private-key"></a>Ottenere la chiave privata

È possibile usare la chiave privata dell'account Ethereum per eseguire l'autenticazione quando si invia una transazione al blockchain. Le chiavi pubbliche e private dell'account Ethereum vengono generate da un tasto di scelta di 12 parole. Azure blockchain Development Kit per Ethereum genera un tasto di scelta quando ci si connette a un membro del Consorzio di servizi di Azure blockchain. È possibile ottenere l'indirizzo dell'endpoint utilizzando l'estensione del kit di sviluppo.

1. In Visual Studio Code aprire il riquadro comandi (F1).
1. Selezionare **Azure blockchain: Recupera chiave privata**.
1. Selezionare il tasto di scelta rapida salvato durante la connessione al membro del Consorzio.

    ![Riquadro comandi con un'opzione per selezionare il tasto di scelta](./media/ethereum-logic-app/private-key.png)

    La chiave privata viene copiata negli Appunti.

## <a name="get-the-account-address"></a>Ottenere l'indirizzo dell'account

È possibile usare l'account e la password del membro per l'autenticazione quando si invia una transazione a blockchain. La password viene impostata quando si crea il membro.

1. Nella portale di Azure andare alla pagina di panoramica del servizio Azure blockchain.
1. Copiare l'indirizzo dell' **account del membro** .

    ![Pagina panoramica con l'indirizzo dell'account membro](./media/ethereum-logic-app/member-account.png)

Per ulteriori informazioni sull'indirizzo e la password dell'account, vedere l' [account Ethereum](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Ottenere l'ABI del contratto

Il contratto ABI definisce le interfacce smart Contract. Viene descritto come interagire con il contratto Smart. È possibile ottenere l'ABI del contratto usando Azure blockchain Development Kit per Ethereum. È anche possibile ottenerlo dal file di metadati del contratto creato dal compilatore di solidità.

**Per utilizzare il kit di sviluppo:**

Se è stato usato il kit di sviluppo o il tartufo per creare il contratto intelligente, è possibile usare l'estensione per copiare l'ABI del contratto negli Appunti.

1. Nel riquadro Visual Studio Code Explorer espandere la cartella **Build/Contracts** del progetto di solidità.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **JSON** .
1. Selezionare **copia contratto Abi**.

    ![Riquadro Visual Studio Code con la selezione ABI del contratto di copia](./media/ethereum-logic-app/abi-devkit.png)

    L'interfaccia ABI del contratto viene copiata negli Appunti.

**Per utilizzare il file di metadati del contratto:**

1. Aprire il file di metadati del contratto contenuto nella cartella **Build/Contracts** del progetto di solidità. Il nome del file è il nome del contratto intelligente seguito dall'estensione **JSON** .
1. Trovare la sezione **Abi** nel file JSON.
1. Copiare la matrice JSON **Abi** .

    ![Codice ABI nel file di metadati del contratto](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Ottenere il bytecode del contratto

Il bytecode del contratto è la Smart Contract compilata eseguita dalla macchina virtuale Ethereum. È possibile ottenere il bytecode del contratto usando Azure blockchain Development Kit per Ethereum. È anche possibile ottenerlo dal compilatore di solidità.

**Per utilizzare il kit di sviluppo:**

Se è stato usato il kit di sviluppo o il tartufo per creare il contratto intelligente, è possibile usare l'estensione per copiare il bytecode del contratto negli Appunti.

1. Nel riquadro Visual Studio Code Explorer espandere la cartella **Build/Contracts** del progetto di solidità.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **JSON** .
1. Selezionare **copia bytecode del contratto**.

    ![Riquadro Visual Studio Code con la selezione del bytecode del contratto di copia](./media/ethereum-logic-app/bytecode-devkit.png)

    Il bytecode del contratto viene copiato negli Appunti.

**Per utilizzare il file di metadati del contratto:**

1. Aprire il file di metadati del contratto contenuto nella cartella **Build/Contracts** del progetto di solidità. Il nome del file è il nome del contratto intelligente seguito dall'estensione **JSON** .
1. Trovare l'elemento **bytecode** nel file JSON.
1. Copiare il valore **bytecode** .

    ![Riquadro Visual Studio Code con bytecode nei metadati](./media/ethereum-logic-app/bytecode-metadata.png)

**Per usare il compilatore di solidità:**

Usare il comando `solc --bin <smart contract>.sol` per generare il bytecode del contratto.

## <a name="get-the-contract-address"></a>Ottenere l'indirizzo del contratto

L'indirizzo del contratto è l'indirizzo di destinazione del contratto Smart in Ethereum blockchain. Utilizzare questo indirizzo per inviare uno stato di transazione o di query di un contratto intelligente. È possibile ottenere l'indirizzo del contratto dall'output della migrazione del tartufo o dal file di metadati del contratto.

**Per usare l'output della migrazione di tartufo:**

Tartufo Visualizza l'indirizzo del contratto dopo la distribuzione del contratto Smart. Copiare l' **indirizzo del contratto** dall'output.

![Output della migrazione del tartufo con l'indirizzo del contratto in Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Per utilizzare il file di metadati del contratto:**

1. Aprire il file di metadati del contratto contenuto nella cartella **Build/Contracts** del progetto di solidità. Il nome del file è il nome del contratto intelligente seguito dall'estensione **JSON** .
1. Trovare la sezione **reti** nel file JSON.
1. Le reti private sono identificate da un ID di rete Integer. Individuare il valore dell'indirizzo all'interno della sezione rete.
1. Copiare il valore dell' **Indirizzo** .

![Metadati con il valore dell'indirizzo in Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Passaggi successivi

Guarda gli scenari comuni nel video [con le app per la logica](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
