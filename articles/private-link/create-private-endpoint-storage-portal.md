---
title: Connettersi in privato a un account di archiviazione usando un endpoint privato di Azure
description: Informazioni su come connettersi privatamente a un account di archiviazione in Azure usando un endpoint privato.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8c76333d5a2be8a2c589dbe54389b023fef34854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252538"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connettersi in privato a un account di archiviazione usando un endpoint privato di Azure
Azure Private Endpoint is the fundamental building block for Private Link in Azure. Consente alle risorse di Azure, ad esempio le macchine virtuali (VM), di comunicare privatamente con le risorse Private Link.It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In questa guida introduttiva si apprenderà come creare una macchina virtuale in una rete virtuale di Azure, un account di archiviazione con un endpoint privato tramite il portale di Azure.In this Quickstart, you will learn how to create a VM on an Azure virtual network, a storage account with a Private Endpoint using the Azure portal. È quindi possibile accedere in modo sicuro all'account di archiviazione dalla macchina virtuale.

> [!NOTE]
> Gli endpoint privati non sono consentiti in combinazione con gli endpoint di servizio nella stessa subnet.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vm"></a>Creare una macchina virtuale
In questa sezione verranno creati la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato (un account di archiviazione in questo esempio).

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

In questa sezione verranno creati la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa Private Link.

In questa sezione è necessario sostituire i seguenti parametri nei passaggi con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<>nome-gruppo di risorse**  | myResourceGroup |
| **\<>nome-rete virtualeVirtual-network-name>** | myVirtualNetwork          |
| **\<>nome-regione**          | Stati Uniti centro-occidentali      |
| **\<>dello spazio indirizzo IPv4**   | 10.1.0.0\16          |
| **\<nome della>subnet**          | mySubnet        |
| **\<>subnet-address-range** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Creare macchina virtuale

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
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
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

## <a name="create-your-private-endpoint"></a>Creare l'endpoint privatoCreate your Private Endpoint
In questa sezione verrà creato un account di archiviazione privato usando un endpoint privato. 

1. Nella parte superiore sinistra della schermata nel portale di Azure selezionare Crea un**account**di**archiviazione** >  **delle risorse.** > 

1. In **Crea account di archiviazione - Nozioni di base**immettere o selezionare le informazioni seguente:In Create storage account - Basics , enter or select this information:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **DETTAGLI ISTANZA** |  |
    | Nome dell'account di archiviazione  | Immettere *mystorageaccount*. Se il nome è già usato, creare un nome univoco. |
    | Region | Selezionare **Stati Uniti centro-occidentali**. |
    | Prestazioni| Lasciare l'impostazione predefinita **Standard**. |
    | Tipo di account | Lasciare l'impostazione predefinita **Archiviazione (uso generale v2)**. |
    | Replica | Selezionare **Archiviazione con ridondanza geografica di accesso in lettura (RA-GRS)**. |
    |||
  
3. Selezionare **Avanti: Rete**.
4. In **Create a storage account - Networking**, metodo di connettività , selezionare Endpoint **privato**.
5. In **Create a storage account - Networking**selezionare Add Private **Endpoint**. 
6. In Crea endpoint privato immettere o selezionare le informazioni seguente:In **Create Private Endpoint**, enter or select this information:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    |Location|Selezionare **Stati Uniti centro-occidentali**.|
    |Nome|Immettere *myPrivateEndpoint*.  |
    |Sottorisorsa di archiviazione|Lasciare il **BLOB**predefinito . |
    | **Networking** |  |
    | Rete virtuale  | Selezionare *MyVirtualNetwork* dal gruppo di risorse *myResourceGroup*. |
    | Subnet | Selezionare *mySubnet*. |
    | **INTEGRAZIONE DNS PRIVATO**|  |
    | Integra con la zona DNS privato  | Lasciare il valore predefinito **Sì**. |
    | Zona DNS privato  | Lasciare il valore predefinito **(Nuovo) privatelink.blob.core.windows.net**. |
    |||
7. Selezionare **OK**. 
8. Selezionare **Revisione e creazione**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
9. Quando viene visualizzato il messaggio **Convalida superata** , selezionare **Crea**. 
10. Passare alla risorsa dell'account di archiviazione appena creata.
11. Selezionare **Chiavi di accesso** dal menu del contenuto a sinistra.
12. Selezionare Copia nella stringa di connessione per key1.Select **Copy** on the connection string for key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file *downloaded.rdp*.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un account diverso**per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-storage-account-privately-from-the-vm"></a>Accedere all'account di archiviazione privatamente dalla macchina virtualeAccess storage account privately from the VM

In questa sezione ci si connetterà privatamente all'account di archiviazione usando l'endpoint privato.

1. Nel Desktop remoto di *myVm1* aprire PowerShell.
2. Invio `nslookup mystorageaccount.blob.core.windows.net` Riceverai un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Installare [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Selezionare **Account di** archiviazione con il pulsante destro del mouse.
5. Selezionare **Connetti a un archivio azure**.
6. Selezionare **Usa una stringa**di connessione .
7. Fare clic su **Avanti**.
8. Immettere la stringa di connessione incollando le informazioni copiate in precedenza.
9. Fare clic su **Avanti**.
10. Selezionare **Connetti**.
11. Esplorare i contenitori BLOB da mystorageaccount 
12. (Opzionalmente) Creare cartelle e/o caricare file in *mystorageaccount*. 
13. Chiudere la connessione desktop remoto a *myVM*. 

Opzioni aggiuntive per accedere all'account di archiviazione:Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. È possibile installare l'applicazione per esplorare privatamente il contenuto dell'account di archiviazione. 
 
- L'utilità AzCopy è un'altra opzione per il trasferimento di dati scriptable ad alte prestazioni per l'archiviazione di Azure.The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Usare AzCopy per trasferire i dati da e verso risorse di archiviazione BLOB, file e tabelle. 


## <a name="clean-up-resources"></a>Pulire le risorse 
Al termine dell'uso dell'endpoint privato, dell'account di archiviazione e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse in esso contenute:When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Immettere *myResourceGroup* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca. 
2. Selezionare **Elimina gruppo di risorse**. 
3. Immettere *myResourceGroup* per **TYPE THE RESOURCE GROUP NAME** e selezionare **Delete**. 

## <a name="next-steps"></a>Passaggi successivi
In questa Guida introduttiva è stata creata una macchina virtuale in una rete virtuale e un account di archiviazione e un endpoint privato. È stata eseguita la connessione a una macchina virtuale da Internet e che è stata comunicata in modo sicuro all'account di archiviazione tramite Private Link.You connected to one VM from the internet and securely communicateed to the storage account using Private Link. Per altre informazioni sull'endpoint privato, vedere [Che cos'è l'endpoint privato di Azure?.](private-endpoint-overview.md)
