---
title: Connettersi a un account Azure Cosmos con collegamento privato di Azure
description: Informazioni su come accedere in modo sicuro all'account Azure Cosmos da una macchina virtuale creando un endpoint privato.
author: asudbring
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: e54aa00df9efa60cce0fd6fa1da32720f2947b12
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851197"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Connettersi privatamente a un account Azure Cosmos usando il collegamento privato di Azure

Endpoint privato di Azure è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse di collegamento privato.

In questo articolo si apprenderà come creare una VM in una rete virtuale di Azure e un account Azure Cosmos con un endpoint privato usando il portale di Azure. Quindi, è possibile accedere in modo sicuro all'account Azure Cosmos dalla macchina virtuale.

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al [portale di Azure.](https://portal.azure.com)

## <a name="create-a-vm"></a>Creare una VM

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si creerà una rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato (un account Azure Cosmos in questo esempio).

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.

1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Value |
    | ------- | ----- |
    | name | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Sottoscrizione | Selezionare la propria sottoscrizione.|
    | Gruppo di risorse | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Località | Selezionare **Stati Uniti centro-occidentali**.|
    | Subnet - Nome | Immettere *mySubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |
    |||

1. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.

### <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **calcolo** > **macchina virtuale**.

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | Value |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Area geografica | Selezionare **Stati Uniti centro-occidentali**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensioni | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a propria scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Immettere nuovamente la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: dischi**.

1. In **creare una macchina virtuale-dischi**lasciare le impostazioni predefinite e selezionare **Avanti: rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | Value |
    | ------- | ----- |
    | Rete virtuale | Lasciare l'impostazione predefinita **MyVirtualNetwork**.  |
    | Spazio degli indirizzi | Lasciare l'impostazione predefinita **10.1.0.0/24**.|
    | Subnet | Lasciare l'impostazione predefinita **mySubnet (10.1.0.0/24)** .|
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**.|
    ||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

## <a name="create-an-azure-cosmos-account"></a>Creare un account Azure Cosmos

Creare un [account API di Azure Cosmos SQL](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Per semplicità, è possibile creare l'account Azure Cosmos nella stessa area delle altre risorse (ovvero "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Creare un endpoint privato per l'account Azure Cosmos

Creare un collegamento privato per l'account Azure Cosmos, come descritto nella sezione [creare un collegamento privato usando la portale di Azure](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) dell'articolo collegato.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione *.rdp* scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Accedere all'account Azure Cosmos privatamente dalla macchina virtuale

In questa sezione si effettuerà la connessione privata all'account Azure Cosmos usando l'endpoint privato. 

1. Per includere l'indirizzo IP e il mapping DNS, accedere alla macchina virtuale *myVM*, aprire il file `c:\Windows\System32\Drivers\etc\hosts` e includere le informazioni DNS del passaggio precedente nel formato seguente:

   [Indirizzo IP privato] [Endpoint account]. Documents. Azure. com

   **Esempio:**

   mycosmosaccount.documents.azure.com 10.1.255.13

   mycosmosaccount-eastus.documents.azure.com 10.1.255.14


1. Nel Desktop remoto di *myVM*installare [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Selezionare **account Cosmos DB (anteprima)** con il pulsante destro del mouse.

1. Selezionare **Connetti a Cosmos DB**.

1. Selezionare **API**.

1. Immettere la stringa di connessione incollando le informazioni copiate in precedenza.

1. Selezionare **Avanti**.

1. Selezionare **Connessione**.

1. Esplorare i database e i contenitori di Azure Cosmos da *mycosmosaccount*.

1. (Facoltativamente) aggiungere nuovi elementi a *mycosmosaccount*.

1. Chiudere la connessione Desktop remoto a *myVM*.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'operazione, eliminare il gruppo di risorse e tutte le risorse in esso contenute usando l'endpoint privato, l'account Azure Cosmos e la macchina virtuale: 

1. Immettere *myResourceGroup* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere *myResourceGroup* per **digitare il nome del gruppo di risorse** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una VM in una rete virtuale, un account Azure Cosmos e un endpoint privato. È stata effettuata la connessione alla macchina virtuale da Internet e la comunicazione protetta con l'account Azure Cosmos usando un collegamento privato.

* Per altre informazioni sull'endpoint privato, vedere [che cos'è endpoint privato di Azure?](private-endpoint-overview.md).

* Per altre informazioni sulla limitazione dell'endpoint privato quando si usa con Azure Cosmos DB, vedere [collegamento privato di Azure con Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) articolo.