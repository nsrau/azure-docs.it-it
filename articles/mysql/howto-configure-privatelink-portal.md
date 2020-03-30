---
title: Private Link - Azure portal - Azure Database for MySQL
description: Informazioni su come configurare il collegamento privato per il database di Azure per MySQL dal portale di AzureLearn how to configure private link for Azure Database for MySQL from Azure portal
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4a4824a9f8340b12bca7e18562d723eb24e58b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371920"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-portal"></a>Creare e gestire Private Link per Azure Database per MySQL tramite portaleCreate and manage Private Link for Azure Database for MySQL using Portal

Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. In questo articolo si apprenderà come usare il portale di Azure per creare una macchina virtuale in una rete virtuale di Azure e un database di Azure per il server MySQL con un endpoint privato di Azure.In this article, you will learn how to use the Azure portal to create a VM in an Azure Virtual Network and an Azure Database for MySQL server with an Azure private endpoint.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Azure Database for MySQL supporta i livelli dei prezzi Scopo generale e Ottimizzato per la memoria.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al [portale](https://portal.azure.com)di Azure .

## <a name="create-an-azure-vm"></a>Creare una macchina virtuale di Azure

In questa sezione verranno creati la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa Private Link (un server MySQL in Azure).

### <a name="create-the-virtual-network"></a>Creare la rete virtuale
In questa sezione si creeranno la rete virtuale e la subnet che ospiteranno la VM usata per accedere alla risorsa Collegamento privato.

1. Nella parte superiore sinistra dello schermo selezionare **Crea una risorsa** > Rete**virtuale****di rete** > .
2. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Location | Selezionare **Europa occidentale**.|
    | Subnet - Nome | Immettere *mySubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |
    |||
3. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.

### <a name="create-virtual-machine"></a>Creare la macchina virtuale

1. Nella parte superiore sinistra dello schermo nel portale di Azure selezionare Crea una**macchina virtuale**di**calcolo** >  **delle risorse** > .

2. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **Europa occidentale**. |
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
    |||


1. Selezionare **Revisione e creazione**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata** , selezionare **Crea**.

## <a name="create-an-azure-database-for-mysql"></a>Creare un database di Azure per MySQL

In this section, you will create an Azure Database for MySQL server in Azure. 

1. Nella parte superiore sinistra della schermata nel portale di Azure selezionare Crea un database di **risorse** > **Databases** > **Database di Azure per MySQL**.

1. In **Database di Azure per MySQL** queste informazioni vengono fornite le informazioni seguenti:In Azure Database for MySQL provide these information:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **Dettagli del server** |  |
    |Nome server  | Immettere *myServer*. Se il nome è già usato, creare un nome univoco.|
    | Nome utente amministratore| Immettere un nome amministratore a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 8 caratteri e soddisfare i requisiti definiti. |
    | Location | Selezionare un'area di Azure in cui si vuole voler risiedere MySQL Server.Select an Azure region where you want to want your MySQL Server to reside. |
    |Versione  | Selezionare la versione del database del server MySQL necessaria.|
    | Calcolo e stoccaggio| Selezionare il piano tariffario necessario per il server in base al carico di lavoro. |
    |||
 
7. Selezionare **OK**. 
8. Selezionare **Revisione e creazione**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
9. Quando viene visualizzato il messaggio Convalida superata , selezionare **Crea**. 
10. Quando viene visualizzato il messaggio Convalida superata, selezionare Crea. 

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

In questa sezione verrà creato un server MySQL e vi verrà aggiunto un endpoint privato. 

1. Nella parte superiore sinistra dello schermo nel portale di Azure selezionare **Crea una risorsa** > **Collegamento** > **privato**rete .

2. In **Centro collegamento privato - Informazioni generali** selezionare **Avvia** per l'opzione **Crea una connessione privata a un servizio**.

    ![Panoramica di Private Link](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. In **Create a private endpoint - Basics**immettere o selezionare le informazioni seguente:In Create a private endpoint - Basics , enter or select this information:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **Dettagli istanza** |  |
    | Nome | Immettere *myPrivateEndpoint*. Se il nome è già usato, creare un nome univoco. |
    |Region|Selezionare **Europa occidentale**.|
    |||

5. Selezionare **Avanti: Risorsa**.
6. In **Crea un endpoint privato - Risorsa** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    |Metodo di connessione  | Selezionare Connettersi a una risorsa di Azure nella directory.|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft.DBforMySQL/servers**. |
    | Risorsa |Selezionare *myServer*.|
    |Sottorisorsa di destinazione |Selezionare *mysqlServer*|
    |||
7. Selezionare **Avanti: Configurazione**.
8. In **Create a private endpoint - Configuration**immettere o selezionare le informazioni seguente:In Create a private endpoint - Configuration , enter or select this information:

    | Impostazione | valore |
    | ------- | ----- |
    |**Networking**| |
    | Rete virtuale| Selezionare *MyVirtualNetwork*. |
    | Subnet | Selezionare  *mySubnet*. |
    |**INTEGRAZIONE DNS PRIVATO**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Selezionare *(New)privatelink.mysql.database.azure.comSelect (New)privatelink.mysql.database.azure.com* |
    |||

1. Selezionare **Revisione e creazione**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
2. Quando viene visualizzato il messaggio **Convalida superata** , selezionare **Crea**. 

    ![Collegamento privato creato](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

    > [!NOTE] 
    > Il nome di dominio completo nell'impostazione DNS del cliente non viene risolto nell'IP privato configurato. È necessario configurare una zona DNS per il nome di dominio completo configurato, come illustrato [di seguito.](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Connettersi a una VM con Desktop remoto (RDP)


Dopo aver creato **myVm**, connettersi alla macchina virtuale da Internet come illustrato di seguito: 

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

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Accedere al server MySQL privatamente dalla macchina virtualeAccess the MySQL server privately from the VM

1. Nel desktop remoto di  *myVM* aprire PowerShell.

2. Immettere  `nslookup  myServer.privatelink.mysql.database.azure.com`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Testare la connessione di collegamento privato per il server MySQL utilizzando qualsiasi client disponibile. Nell'esempio seguente ho usato [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) per eseguire l'operazione.

4. In **Nuova connessione**immettere o selezionare le informazioni seguente:

    | Impostazione | valore |
    | ------- | ----- |
    | Tipo di server| Selezionare **MySQL**.|
    | Nome server| Selezionare *myServer.privatelink.mysql.database.azure.com* |
    | Nome utente | Immettere username@servername username come fornito durante la creazione del server MySQL. |
    |Password |Immettere una password fornita durante la creazione del server MySQL. |
    |SSL|Selezionare **Obbligatorio**.|
    ||

5. Selezionare Connetti.

6. Esplorare i database dal menu a sinistra.

7. (Opzionalmente) Creare o eseguire query sulle informazioni dal server MySQL.

8. Chiudere la connessione desktop remoto a myVm.

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine dell'utilizzo dell'endpoint privato, del server MySQL e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse che contiene:When you're done using the private endpoint, MySQL server, and the VM, delete the resource group and all of the resources it contains:

1. Immettere *myResourceGroup* nella casella **di ricerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere myResourceGroup per **TYPE THE RESOURCE GROUP NAME** e selezionare **Delete**.

## <a name="next-steps"></a>Passaggi successivi

In questa procedura è stata creata una macchina virtuale in una rete virtuale, un database di Azure per MySQL e un endpoint privato per l'accesso privato. Ti sei connesso a una macchina virtuale da Internet e comunicati in modo sicuro al server MySQL usando Private Link. Per altre informazioni sugli endpoint privati, vedere [Che cos'è l'endpoint privato](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)di Azure.To learn more about private endpoints, see What is Azure private endpoint.
