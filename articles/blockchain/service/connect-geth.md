---
title: Usare Geth per connettersi al servizio Azure Blockchain
description: Connettersi a una rete del servizio Azure Blockchain con Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931781"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Guida introduttiva: Usare Geth per connettersi a un nodo della transazione

Geth è un client Go Ethereum che è possibile usare per connettersi a un'istanza di Geth in un nodo della transazione del servizio Azure Blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Installare [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Completare [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md) o [Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure](create-member-cli.md)

## <a name="get-the-geth-connection-string"></a>Ottenere la stringa di connessione di Geth

È possibile trovare la stringa di connessione di Geth nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure Blockchain. Selezionare **Transaction nodes** (Nodi transazione) e il collegamento al nodo della transazione predefinito.

    ![Selezionare il nodo della transazione predefinito](./media/connect-geth/transaction-nodes.png)

1. Selezionare **Connection strings** (Stringhe di connessione).
1. Copiare la stringa di connessione da **HTTPS (Access key 1)** (HTTPS - chiave di accesso 1). Il comando è necessario per la sezione successiva.

    ![Stringa di connessione](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Connettersi a Geth

1. Aprire una shell o un prompt dei comandi.
1. Usare il comando secondario attach di Geth per collegarsi all'istanza di Geth in esecuzione nel nodo della transazione. Incollare la stringa di connessione come argomento del comando secondario attach. Ad esempio,

    ```
    geth attach <connection string>
    ```

1. Dopo la connessione alla console Ethereum del nodo della transazione, è possibile chiamare l'API di Dapp JavaScript web3 o l'API di amministrazione.

    Ad esempio, usare l'API seguente per individuare il valore di chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    In questo esempio, il valore di chainId è 297.

    ![Opzione del servizio Azure Blockchain](./media/connect-geth/geth-attach.png)

1. Per disconnettersi dalla console, digitare `exit`.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato usato il client Geth per connettersi a un'istanza di Geth in un nodo della transazione del servizio Azure Blockchain. Provare l'esercitazione successiva per usare Azure Blockchain Development Kit per Ethereum e Truffle per creare, compilare, distribuire ed eseguire una funzione di contratto intelligente tramite una transazione.

> [!div class="nextstepaction"]
> [Usare Visual Studio Code per creare, compilare e distribuire contratti intelligenti](send-transaction.md)