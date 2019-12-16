---
title: Usare Blockchain Data Manager per aggiornare Azure Cosmos DB - Servizio Azure Blockchain
description: Usare Blockchain Data Manager per Servizio Azure Blockchain per inviare dati della blockchain ad Azure Cosmos DB
ms.date: 12/04/2019
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 79c39d9883b5ba618e368b0ff6d3e95f1af5bd96
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977396"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Esercitazione: Usare Blockchain Data Manager per inviare dati ad Azure Cosmos DB

In questa esercitazione si usa Blockchain Data Manager per il servizio Azure Blockchain per registrare i dati delle transazioni blockchain in Azure Cosmos DB. Blockchain Data Manager consente di acquisire, trasformare e distribuire i dati del libro mastro della blockchain agli argomenti di Griglia di eventi di Azure. In Griglia di eventi di Azure è possibile usare un connettore per l'app per la logica di Azure per creare documenti in un database Azure Cosmos DB. Al termine dell'esercitazione, è possibile esplorare i dati delle transazioni blockchain in Esplora dati di Azure Cosmos DB.

[![Dettagli delle transazioni blockchain](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

In questa esercitazione:

> [!div class="checklist"]
> * Creare un'istanza di Blockchain Data Manager
> * Aggiungere un'applicazione blockchain per decodificare gli eventi e le proprietà delle transazioni
> * Creare un account e un database Azure Cosmos DB per archiviare i dati delle transazioni
> * Creare un'app per la logica di Azure per connettere un argomento di Griglia di eventi di Azure ad Azure Cosmos DB
> * Inviare una transazione a un libro mastro della blockchain
> * Visualizzare i dati della transazione decodificati in Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md) o [Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure](create-member-cli.md)
* Completare [Avvio rapido: Usare Visual Studio Code per connettersi alla rete di un consorzio del servizio Azure Blockchain](connect-vscode.md). I moduli di avvio rapido guidano l'utente attraverso l'installazione dell'estensione [Azure Blockchain Development Kit per Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e la configurazione dell'ambiente di sviluppo blockchain.
* Completare l'articolo [Esercitazione: Usare Visual Studio Code per creare, compilare e distribuire contratti intelligenti](send-transaction.md). Questa esercitazione illustra la creazione di un contratto intelligente di esempio.
* Creare un [argomento di Griglia di eventi](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informazioni su [Gestori eventi di Griglia di eventi di Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Creare un'istanza

Un'istanza di Blockchain Data Manager connette e monitora un nodo della transazione del servizio Azure Blockchain. Un'istanza acquisisce tutti i dati di blocchi e transazioni non elaborati dal nodo della transazione. Una connessione in uscita invia i dati della blockchain a Griglia di eventi di Azure. Quando si crea l'istanza, viene configurata una singola connessione in uscita.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure Blockchain creato nel prerequisito [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md). Selezionare **Blockchain Data Manager**.
1. Selezionare **Aggiungi**.

    ![Aggiungere Blockchain Data Manager](./media/data-manager-cosmosdb/add-instance.png)

    Immettere i dettagli seguenti:

    Impostazione | Esempio | DESCRIZIONE
    --------|---------|------------
    NOME | mywatcher | Immettere un nome univoco per un'istanza di Blockchain Data Manager connessa.
    Nodo di transazione | myblockchainmember | Scegliere il nodo della transazione predefinito del membro del servizio Azure Blockchain creato nel prerequisito.
    Nome connessione | cosmosdb | Immettere un nome univoco della connessione in uscita a cui vengono inviati i dati della transazione blockchain.
    Endpoint di Griglia di eventi | mytopic | Scegliere un argomento di Griglia di eventi creato nel prerequisito. Note: L'istanza di Blockchain Data Manager e l'argomento di Griglia di eventi devono trovarsi nella stessa sottoscrizione.

1. Selezionare **OK**.

    La creazione di un'istanza di Blockchain Data Manager richiede meno di un minuto. Una volta distribuita, l'istanza viene avviata automaticamente. Un'istanza di Blockchain Data Manager in esecuzione acquisisce gli eventi della blockchain dal nodo della transazione e invia i dati a Griglia di eventi.

## <a name="add-application"></a>Aggiunta di un'applicazione

Aggiungere l'applicazione blockchain **helloblockchain** in modo che Blockchain Data Manager decodifichi lo stato di eventi e proprietà. Per aggiungere l'applicazione, Blockchain Data Manager richiede l'ABI e il file bytecode del contratto intelligente.

### <a name="get-contract-abi-and-bytecode"></a>Ottenere l'ABI e il bytecode del contratto

L'interfaccia ABI (Application Binary Interface) del contratto definisce le interfacce del contratto intelligente. Descrive come interagire con il contratto intelligente. È possibile usare l'[estensione Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) per copiare l'ABI del contratto negli Appunti.

1. Nel riquadro di esplorazione di Visual Studio Code espandere la cartella **build/contracts** del progetto Solidity **helloblockchain** creato nel prerequisito [Esercitazione: Usare Visual Studio Code per creare, compilare e distribuire contratti intelligenti](send-transaction.md).
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Selezionare **Copy contract ABI** (Copia interfaccia ABI contratto).

    ![Riquadro di Visual Studio Code con l'opzione Copia interfaccia ABI contratto selezionata](./media/data-manager-cosmosdb/abi-devkit.png)

    L'interfaccia ABI del contratto viene copiata negli Appunti.

1. Salvare la matrice **abi** come file JSON. Ad esempio, *abi.json*. Questo file verrà usato in un passaggio successivo.

Blockchain Data Manager richiede il bytecode distribuito per il contratto intelligente. Il bytecode distribuito è diverso dal bytecode del contratto intelligente. È possibile usare l'estensione del kit di sviluppo di Azure Blockchain per copiare il bytecode negli Appunti.

1. Nel riquadro di Esplora risorse di Visual Studio Code espandere la cartella **build/contracts** del progetto Solidity.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Selezionare **Copy Transaction Bytecode** (Copia bytecode della transazione).

    ![Riquadro di Visual Studio Code con l'opzione Copy Transaction Bytecode selezionata](./media/data-manager-cosmosdb/bytecode-devkit.png)

    Il bytecode viene copiato negli Appunti.

1. Salvare il valore **bytecode** come file JSON. Ad esempio, *bytecode.json*. Questo file verrà usato in un passaggio successivo.

L'esempio seguente mostra i file *abi.json* e *bytecode.json* aperti nell'editor di VS Code. I file dovrebbero essere simili.

![Esempio di file abi.json e bytecode.json](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Creare l'URL dell'ABI e del bytecode del contratto

Blockchain Data Manager richiede che i file ABI e bytecode del contratto siano accessibili da un URL durante l'aggiunta di un'applicazione. È possibile usare un account di archiviazione di Azure per fornire un URL accessibile privatamente.

#### <a name="create-storage-account"></a>Crea account di archiviazione

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Caricare i file del contratto

1. Creare un nuovo contenitore per l'account di archiviazione. Selezionare **Contenitori > Contenitore**.

    ![Creare un contenitore dell'account di archiviazione](./media/data-manager-cosmosdb/create-container.png)

    | Impostazione | DESCRIZIONE |
    |---------|-------------|
    | NOME  | Assegnare un nome al contenitore. Ad esempio, *smartcontract* |
    | Livello di accesso pubblico | Scegliere *Privato (nessun accesso anonimo)* |

1. Fare clic su **OK** per creare il contenitore.
1. Selezionare il contenitore e quindi **Carica**.
1. Scegliere entrambi i file JSON creati nella sezione [Ottenere l'ABI e il bytecode del contratto](#get-contract-abi-and-bytecode).

    ![Caricamento del BLOB](./media/data-manager-cosmosdb/upload-blobs.png)

    Selezionare **Carica**.

#### <a name="generate-url"></a>Generare l'URL

Per ogni BLOB, generare una firma di accesso condiviso.

1. Selezionare il BLOB JSON dell'ABI.
1. Selezionare **Genera firma di accesso condiviso**
1. Impostare la scadenza desiderata per la firma di accesso, quindi selezionare **Genera URL e token di firma di accesso condiviso del BLOB**.

    ![Generare il token di firma di accesso condiviso](./media/data-manager-cosmosdb/generate-sas.png)

1. Copiare il valore di **URL di firma di accesso condiviso del BLOB** e salvarlo per la sezione successiva.
1. Ripetere la procedura [Generare l'URL](#generate-url) per il BLOB JSON del bytecode.

### <a name="add-helloblockchain-application-to-instance"></a>Aggiungere l'applicazione helloblockchain all'istanza

1. Selezionare l'istanza di Blockchain Data Manager nell'elenco di istanze.
1. Selezionare **Blockchain applications** (Applicazioni blockchain).
1. Selezionare **Aggiungi**.

    ![Aggiungere un'applicazione blockchain](./media/data-manager-cosmosdb/add-application.png)

    Immettere il nome dell'applicazione blockchain e gli URL dell'ABI e del bytecode del contratto intelligente.

    Impostazione | DESCRIZIONE
    --------|------------
    NOME | Immettere un nome univoco per l'applicazione blockchain da monitorare.
    Contract ABI (ABI contratto) | Percorso URL del file ABI del contratto. Per altre informazioni, vedere [Creare l'URL dell'ABI e del bytecode del contratto](#create-contract-abi-and-bytecode-url).
    Contract bytecode (Bytecode contratto) | Percorso URL del file bytecode. Per altre informazioni, vedere [Creare l'URL dell'ABI e del bytecode del contratto](#create-contract-abi-and-bytecode-url).

1. Selezionare **OK**.

    Una volta creata, l'applicazione viene visualizzata nell'elenco di applicazioni blockchain.

    ![Elenco di applicazioni blockchain](./media/data-manager-cosmosdb/artifact-list.png)

È possibile eliminare l'account di archiviazione di Azure o usarlo per configurare altre applicazioni blockchain. Se si vuole eliminare l'account di archiviazione di Azure, è possibile eliminare il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminati anche l'account di archiviazione associato e tutte le altre risorse correlate al gruppo di risorse.

## <a name="create-azure-cosmos-db"></a>Creare l'istanza di Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Aggiungere un database e un contenitore

È possibile usare Esplora dati nel portale di Azure per creare un database e un contenitore.

1. Scegliere **Esplora dati** dal menu di spostamento sinistro nella pagina dell'account Azure Cosmos DB, quindi selezionare **Nuovo contenitore**.
1. Nel riquadro **Aggiungi contenitore** immettere le impostazioni per il nuovo contenitore.

    ![Impostazioni per l'aggiunta del contenitore](./media/data-manager-cosmosdb/add-container.png)

    | Impostazione | DESCRIZIONE
    |---------|-------------|
    | ID database | Immettere **blockchain-data** come nome del nuovo database. |
    | Velocità effettiva | Lasciare il numero di unità elaborate impostato su **400** unità richieste al secondo (UR/s). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento.|
    | ID contenitore | Immettere **Messages** come nome per il nuovo contenitore. |
    | Chiave di partizione | Usare **/MessageType** come chiave di partizione. |

1. Selezionare **OK**. In Esplora dati vengono visualizzati il nuovo database e il nuovo contenitore creati.

## <a name="create-logic-app"></a>Creare un'app per la logica

Le app per la logica di Azure consentono di pianificare e automatizzare i processi e i flussi di lavoro aziendali quando occorre integrare sistemi e servizi. È possibile usare un'app per la logica per connettere Griglia di eventi ad Azure Cosmos DB.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.
1. Specificare i dettagli sulla posizione in cui creare l'app per la logica. Al termine, selezionare **Crea**.

    Per altre informazioni sulla creazione di App per la logica di Azure, vedere [Creare flussi di lavoro automatizzati con App per la logica di Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Dopo che l'app è stata distribuita in Azure, selezionare la risorsa app per la logica.
1. Nella sezione **Modelli** di Progettazione app per la logica selezionare **App per la logica vuota**.

### <a name="add-event-grid-trigger"></a>Aggiungi trigger Griglia di eventi

Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica che si avvia ed esegue il flusso di lavoro. Usare un trigger di Griglia di eventi di Azure per inviare i dati delle transazioni blockchain da Griglia di eventi a Cosmos DB.

1. In Progettazione app per la logica cercare e selezionare il connettore **Griglia di eventi di Azure**.
1. Nella scheda **Trigger** selezionare **Quando si verifica un evento della risorsa**.
1. Creare una connessione API all'argomento di Griglia di eventi.

    ![Impostazioni del trigger di Griglia di eventi](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Impostazione | DESCRIZIONE
    |---------|-------------|
    | Subscription | Scegliere la sottoscrizione che contiene l'argomento di Griglia di eventi. |
    | Tipo di risorsa | Scegliere **Microsoft.EventGrid.Topics**. |
    | Nome risorsa | Scegliere il nome dell'argomento di Griglia di eventi a cui Blockchain Data Manager invia i messaggi con i dati delle transazioni. |

### <a name="add-cosmos-db-action"></a>Aggiungere un'azione di Cosmos DB

Aggiungere un'azione per creare un documento in Cosmos DB per ogni transazione. Usare il tipo di messaggio transazione come chiave di partizione per categorizzare i messaggi.

1. Selezionare **Nuovo passaggio**.
1. In **Scegliere un'azione** cercare **Azure Cosmos DB**.
1. Scegliere **Azure Cosmos DB > Azioni > Crea o aggiorna il documento**.
1. Creare una connessione API al database Cosmos DB.

    ![Impostazioni di connessione di Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Impostazione | DESCRIZIONE
    |---------|-------------|
    | Connection Name (Nome connessione) | Scegliere la sottoscrizione che contiene l'argomento di Griglia di eventi. |
    | Account DocumentDB | Scegliere l'account DocumentDB creato nella sezione [Creare l'account Azure Cosmos DB](#create-azure-cosmos-db). |

1. Immettere l'**ID database** e l'**ID raccolta** per il database Azure Cosmos DB creato in precedenza nella sezione [Aggiungere un database e un contenitore](#add-a-database-and-container).

1. Selezionare l'impostazione **Documento**. Nel popup *Aggiungi contenuto dinamico* selezionare **Espressione**, quindi copiare e incollare l'espressione seguente:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    L'espressione recupera la parte di dati del messaggio e imposta l'ID su un valore timestamp.

1. Selezionare **Aggiungi nuovo parametro** e scegliere **Valore chiave di partizione**.
1. Impostare **Valore chiave di partizione** su `"@{triggerBody()['data']['MessageType']}"`. Il valore deve essere racchiuso tra virgolette doppie.

    ![Progettazione app per la logica con impostazioni di Cosmos DB](./media/data-manager-cosmosdb/create-action.png)

    Il valore imposta la chiave di partizione sul tipo di messaggio transazione.

1. Selezionare **Salva**.

L'app per la logica monitora l'argomento di Griglia di eventi. Quando viene inviato un nuovo messaggio su una transazione da Blockchain Data Manager, l'app per la logica crea un documento in Cosmos DB.

## <a name="send-a-transaction"></a>Inviare una transazione

Successivamente, inviare una transazione al libro mastro della blockchain per testare quello che è stato creato. Usare lo script **sendrequest.js** creato nel prerequisito [Esercitazione: Usare Visual Studio Code per creare, compilare e distribuire contratti intelligenti](send-transaction.md).

Nel riquadro del terminale di VS Code usare Truffle per eseguire lo script nella rete blockchain per consorzi. Nella barra dei menu del riquadro del terminale selezionare la scheda **Terminale** e **PowerShell** nell'elenco a discesa.

``` PowerShell
truffle exec sendrequest.js --network <blockchain network>
```

Sostituire \<blockchain network\> con il nome della rete blockchain definita in **truffle-config.js**.

![Inviare la transazione](./media/data-manager-cosmosdb/send-request.png)

## <a name="view-transaction-data"></a>Visualizzare i dati delle transazioni

Dopo aver connesso Blockchain Data Manager ad Azure Cosmos DB, è possibile visualizzare i messaggi sulle transazioni blockchain in Esplora dati di Cosmos DB.

1. Passare alla visualizzazione Esplora dati di Cosmos DB. Ad esempio, **cosmosdb-blockchain > Esplora dati > blockchain-data > Messaggi > Elementi**.

    ![Esplora dati di Cosmos DB](./media/data-manager-cosmosdb/data-explorer.png)

    Esplora dati elenca i messaggi di dati della blockchain creati nel database Cosmos DB.

1. Esplorare i messaggi selezionando l'ID elemento e trovare il messaggio con l'hash della transazione corrispondente.

    [![Dettagli della transazione blockchain](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Il messaggio non elaborato contiene i dettagli sulla transazione. Tuttavia, le informazioni sulle proprietà sono crittografate.

    Poiché è stato aggiunto il contratto intelligente HelloBlockchain all'istanza di Blockchain Data Manager, viene inviato anche un tipo di messaggio **ContractProperties** che contiene informazioni decodificate sulle proprietà.

1. Trovare il messaggio **ContractProperties** per la transazione. Dovrebbe essere il messaggio successivo nell'elenco.

    [![Dettagli della transazione blockchain](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    La matrice **DecodedProperties** contiene le proprietà della transazione.

Congratulazioni! È stato creato un riquadro di esplorazione dei messaggi sulle transazioni usando Blockchain Data Manager e Azure Cosmos DB.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare le risorse e i gruppi di risorse usati per questa esercitazione. Per eliminare un gruppo di risorse:

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare.
1. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'integrazione con i libri mastri della blockchain.

> [!div class="nextstepaction"]
> [Uso del connettore Ethereum Blockchain con App per la logica di Azure](ethereum-logic-app.md)
