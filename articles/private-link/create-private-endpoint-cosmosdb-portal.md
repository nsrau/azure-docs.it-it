---
title: Connettersi a un account Azure Cosmos con Azure Private LinkConnect to an Azure Cosmos account with Azure Private Link
description: Informazioni su come accedere in modo sicuro all'account Cosmos di Azure da una macchina virtuale creando un endpoint privato.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252591"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Connettersi privatamente a un account Azure Cosmos usando Azure Private LinkConnect privately to an Azure Cosmos account using Azure Private Link

Azure Private Endpoint is the fundamental building block for Private Link in Azure. Consente alle risorse di Azure, ad esempio le macchine virtuali (VM), di comunicare privatamente con le risorse Private Link.It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In questo articolo si apprenderà come creare una macchina virtuale in una rete virtuale di Azure e un account Azure Cosmos con un endpoint privato tramite il portale di Azure.In this article, you will learn how to create a VM on an Azure virtual network and an Azure Cosmos account with a Private Endpoint using the Azure portal. È quindi possibile accedere in modo sicuro all'account Cosmos di Azure dalla macchina virtuale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [Azure.Sign](https://portal.azure.com) in to the Azure portal.

## <a name="create-a-vm"></a>Creare una macchina virtuale

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

In questa sezione verranno create una rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa Private Link (un account Cosmos di Azure in questo esempio).

In questa sezione è necessario sostituire i seguenti parametri nei passaggi con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<>nome-gruppo di risorse**  | myResourceGroup|
| **\<>nome-rete virtualeVirtual-network-name>** | myVirtualNetwork         |
| **\<>nome-regione**          | Stati Uniti centro-occidentali     |
| **\<>dello spazio indirizzo IPv4**   | 10.1.0.0\16          |
| **\<nome della>subnet**          | mySubnet        |
| **\<>subnet-address-range** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

1. Nella parte superiore sinistra dello schermo nel portale di Azure selezionare Crea una**macchina virtuale**di**calcolo** >  **delle risorse** > .

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **Stati Uniti centro-occidentali**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Inserisci un nome utente a tua scelta. |
    | Password | Immettere una password a propria scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Immettere nuovamente la password. |
    | **REGOLE DELLE PORTE IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: Dischi**.

1. In **Create a virtual machine - Disks**, lasciare le impostazioni predefinite e selezionare **Next: Networking**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Rete virtuale | Lasciare il valore predefinito **MyVirtualNetwork**.  |
    | Spazio degli indirizzi | Lasciare l'impostazione predefinita **10.1.0.0/24**.|
    | Subnet | Lasciare l'impostazione predefinita **mySubnet (10.1.0.0/24)**.|
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**.|
    ||

1. Selezionare **Revisione e creazione**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata** , selezionare **Crea**.

## <a name="create-an-azure-cosmos-account"></a>Creare un account Azure Cosmos

Creare un [account API SQL di Azure Cosmos.](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) Per semplicità, è possibile creare l'account Cosmos di Azure nella stessa area delle altre risorse (ovvero "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Creare un endpoint privato per l'account Cosmos di AzureCreate a Private Endpoint for your Azure Cosmos account

Creare un collegamento privato per l'account Azure Cosmos come descritto nella sezione [Creare un collegamento privato](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) usando il portale di Azure dell'articolo collegato.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file *con estensione rdp* scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un account diverso**per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Accedere all'account Cosmos di Azure privatamente dalla macchina virtualeAccess the Azure Cosmos account privately from the VM

In questa sezione ci si connetterà privatamente all'account Cosmos di Azure usando l'endpoint privato. 

1. Per includere l'indirizzo IP e il mapping DNS, `c:\Windows\System32\Drivers\etc\hosts` accedere alla macchina virtuale *myVM*, aprire il file e includere le informazioni DNS del passaggio precedente nel formato seguente:

   [Indirizzo IP privato] [Endpoint account].documents.azure.com

   **Esempio:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. In Desktop remoto di *myVM*installare [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Selezionare **Cosmos DB Accounts (Preview)** con il pulsante destro del mouse.

1. Selezionare **Connetti a Cosmos DB**.

1. Selezionare **API**.

1. Immettere la stringa di connessione incollando le informazioni copiate in precedenza.

1. Fare clic su **Avanti**.

1. Selezionare **Connetti**.

1. Sfogliare i database e i contenitori di Azure Cosmos da *mycosmosaccount*.

1. (Opzionalmente) aggiungere nuovi elementi a *mycosmosaccount*.

1. Chiudere la connessione desktop remoto a *myVM*.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'uso dell'endpoint privato, dell'account Cosmos di Azure e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse che contiene:When you're done using the Private Endpoint, Azure Cosmos account and the VM, delete the resource group and all of the resources it contains: 

1. Immettere *myResourceGroup* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere *myResourceGroup* per **TYPE THE RESOURCE GROUP NAME** e selezionare **Delete**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una macchina virtuale in una rete virtuale, un account Azure Cosmos e un endpoint privato. Si è connessi alla macchina virtuale da Internet e viene comunicato in modo sicuro all'account Azure Cosmos tramite Private Link.

* Per altre informazioni sull'endpoint privato, vedere [Che cos'è l'endpoint privato di Azure?.](private-endpoint-overview.md)

* Per altre informazioni sulla limitazione dell'endpoint privato quando si usa Azure Cosmos DB, vedere l'articolo Azure Private Link with Azure Cosmos DB .To learn more about limitation of Private Endpoint when using with Azure Cosmos DB, see [Azure Private Link with Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) article.
