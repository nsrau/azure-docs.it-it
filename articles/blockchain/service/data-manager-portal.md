---
title: Configurare blockchain Data Manager-portale di Azure
description: Come creare e gestire blockchain Data Manager usando il portale di Azure.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 1f46fe92fd6650daa3ba4b9a930c4d781925d3fc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518255"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurare blockchain Data Manager usando il portale di Azure

Configurare blockchain Data Manager per il servizio Azure blockchain per acquisire i dati blockchain e inviarli a un argomento di griglia di eventi di Azure.

Per configurare un'istanza di Data Manager blockchain, è necessario:

* Creare un'istanza di Data Manager blockchain per un nodo di transazione del servizio blockchain di Azure
* Aggiungere le applicazioni blockchain

## <a name="prerequisites"></a>Prerequisiti

* [Guida introduttiva: creare un membro blockchain usando il portale di Azure o la](create-member.md) [Guida introduttiva: creare un membro blockchain del servizio blockchain di Azure usando l'interfaccia](create-member-cli.md) della riga di comando
* Creare un [argomento di griglia di eventi](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informazioni sui [gestori eventi in griglia di eventi di Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Crea istanza

Un'istanza di blockchain Data Manager connette e monitora un nodo di transazione del servizio blockchain di Azure. Solo gli utenti con accesso al nodo di transazione possono creare una connessione. Un'istanza acquisisce tutti i dati del blocco non elaborato e della transazione non elaborati dal nodo della transazione.

Una connessione in uscita invia i dati di blockchain a griglia di eventi di Azure. Quando si crea l'istanza di, viene configurata una singola connessione in uscita. Blockchain Data Manager supporta le connessioni in uscita per più argomenti di griglia di eventi per qualsiasi istanza di blockchain Data Manager specificata. È possibile inviare i dati di blockchain a una singola destinazione o inviare dati blockchain a più destinazioni. Per aggiungere un'altra destinazione, è sufficiente aggiungere altre connessioni in uscita all'istanza.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure blockchain che si vuole connettere a blockchain Data Manager. Selezionare **Blockchain Data Manager**.
1. Selezionare **Aggiungi**.

    ![Aggiungi blockchain Data Manager](./media/data-manager-portal/add-instance.png)

    Immettere i dettagli seguenti:

    Impostazione | Descrizione
    --------|------------
    Name | Immettere un nome univoco per una Data Manager blockchain connessa. Il nome del Data Manager blockchain può contenere lettere minuscole e numeri e ha una lunghezza massima di 20 caratteri.
    Nodo di transazione | Scegliere un nodo di transazione. Sono elencati solo i nodi di transazione per i quali si dispone dell'accesso in lettura.
    Nome connessione | Immettere un nome univoco della connessione in uscita in cui vengono inviati i dati della transazione blockchain.
    Endpoint di griglia di eventi | Scegliere un argomento di griglia di eventi nella stessa sottoscrizione dell'istanza di Data Manager blockchain.

1. Selezionare **OK**.

    La creazione di un'istanza di blockchain Data Manager richiede meno di un minuto. Una volta distribuita, l'istanza viene avviata automaticamente. Un'istanza in esecuzione di Data Manager blockchain acquisisce gli eventi blockchain dal nodo della transazione e invia i dati alle connessioni in uscita. Se si desidera acquisire anche dati di eventi e proprietà decodificati dal nodo della transazione, creare un'applicazione blockchain per l'istanza di blockchain Data Manager.

    La nuova istanza viene visualizzata nell'elenco di istanze di blockchain Data Manager per il membro del servizio blockchain di Azure.

    ![Elenco di istanze del membro dati blockchain](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Aggiungi applicazione blockchain

Se si aggiunge un'applicazione blockchain, blockchain Data Manager decodifica l'evento e lo stato della proprietà per l'applicazione. In caso contrario, vengono inviati solo i dati delle transazioni RAW e i blocchi Raw. Blockchain Data Manager individua anche gli indirizzi del contratto quando il contratto viene distribuito. È possibile aggiungere più applicazioni blockchain a un'istanza di blockchain Data Manager.

Per aggiungere l'applicazione, blockchain Data Manager richiede un file ABI e bytecode con Smart Contract.

### <a name="get-contract-abi-and-bytecode"></a>Ottenere l'ABI e il bytecode del contratto

Il contratto ABI definisce le interfacce smart Contract. Viene descritto come interagire con il contratto Smart. È possibile usare [Azure blockchain Development Kit per l'estensione Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) per copiare l'ABI del contratto negli Appunti.

1. Nel riquadro Visual Studio Code Explorer espandere la cartella **Build/Contracts** del progetto di solidità.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **JSON** .
1. Selezionare **copia contratto Abi**.

    ![Riquadro Visual Studio Code con la selezione ABI del contratto di copia](./media/data-manager-portal/abi-devkit.png)

    L'interfaccia ABI del contratto viene copiata negli Appunti.

1. Salvare la matrice **Abi** come file JSON. Ad esempio, *ABI. JSON*. Il file viene usato in un passaggio successivo.

Il bytecode del contratto è la Smart Contract compilata eseguita dalla macchina virtuale Ethereum. È possibile utilizzare l'estensione per copiare il bytecode del contratto negli Appunti.

1. Nel riquadro Visual Studio Code Explorer espandere la cartella **Build/Contracts** del progetto di solidità.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **JSON** .
1. Selezionare **copia bytecode del contratto**.

    ![Riquadro Visual Studio Code con la selezione del bytecode del contratto di copia](./media/data-manager-portal/bytecode-devkit.png)

    Il bytecode del contratto viene copiato negli Appunti.

1. Salvare il valore **bytecode** come file JSON. Ad esempio, *bytecode. JSON*. Salva solo il valore esadecimale. Il file viene usato in un passaggio successivo.

L'esempio seguente mostra i file *ABI. JSON* e *bytecode. JSON* aperti nell'editor vs code. I file dovrebbero essere simili.

![Esempio di file ABI. JSON e bytecode. JSON](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Crea l'URL del contratto ABI e bytecode

Blockchain Data Manager richiede che i file ABI e bytecode del contratto siano accessibili da un URL durante l'aggiunta di un'applicazione. È possibile usare un account di archiviazione di Azure per fornire un URL accessibile privatamente.

#### <a name="create-storage-account"></a>Crea account di archiviazione

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Carica file di contratto

1. Creare un nuovo contenitore per l'account di archiviazione. Selezionare **contenitori > contenitore**.

    ![Creare un contenitore dell'account di archiviazione](./media/data-manager-portal/create-container.png)

    | Campo | Descrizione |
    |-------|-------------|
    | Name  | Assegnare un nome al contenitore. Ad esempio, *smartcontract* |
    | Livello di accesso pubblico | Scegliere *privato (nessun accesso anonimo)* |

1. Fare clic su **OK** per creare il contenitore.
1. Selezionare il contenitore e quindi fare clic su **carica**.
1. Scegliere entrambi i file JSON creati nella sezione [Get Contract Abi and bytecode](#get-contract-abi-and-bytecode) .

    ![Caricamento del BLOB](./media/data-manager-portal/upload-blobs.png)

    Selezionare **Carica**.

#### <a name="generate-url"></a>Generare l'URL

Per ogni BLOB, generare una firma di accesso condiviso.

1. Selezionare il BLOB JSON ABI.
1. Selezionare **genera SAS**
1. Impostare la scadenza della firma di accesso desiderata, quindi selezionare **genera token SAS BLOB e URL**.

    ![Genera token SAS](./media/data-manager-portal/generate-sas.png)

1. Copiare l' **URL** di firma di accesso condiviso BLOB e salvarlo per la sezione successiva.
1. Ripetere i passaggi [genera URL](#generate-url) per il BLOB JSON JSON.

### <a name="add-application-to-instance"></a>Aggiungi applicazione all'istanza

1. Selezionare l'istanza di blockchain Data Manager dall'elenco di istanze.
1. Selezionare **le applicazioni blockchain**.
1. Selezionare **Aggiungi**.

    ![Aggiungere un'applicazione blockchain](./media/data-manager-portal/add-application.png)

    Immettere il nome dell'applicazione blockchain e gli URL ABI e bytecode di Smart Contract.

    Impostazione | Descrizione
    --------|------------
    Name | Immettere un nome univoco per l'applicazione blockchain da rilevare.
    ABI contratto | Percorso URL del file ABI del contratto. Per altre informazioni, vedere [create Contract Abi and BYTECODE URL](#create-contract-abi-and-bytecode-url).
    Bytecode del contratto | Percorso URL del file bytecode. Per altre informazioni, vedere [create Contract Abi and BYTECODE URL](#create-contract-abi-and-bytecode-url).

1. Selezionare **OK**.

    Una volta creata l'applicazione, l'applicazione viene visualizzata nell'elenco delle applicazioni blockchain.

    ![Elenco di applicazioni blockchain](./media/data-manager-portal/artifact-list.png)

È possibile eliminare l'account di archiviazione di Azure o usarlo per configurare più applicazioni blockchain. Se si vuole eliminare l'account di archiviazione di Azure, è possibile eliminare il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminati anche l'account di archiviazione associato e tutte le altre risorse correlate al gruppo di risorse.

## <a name="stop-instance"></a>Arresta istanza

Arrestare l'istanza di blockchain Manager quando si desidera arrestare l'acquisizione degli eventi blockchain e l'invio dei dati alle connessioni in uscita. Quando l'istanza viene arrestata, non vengono addebitati costi per blockchain Data Manager. Per altre informazioni, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Passare a **Panoramica** e selezionare **Arresta**.

    ![Arresta istanza](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [gestori di eventi in griglia di eventi di Azure](../../event-grid/event-handlers.md).
