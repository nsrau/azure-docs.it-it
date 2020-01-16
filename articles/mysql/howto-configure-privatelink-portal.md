---
title: Collegamento privato per il metodo di installazione del portale di database di Azure per MySQL (anteprima)
description: Informazioni su come configurare il collegamento privato per database di Azure per MySQL da portale di Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 1c721685e12c417116888ccc3cf8d25123761933
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978634"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-portal"></a>Creare e gestire un collegamento privato per database di Azure per MySQL (anteprima) con il portale

Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. In questo articolo si apprenderà come usare il portale di Azure per creare una macchina virtuale in una rete virtuale di Azure e un database di Azure per il server MySQL con un endpoint privato di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui database di Azure per MySQL supporta i piani tariffari per utilizzo generico e con ottimizzazione per la memoria.

## <a name="sign-in-to-azure"></a>Accedere a Azure
Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Creare una macchina virtuale di Azure

In questa sezione si creeranno la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato (un server MySQL in Azure).

### <a name="create-the-virtual-network"></a>Creare la rete virtuale
In questa sezione si creeranno la rete virtuale e la subnet che ospiteranno la VM usata per accedere alla risorsa Collegamento privato.

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
2. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Sottoscrizione | Selezionare la propria sottoscrizione.|
    | Gruppo di risorse | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Percorso | Selezionare **Europa occidentale**.|
    | Subnet - Nome | Immettere *mySubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |
    |||
3. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.

### <a name="create-virtual-machine"></a>Crea una macchina virtuale

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**.

2. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Area | Selezionare **Europa occidentale**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensioni | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: dischi**.

1. In **creare una macchina virtuale-dischi**lasciare le impostazioni predefinite e selezionare **Avanti: rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Rete virtuale | Lasciare l'impostazione predefinita **MyVirtualNetwork**.  |
    | Spazio degli indirizzi | Lasciare l'impostazione predefinita **10.1.0.0/24**.|
    | Subnet | Lasciare l'impostazione predefinita **mySubnet (10.1.0.0/24)** .|
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**.|
    |||


1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

## <a name="create-an-azure-database-for-mysql"></a>Creare un database di Azure per MySQL

In questa sezione verrà creato un database di Azure per il server MySQL in Azure. 

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** ** > database** > **database di Azure per MySQL**.

1. In **database di Azure per MySQL** fornire queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **Dettagli server** |  |
    |Nome server  | Immettere *MyServer*. Se il nome è già usato, creare un nome univoco.|
    | Nome utente amministratore| Immettere un nome amministratore a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 8 caratteri e soddisfare i requisiti definiti. |
    | Percorso | Selezionare un'area di Azure in cui si vuole che il server MySQL si trovi. |
    |Versione  | Selezionare la versione del database del server MySQL richiesta.|
    | Calcolo e archiviazione| Selezionare il piano tariffario necessario per il server in base al carico di lavoro. |
    |||
 
7. Selezionare **OK**. 
8. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
9. Quando viene visualizzato il messaggio Convalida superata, selezionare **Crea**. 
10. Quando viene visualizzato il messaggio Convalida superata, selezionare Crea. 

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

In questa sezione si creerà un server MySQL e si aggiungerà un endpoint privato. 

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Rete** > **Centro collegamento privato (anteprima)** .

2. In **Centro collegamento privato - Informazioni generali** selezionare **Avvia** per l'opzione **Crea una connessione privata a un servizio**.

    ![Panoramica sul collegamento privato](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. In **Crea un endpoint privato (anteprima) - Informazioni di base** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **Dettagli istanza** |  |
    | Nome | Immettere *myPrivateEndpoint*. Se il nome è già usato, creare un nome univoco. |
    |Area|Selezionare **Europa occidentale**.|
    |||

5. Selezionare **Avanti: risorsa**.
6. In **Crea un endpoint privato - Risorsa** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |Metodo di connessione  | Selezionare Connettersi a una risorsa di Azure nella directory.|
    | Sottoscrizione| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft. DBforMySQL/Servers**. |
    | Gruppi |Selezionare *myServer*.|
    |Sottorisorsa di destinazione |Selezione di *SqlServer*|
    |||
7. Selezionare **Avanti: configurazione**.
8. In **Crea un endpoint privato (anteprima) - Configurazione** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |**RETE**| |
    | Rete virtuale| Selezionare *MyVirtualNetwork*. |
    | Subnet | Selezionare  *mySubnet*. |
    |**INTEGRAZIONE DNS PRIVATO**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Select *(nuovo) privatelink. database. Azure. com* |
    |||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
2. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**. 

    ![Collegamento privato creato](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Connettersi a una VM con Desktop remoto (RDP)


Dopo aver creato **myVm**, connettersi alla macchina virtuale da Internet come illustrato di seguito: 

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file *downloaded.rdp*.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Accedere al server MySQL privatamente dalla macchina virtuale

1. Nel desktop remoto di  *myVM* aprire PowerShell.

2. Immettere  `nslookup  myServer.mysql.privatelink.database.azure.com`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.mysql.privatelink.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MySQL server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.

4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **MySQL**.|
    | Server name| Select *myServer.mysql.privatelink.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the MySQL server creation. |
    |Password |Enter a password provided during the MySQL server creation. |
    |SSL|Select **Required**.|
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the MySQL server.

8. Close the remote desktop connection to myVm.

## Clean up resources
When you're done using the private endpoint, MySQL server, and the VM, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.
2. Select **Delete resource group**.
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this how-to, you created a VM on a virtual network, an Azure Database for MySQL, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the MySQL server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).