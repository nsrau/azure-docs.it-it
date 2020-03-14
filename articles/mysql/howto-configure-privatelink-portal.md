---
title: Collegamento privato-portale di Azure-database di Azure per MySQL
description: Informazioni su come configurare il collegamento privato per database di Azure per MySQL da portale di Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4a4824a9f8340b12bca7e18562d723eb24e58b71
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371920"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-portal"></a>Creare e gestire un collegamento privato per database di Azure per MySQL con il portale

Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. In questo articolo si apprenderà come usare il portale di Azure per creare una macchina virtuale in una rete virtuale di Azure e un database di Azure per il server MySQL con un endpoint privato di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui database di Azure per MySQL supporta i piani tariffari per utilizzo generico e con ottimizzazione per la memoria.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Creare una macchina virtuale di Azure

In questa sezione si creeranno la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato (un server MySQL in Azure).

### <a name="create-the-virtual-network"></a>Creare la rete virtuale
In questa sezione si creeranno la rete virtuale e la subnet che ospiteranno la VM usata per accedere alla risorsa Collegamento privato.

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
2. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Name | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Gruppo di risorse | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Location | Selezionare **Europa occidentale**.|
    | Subnet - Nome | Immettere *mySubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |
    |||
3. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.

### <a name="create-virtual-machine"></a>Creazione della macchina virtuale

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**.

2. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **Europa occidentale**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Conferma password | Reimmettere la password. |
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
    | Subscription | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **Dettagli server** |  |
    |Nome server  | Immettere *MyServer*. Se il nome è già usato, creare un nome univoco.|
    | Nome utente amministratore| Immettere un nome amministratore a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 8 caratteri e soddisfare i requisiti definiti. |
    | Location | Selezionare un'area di Azure in cui si vuole che il server MySQL si trovi. |
    |Versione  | Selezionare la versione del database del server MySQL richiesta.|
    | Calcolo e archiviazione| Selezionare il piano tariffario necessario per il server in base al carico di lavoro. |
    |||
 
7. Scegliere **OK**. 
8. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
9. Quando viene visualizzato il messaggio Convalida superata, selezionare **Crea**. 
10. Quando viene visualizzato il messaggio Convalida superata, selezionare Crea. 

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

In questa sezione si creerà un server MySQL e si aggiungerà un endpoint privato. 

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **rete** > **collegamento privato**.

2. In **Centro collegamento privato - Informazioni generali** selezionare **Avvia** per l'opzione **Crea una connessione privata a un servizio**.

    ![Panoramica sul collegamento privato](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. In **Crea un endpoint privato-nozioni di base**immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **Dettagli istanza** |  |
    | Name | Immettere *myPrivateEndpoint*. Se il nome è già usato, creare un nome univoco. |
    |Region|Selezionare **Europa occidentale**.|
    |||

5. Selezionare **Avanti: risorsa**.
6. In **Crea un endpoint privato - Risorsa** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |Metodo di connessione  | Selezionare Connettersi a una risorsa di Azure nella directory.|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft. DBforMySQL/Servers**. |
    | Resource |Selezionare *myServer*.|
    |Sottorisorsa di destinazione |Selezione di *SqlServer*|
    |||
7. Selezionare **Avanti: configurazione**.
8. In **Crea un endpoint privato-configurazione**immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |**RETE**| |
    | Rete virtuale| Selezionare *MyVirtualNetwork*. |
    | Subnet | Selezionare  *mySubnet*. |
    |**INTEGRAZIONE DNS PRIVATO**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Selezionare *(nuovo) privatelink. MySQL. database. Azure. com* |
    |||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
2. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**. 

    ![Collegamento privato creato](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

    > [!NOTE] 
    > Il nome di dominio completo nell'impostazione DNS del cliente non viene risolto nell'indirizzo IP privato configurato. Sarà necessario configurare una zona DNS per il nome di dominio completo configurato come illustrato di [seguito](../dns/dns-operations-recordsets-portal.md).

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

1. Scegliere **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Accedere al server MySQL privatamente dalla macchina virtuale

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

3. Testare la connessione del collegamento privato per il server MySQL usando un client disponibile. Nell'esempio seguente ho usato [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) per eseguire l'operazione.

4. In **nuova connessione**immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Tipo di server| Selezionare **MySQL**.|
    | Nome server| Seleziona *MyServer.privatelink.MySQL.database.Azure.com* |
    | Nome utente | Immettere username come username@servername fornito durante la creazione del server MySQL. |
    |Password |Immettere una password specificata durante la creazione del server MySQL. |
    |SSL|Selezionare **required**.|
    ||

5. Selezionare Connetti.

6. Esplorare i database dal menu a sinistra.

7. Facoltativamente Creare o eseguire query sulle informazioni dal server MySQL.

8. Chiudere la connessione Desktop remoto a myVm.

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine dell'operazione, eliminare il gruppo di risorse e tutte le risorse in esso contenute usando l'endpoint privato, il server MySQL e la macchina virtuale:

1. Immettere *myResourceGroup* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere myResourceGroup in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa procedura è stata creata una VM in una rete virtuale, un database di Azure per MySQL e un endpoint privato per l'accesso privato. È stata effettuata la connessione a una VM da Internet e la comunicazione sicura con il server MySQL con il collegamento privato. Per altre informazioni sugli endpoint privati, vedere [che cos'è endpoint privato di Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
