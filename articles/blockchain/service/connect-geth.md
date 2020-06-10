---
title: Usare Geth per collegarsi al servizio Azure Blockchain
description: Collegarsi a un'istanza Geth nel nodo della transazione del servizio Azure Blockchain
ms.date: 05/26/2020
ms.topic: quickstart
ms.reviewer: maheshna
ms.openlocfilehash: 2c5b6a56d96ac132949052e9879c8f61d41ffb06
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994784"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Avvio rapido: Usare Geth per collegarsi a un nodo della transazione del servizio Azure Blockchain

In questa guida di avvio rapido si usa il client Geth per collegarsi a un'istanza di Geth in un nodo della transazione del servizio Azure Blockchain. Dopo il collegamento, usare la console di Geth per chiamare un'API JavaScript Ethereum.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Installare [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Completare [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md) o [Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Ottenere la stringa di connessione di Geth

La stringa di connessione di Geth per un nodo della transazione del servizio Azure Blockchain si ottiene nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure Blockchain. Selezionare **Transaction nodes** (Nodi transazione) e il collegamento al nodo della transazione predefinito.

    ![Selezionare il nodo della transazione predefinito](./media/connect-geth/transaction-nodes.png)

1. Selezionare **Connection strings** (Stringhe di connessione).
1. Copiare la stringa di connessione da **HTTPS (Access key 1)** (HTTPS - chiave di accesso 1). La stringa è necessaria per la sezione successiva.

    ![Stringa di connessione](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Connettersi a Geth

1. Aprire una shell o un prompt dei comandi.
1. Usare il comando secondario attach di Geth per collegarsi all'istanza di Geth in esecuzione nel nodo della transazione. Incollare la stringa di connessione come argomento del comando secondario attach. Ad esempio:

    ``` bash
    geth attach <connection string>
    ```

1. Dopo la connessione alla console Ethereum del nodo della transazione, è possibile usare l'API JavaScript Ethereum.

    Ad esempio, usare l'API seguente per trovare il valore di chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    In questo esempio il valore di chainId è 661.

    ![Opzione del servizio Azure Blockchain](./media/connect-geth/geth-attach.png)

1. Per disconnettersi dalla console, digitare `exit`.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato usato il client Geth per connettersi a un'istanza di Geth in un nodo della transazione del servizio Azure Blockchain. Provare l'esercitazione successiva per usare Azure Blockchain Development Kit per Ethereum per creare, compilare, distribuire ed eseguire una funzione di contratto intelligente tramite una transazione.

> [!div class="nextstepaction"]
> [Creare, compilare e distribuire contratti intelligenti in Servizio Azure Blockchain](send-transaction.md)
