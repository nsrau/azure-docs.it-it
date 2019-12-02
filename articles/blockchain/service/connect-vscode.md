---
title: Usare Visual Studio Code per connettersi al servizio Azure Blockchain
description: Connettersi alla rete di un consorzio del servizio Azure Blockchain usando l'estensione Azure Blockchain Development Kit per Ethereum in Visual Studio Code
ms.date: 11/19/2019
ms.topic: quickstart
ms.reviewer: chrisseg
ms.openlocfilehash: 4d4625df1cb07818dcadb88e2c98bf9ae1da1b1f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455992"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Guida introduttiva: Usare Visual Studio Code per connettersi alla rete di un consorzio del servizio Azure Blockchain

In questa guida di avvio rapido si installa e si usa l'estensione Azure Blockchain Development Kit per Ethereum di Visual Studio Code (VS Code) per connettersi a un consorzio nel servizio Azure Blockchain. Azure Blockchain Development Kit semplifica la creazione, la connessione, la compilazione e la distribuzione di contratti intelligenti nei libri mastro della blockchain Ethereum.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md) o [Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure](create-member-cli.md)
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

### <a name="verify-azure-blockchain-development-kit-environment"></a>Verificare l'ambiente di Azure Blockchain Development Kit

Azure Blockchain Development Kit verifica che siano stati soddisfatti i prerequisiti per l'ambiente di sviluppo. Per verificare l'ambiente di sviluppo:

Dal riquadro comandi di VS Code scegliere **Azure Blockchain: Mostra pagina iniziale**.

Azure Blockchain Development Kit esegue uno script di convalida che richiede circa un minuto per il completamento. È possibile visualizzare l'output selezionando **Terminale > Nuovo terminale**. Nella barra dei menu del terminale selezionare la scheda **Output** e **Azure Blockchain** nell'elenco a discesa. Una convalida con esito positivo ha un aspetto simile all'immagine seguente:

![Ambiente di sviluppo valido](./media/connect-vscode/valid-environment.png)

 Se non è presente uno strumento necessario, una nuova scheda denominata **Azure Blockchain Development Kit - Anteprima** elenca gli strumenti necessari con i collegamenti per il download.

![App necessarie per il kit di sviluppo](./media/connect-vscode/required-apps.png)

Installare gli eventuali prerequisiti mancanti prima di continuare con questa guida di avvio rapido.

## <a name="connect-to-consortium-member"></a>Connettersi a un membro del consorzio

È possibile connettersi ai membri del consorzio usando l'estensione di VS Code Azure Blockchain Development Kit. Una volta connessi a un consorzio, è possibile compilare e distribuire contratti intelligenti a un membro del consorzio del servizio Azure Blockchain.

Se non si ha accesso a un membro del consorzio del servizio Azure Blockchain, completare il prerequisito [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md) o [Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure](create-member-cli.md).

1. Nel riquadro Explorer di VS Code espandere l'estensione **Azure Blockchain**.
1. Selezionare **Connect to Consortium** (Connetti al consorzio).

   ![Connect to consortium (Connetti al consorzio)](./media/connect-vscode/connect-consortium.png)

    Se viene richiesta l'autenticazione di Azure, seguire le istruzioni per eseguire l'autenticazione tramite un browser.
1. Scegliere **Connect to Azure Blockchain Service consortium** (Connetti al consorzio del servizio Azure Blockchain) nell'elenco a discesa del riquadro comandi.
1. Scegliere la sottoscrizione e il gruppo di risorse associati al membro del consorzio del servizio Azure Blockchain.
1. Scegliere il consorzio dall'elenco.

I membri del consorzio e della blockchain sono elencati nella barra laterale di VS Code Explorer.

![Consorzio visualizzato in Explorer](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata usata l'estensione Azure Blockchain Development Kit per Ethereum di VS Code per collegarsi a un consorzio nel servizio Azure Blockchain. Provare l'esercitazione successiva per usare Azure Blockchain Development Kit per Ethereum per creare, compilare, distribuire ed eseguire una funzione di contratto intelligente tramite una transazione.

> [!div class="nextstepaction"]
> [Creare, compilare e distribuire contratti intelligenti in Servizio Azure Blockchain](send-transaction.md)