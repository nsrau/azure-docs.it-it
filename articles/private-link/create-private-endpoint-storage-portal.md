---
title: Connettersi privatamente a un account di archiviazione usando un endpoint privato di Azure
description: Informazioni su come connettersi privatamente a un account di archiviazione in Azure usando un endpoint privato.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: d11e035be2e343128caaadcb0b1b2c4a24165330
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091080"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connettersi privatamente a un account di archiviazione usando un endpoint privato di Azure
Endpoint privato di Azure è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse di collegamento privato.

In questa Guida introduttiva si apprenderà come creare una VM in una rete virtuale di Azure, un account di archiviazione con un endpoint privato usando il portale di Azure. Quindi, è possibile accedere in modo sicuro all'account di archiviazione dalla macchina virtuale.


## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vm"></a>Creare una macchina virtuale
In questa sezione si creeranno la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato (un account di archiviazione in questo esempio).

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si creeranno la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato.

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Name | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Sottoscrizione | Selezionare la propria sottoscrizione.|
    | Gruppo di risorse | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Location | Selezionare **WestCentralUS**.|
    | Subnet - Nome | Immettere la *subnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |
    |||
1. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.


### <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **calcolo** > **macchina virtuale**.

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | Value |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome della macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **WestCentralUS**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Image | Selezionare **Windows Server 2019 datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Nome utente | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Conferma password | Reimmettere la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: Dischi**.

1. In **creare una macchina virtuale-dischi**lasciare le impostazioni predefinite e selezionare **avanti: Rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Rete virtuale | Lasciare il valore predefinito **MyVirtualNetwork**.  |
    | Spazio degli indirizzi | Lasciare il valore predefinito **10.1.0.0/24**.|
    | Subnet | Lasciare la **subnet predefinita (10.1.0.0/24)** .|
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**.|
    ||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

## <a name="create-your-private-endpoint"></a>Creare l'endpoint privato
In questa sezione verrà creato un account di archiviazione privato utilizzando un endpoint privato. 

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **archiviazione** > **account di archiviazione**.

1. In **create storage account-Nozioni di base**immettere o selezionare queste informazioni:

    | Impostazione | Value |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome account di archiviazione  | Immettere *mystorageaccount*. Se il nome è già usato, creare un nome univoco. |
    | Region | Selezionare **WestCentralUS**. |
    | Prestazioni| Lasciare l'impostazione predefinita **Standard**. |
    | Tipologia account | Lasciare la risorsa di **archiviazione predefinita (utilizzo generico v2)** . |
    | Replica | Selezionare **archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)** . |
    |||
  
3. Selezionare **avanti: Rete**.
4. In **creare un account di archiviazione-rete**, metodo di connettività selezionare **endpoint privato**.
5. In **creare un account di archiviazione-rete**selezionare **Aggiungi endpoint privato**. 
6. In **Crea endpoint privato**immettere o selezionare queste informazioni:

    | Impostazione | Value |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    |Location|Selezionare **WestCentralUS**.|
    |Name|Immettere *myPrivateEndpoint*.  |
    |Sottorisorsa di archiviazione|Lasciare il **BLOB**predefinito. |
    | **RETE** |  |
    | Rete virtuale  | Selezionare *MyVirtualNetwork* dal gruppo di risorse *myResourceGroup*. |
    | Subnet | Selezionare la *subnet*. |
    | **INTEGRAZIONE DNS PRIVATA**|  |
    | Eseguire l'integrazione con la zona DNS privata  | Lasciare l'impostazione predefinita **Sì**. |
    | Area DNS privato  | Lasciare il valore predefinito  ** (nuovo) privatelink.blob.Core.Windows.NET**. |
    |||
7. Fare clic su **OK**. 
8. Selezionare **Verifica + crea**. Viene rilevata la pagina **Verifica e crea** in cui Azure convalida la configurazione. 
9. Quando viene visualizzato il messaggio **convalida superata** , selezionare **Crea**. 
10. Passare alla risorsa dell'account di archiviazione appena creata.
11. Selezionare **chiavi di accesso** dal menu contenuto a sinistra.
12. Selezionare **copia** nella stringa di connessione per Key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione RDP scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-storage-account-privately-from-the-vm"></a>Accedere a un account di archiviazione privatamente dalla macchina virtuale

In questa sezione si effettuerà la connessione privata con l'account di archiviazione usando l'endpoint privato.

> [!IMPORTANT]
> Per la configurazione DNS per l'archiviazione è necessaria una modifica manuale del file hosts per includere il nome di dominio completo dell'account specifico. modificare il file seguente usando le autorizzazioni di amministratore per Windows: c:\Windows\System32\Drivers\etc\hosts o Linux/hosts Includere le informazioni DNS per l'account del passaggio precedente nel formato seguente [indirizzo IP privato] myaccount.blob.core.windows.net

1. Nel Desktop remoto di *myVM*aprire PowerShell.
2. Immettere `nslookup mystorageaccount.blob.core.windows.net` verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
