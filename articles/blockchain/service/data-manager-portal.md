---
title: Configure Blockchain Data Manager using Azure portal - Azure Blockchain Service
description: Create and manage Blockchain Data Manager for Azure Blockchain Service using the Azure portal.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 9c682f449fbab823134d626870c7dcfe8a8f2847
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455818"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurare Blockchain Data Manager con il portale di Azure

Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data and send it to an Azure Event Grid Topic.

To configure a Blockchain Data Manager instance, you:

* Create a Blockchain Data Manager instance for an Azure Blockchain Service transaction node
* Add your blockchain applications

## <a name="prerequisites"></a>Prerequisiti

* Complete [Quickstart: Create a blockchain member using the Azure portal](create-member.md) or [Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI](create-member-cli.md)
* Creare un [argomento di Griglia di eventi](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informazioni su [Gestori eventi di Griglia di eventi di Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Creare un'istanza

Un'istanza di Blockchain Data Manager connette e monitora un nodo della transazione del servizio Azure Blockchain. Only users with access to the transaction node can create a connection. Un'istanza acquisisce tutti i dati di blocchi e transazioni non elaborati dal nodo della transazione.

Una connessione in uscita invia i dati della blockchain a Griglia di eventi di Azure. Quando si crea l'istanza, viene configurata una singola connessione in uscita. Blockchain Data Manager supporta più connessioni in uscita per gli argomenti di Griglia di eventi per qualsiasi istanza di Blockchain Data Manager specificata. È possibile inviare i dati della blockchain a una o più destinazioni. To add another destination, just add additional outbound connections to the instance.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Go to the Azure Blockchain Service member you want to connect to Blockchain Data Manager. Selezionare **Blockchain Data Manager**.
1. Selezionare **Aggiungi**.

    ![Aggiungere Blockchain Data Manager](./media/data-manager-portal/add-instance.png)

    Immettere i dettagli seguenti:

    Impostazione | Description
    --------|------------
    name | Immettere un nome univoco per un'istanza di Blockchain Data Manager connessa. The Blockchain Data Manager name can contain lower case letters and numbers and has a maximum length of 20 characters.
    Nodo di transazione | Choose a transaction node. Only transaction nodes you have read access are listed.
    Nome connessione | Immettere un nome univoco della connessione in uscita a cui vengono inviati i dati della transazione blockchain.
    Endpoint di Griglia di eventi | Choose an event grid topic in the same subscription as the Blockchain Data Manager instance.

1. Selezionare **OK**.

    La creazione di un'istanza di Blockchain Data Manager richiede meno di un minuto. Una volta distribuita, l'istanza viene avviata automaticamente. A running Blockchain Data Manager instance captures blockchain events from the transaction node and sends data to the outbound connections.

    The new instance appears in the list of Blockchain Data Manager instances for the Azure Blockchain Service member.

    ![List of Blockchain Data Member instances](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Add blockchain application

If you add a blockchain application, Blockchain Data Manager decodes event and property state for the application. Otherwise, only raw block and raw transaction data is sent. Blockchain Data Manager also discovers contract addresses when the contract is deployed. You can add multiple blockchain applications to a Blockchain Data Manager instance.

> [!IMPORTANT]
> Currently, blockchain applications that declare Solidity [array types](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) or [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) are not fully supported. Properties declared as array or mapping types will not be decoded in *ContractPropertiesMsg* or *DecodedContractEventsMsg* messages.

Blockchain Data Manager requires a smart contract ABI and deployed bytecode file to add the application.

### <a name="get-contract-abi-and-bytecode"></a>Get Contract ABI and bytecode

L'interfaccia ABI (Application Binary Interface) del contratto definisce le interfacce del contratto intelligente. Descrive come interagire con il contratto intelligente. È possibile usare l'[estensione Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) per copiare l'ABI del contratto negli Appunti.

1. Nel riquadro di Esplora risorse di Visual Studio Code espandere la cartella **build/contracts** del progetto Solidity.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Selezionare **Copy contract ABI** (Copia interfaccia ABI contratto).

    ![Riquadro di Visual Studio Code con l'opzione Copia interfaccia ABI contratto selezionata](./media/data-manager-portal/abi-devkit.png)

    L'interfaccia ABI del contratto viene copiata negli Appunti.

1. Salvare la matrice **abi** come file JSON. Ad esempio, *abi.json*. Questo file verrà usato in un passaggio successivo.

Blockchain Data Manager richiede il bytecode distribuito per il contratto intelligente. Il bytecode distribuito è diverso dal bytecode del contratto intelligente. È possibile ottenere il bytecode distribuito dal file di metadati del contratto compilato.

1. Aprire il file di metadati del contratto disponibile nella cartella **build/contracts** del progetto Solidity. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Trovare l'elemento **deployedBytecode** nel file JSON.
1. Copiare il valore esadecimale senza virgolette.

    ![Riquadro di Visual Studio Code con il bytecode nei metadati](./media/data-manager-portal/bytecode-metadata.png)

1. Salvare il valore **bytecode** come file JSON. Ad esempio, *bytecode.json*. Questo file verrà usato in un passaggio successivo.

L'esempio seguente mostra i file *abi.json* e *bytecode.json* aperti nell'editor di VS Code. I file dovrebbero essere simili.

![Esempio di file abi.json e bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Creare l'URL dell'ABI e del bytecode del contratto

Blockchain Data Manager richiede che i file ABI e bytecode del contratto siano accessibili da un URL durante l'aggiunta di un'applicazione. È possibile usare un account di archiviazione di Azure per fornire un URL accessibile privatamente.

#### <a name="create-storage-account"></a>Crea account di archiviazione

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Caricare i file del contratto

1. Creare un nuovo contenitore per l'account di archiviazione. Selezionare **Contenitori > Contenitore**.

    ![Creare un contenitore dell'account di archiviazione](./media/data-manager-portal/create-container.png)

    | Campo | Description |
    |-------|-------------|
    | name  | Assegnare un nome al contenitore. Ad esempio, *smartcontract* |
    | Livello di accesso pubblico | Scegliere *Privato (nessun accesso anonimo)* |

1. Fare clic su **OK** per creare il contenitore.
1. Selezionare il contenitore e quindi **Carica**.
1. Scegliere entrambi i file JSON creati nella sezione [Ottenere l'ABI e il bytecode del contratto](#get-contract-abi-and-bytecode).

    ![Caricamento del BLOB](./media/data-manager-portal/upload-blobs.png)

    Selezionare **Carica**.

#### <a name="generate-url"></a>Generare l'URL

Per ogni BLOB, generare una firma di accesso condiviso.

1. Selezionare il BLOB JSON dell'ABI.
1. Selezionare **Genera firma di accesso condiviso**
1. Impostare la scadenza desiderata per la firma di accesso, quindi selezionare **Genera URL e token di firma di accesso condiviso del BLOB**.

    ![Generare il token di firma di accesso condiviso](./media/data-manager-portal/generate-sas.png)

1. Copiare il valore di **URL di firma di accesso condiviso del BLOB** e salvarlo per la sezione successiva.
1. Ripetere la procedura [Generare l'URL](#generate-url) per il BLOB JSON del bytecode.

### <a name="add-application-to-instance"></a>Add application to instance

1. Selezionare l'istanza di Blockchain Data Manager nell'elenco di istanze.
1. Selezionare **Blockchain applications** (Applicazioni blockchain).
1. Selezionare **Aggiungi**.

    ![Aggiungere un'applicazione blockchain](./media/data-manager-portal/add-application.png)

    Immettere il nome dell'applicazione blockchain e gli URL dell'ABI e del bytecode del contratto intelligente.

    Impostazione | Description
    --------|------------
    name | Immettere un nome univoco per l'applicazione blockchain da monitorare.
    Contract ABI (ABI contratto) | Percorso URL del file ABI del contratto. Per altre informazioni, vedere [Creare l'URL dell'ABI e del bytecode del contratto](#create-contract-abi-and-bytecode-url).
    Contract bytecode (Bytecode contratto) | Percorso URL del file bytecode. Per altre informazioni, vedere [Creare l'URL dell'ABI e del bytecode del contratto](#create-contract-abi-and-bytecode-url).

1. Selezionare **OK**.

    Una volta creata, l'applicazione viene visualizzata nell'elenco di applicazioni blockchain.

    ![Elenco di applicazioni blockchain](./media/data-manager-portal/artifact-list.png)

È possibile eliminare l'account di archiviazione di Azure o usarlo per configurare altre applicazioni blockchain. Se si vuole eliminare l'account di archiviazione di Azure, è possibile eliminare il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminati anche l'account di archiviazione associato e tutte le altre risorse correlate al gruppo di risorse.

## <a name="stop-instance"></a>Stop instance

Stop the Blockchain Manager instance when you want to stop capturing blockchain events and sending data to the outbound connections. When the instance is stopped, no charges are incurred for Blockchain Data Manager. Per altre informazioni, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Go to **Overview** and select **Stop**.

    ![Stop instance](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Passaggi successivi

Try the next tutorial creating a blockchain transaction message explorer using Blockchain Data Manager and Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Use Blockchain Data Manager to send data to Azure Cosmos DB](data-manager-cosmosdb.md)