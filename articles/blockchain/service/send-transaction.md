---
title: 'Esercitazione: Creare, compilare e distribuire contratti intelligenti - Servizio Azure Blockchain'
description: Esercitazione sull'utilizzo dell'estensione Azure Blockchain Development Kit per Ethereum in Visual Studio Code per creare, compilare e distribuire un contratto intelligente con il servizio Azure Blockchain.
ms.date: 12/06/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 5b901ab904425a22d2fe9643ffa75a4e978efa88
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972861"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Esercitazione: Creare, compilare e distribuire contratti intelligenti in Servizio Azure Blockchain

In questa esercitazione si usa l'estensione Azure Blockchain Development Kit per Ethereum in Visual Studio Code per creare, compilare e distribuire un contratto intelligente con il servizio Azure Blockchain. Il kit di sviluppo si usa anche per eseguire una funzione del contratto intelligente tramite una transazione.

È possibile usare Azure Blockchain Development Kit per Ethereum per:

> [!div class="checklist"]
> * Creare un contratto intelligente
> * Distribuire un contratto intelligente
> * Eseguire una funzione del contratto intelligente tramite una transazione

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare [Avvio rapido: Usare Visual Studio Code per connettersi alla rete di un consorzio del servizio Azure Blockchain](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Estensione Azure Blockchain Development Kit per Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x o versione successiva](https://nodejs.org/download)
* [Git 2.10.x o versione successiva](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Aggiungere python.exe al percorso. Python versione 2.7.15 nel percorso è necessario per Azure Blockchain Development Kit.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Interfaccia della riga di comando Ganache 6.0.0](https://github.com/trufflesuite/ganache-cli)

In Windows è necessario un compilatore C++ installato per il modulo node-gyp. È possibile usare gli strumenti MSBuild:

* Se è installato Visual Studio 2017, configurare npm per l'uso degli strumenti MSBuild con il comando `npm config set msvs_version 2017 -g`
* Se è installato Visual Studio 2019, impostare il percorso degli strumenti MSBuild per npm. Ad esempio: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* In caso contrario, installare gli strumenti autonomi di compilazione di Visual Studio usando `npm install --global windows-build-tools` in una shell di comandi con privilegi elevati *Esegui come amministratore*.

Per altre informazioni su node-gyp, vedere il [repository node-gyp in GitHub](https://github.com/node-gyp).

## <a name="create-a-smart-contract"></a>Creare un contratto intelligente

Azure Blockchain Development Kit per Ethereum usa i modelli di progetto e gli strumenti di Truffle per semplificare lo scaffolding, la compilazione e la distribuzione dei contratti. Prima di iniziare, completare [Avvio rapido: Usare Visual Studio Code per connettersi alla rete di un consorzio del servizio Azure Blockchain](connect-vscode.md). Questa guida di avvio rapido illustra l'installazione e la configurazione di Azure Blockchain Development Kit per Ethereum.

1. Dal riquadro comandi di VS Code scegliere **Azure Blockchain: New Solidity Project** (Nuovo progetto Solidity).
1. Scegliere **Create basic project** (Crea progetto di base).
1. Creare una nuova cartella denominata `HelloBlockchain` e scegliere **Select new project path** (Selezionare nuovo percorso progetto).

Azure Blockchain Development Kit crea e inizializza automaticamente un nuovo progetto Solidity. Il progetto di base include un contratto intelligente di esempio denominato **HelloBlockchain** e tutti i file necessari per eseguire la compilazione e la distribuzione al membro del consorzio nel servizio Azure Blockchain. La creazione del progetto può richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento nel pannello del terminale di VS Code selezionando l'output per Azure Blockchain.

La struttura del progetto avrà un aspetto simile al seguente:

   ![Progetto Solidity](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Compilare un contratto intelligente

I contratti intelligenti si trovano nella directory **contracts** del progetto. È necessario compilare i contratti intelligenti prima della distribuzione a una blockchain. Usare il comando **Build Contracts** (Compila contratti) per compilare tutti i contratti intelligenti nel progetto.

1. Nella barra laterale di Explorer di VS Code espandere la cartella **contracts** nel progetto.
1. Fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Build Contracts** (Compila contratti) dal menu.

    ![Scegliere il menu Build contracts (Compila contratti) ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit usa Truffle per compilare i contratti intelligenti.

![Output del compilatore Truffle](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Distribuire un contratto intelligente

Truffle usa gli script di migrazione per distribuire i contratti a una rete Ethereum. Le migrazioni sono file JavaScript che si trovano nella directory **migrations** del progetto.

1. Per distribuire un contratto intelligente, fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Deploy Contracts** (Distribuisci contratti) dal menu.
1. Scegliere la rete per consorzi Azure Blockchain nel riquadro comandi. La rete blockchain per consorzi è stata aggiunta al file di configurazione Truffle del progetto al momento della creazione del progetto.
1. Scegliere **Generate mnemonic** (Genera tasto di scelta). Scegliere un nome file e salvare il file dei tasti di scelta nella cartella del progetto. Ad esempio: `myblockchainmember.env`. Il file dei tasti di scelta viene usato per generare una chiave privata Ethereum per il membro della blockchain.

Azure Blockchain Development Kit usa Truffle per eseguire lo script di migrazione per distribuire i contratti alla blockchain.

![Distribuzione del contratto riuscita](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Chiamare una funzione del contratto

La funzione **SendRequest** del contratto **HelloBlockchain** modifica la variabile di stato **RequestMessage**. La modifica dello stato di una rete blockchain viene eseguita tramite una transazione. È possibile usare la pagina di interazione con il contratto intelligente del kit di sviluppo di Azure Blockchain per chiamare la funzione **SendRequest** tramite una transazione.

1. Per interagire con il contratto intelligente, fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Show Smart Contract Interaction Page** (Mostra la pagina di interazione con il contratto intelligente) dal menu.

    ![Scegliere Show Smart Contract Interaction Page dal menu](./media/send-transaction/contract-interaction.png)

1. La pagina di interazione consente di scegliere una versione del contratto distribuita, chiamare le funzioni, visualizzare lo stato corrente e visualizzare i metadati.

    ![Esempio di pagina di interazione con il contratto intelligente](./media/send-transaction/interaction-page.png)

1. Per chiamare la funzione del contratto intelligente, selezionare l'azione del contratto e passare gli argomenti. Scegliere l'azione del contratto **SendRequest** e immettere **Hello, Blockchain!** per il parametro **requestMessage**. Selezionare **Execute** (Esegui) per chiamare la funzione **SendRequest** tramite una transazione.

    ![Eseguire l'azione SendRequest](./media/send-transaction/sendrequest-action.png)

Una volta elaborata la transazione, la sezione dell'interazione riflette le modifiche dello stato.

![Modifiche dello stato del contratto](./media/send-transaction/contract-state.png)

La funzione SendRequest imposta i campi **RequestMessage** e **State**. Lo stato corrente per **RequestMessage** è l'argomento **Hello, Blockchain** passato. Il valore del campo **State** rimane **Request**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse `myResourceGroup` creato nell'avvio rapido *Creare un membro della blockchain* eseguito come parte dei prerequisiti.

Per eliminare il gruppo di risorse:

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare.
1. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un progetto Solidity di esempio usando Azure Blockchain Development Kit. È stato compilato e distribuito un contratto intelligente e quindi è stata chiamata una funzione tramite una transazione in una rete blockchain per consorzi ospitata nel servizio Azure Blockchain.

> [!div class="nextstepaction"]
> [Sviluppo di applicazioni blockchain con il servizio Azure Blockchain](develop.md)
