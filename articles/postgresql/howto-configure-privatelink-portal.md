---
title: Collegamento privato-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Informazioni su come configurare un collegamento privato per database di Azure per PostgreSQL-server singolo da portale di Azure
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 72dcf95c8ae8d8da34532fa96e3bf0371f5112fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370917"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Creare e gestire un collegamento privato per database di Azure per PostgreSQL-server singolo con il portale

Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato.  Questo articolo illustra come usare la portale di Azure per creare una VM in una rete virtuale di Azure e un singolo server di database di Azure per PostgreSQL con un endpoint privato di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui il server singolo database di Azure per PostgreSQL supporta i piani tariffari per utilizzo generico e con ottimizzazione per la memoria.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Creare una macchina virtuale di Azure

In questa sezione si creeranno la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato (un server PostgreSQL in Azure).

### <a name="create-the-virtual-network"></a>Creare la rete virtuale
In questa sezione si creeranno la rete virtuale e la subnet che ospiteranno la VM usata per accedere alla risorsa Collegamento privato.

1. Sul lato superiore sinistro della schermata selezionare **Crea una risorsa** > **Networking** > **rete rete virtuale**.
2. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Percorso | Selezionare **Europa occidentale**.|
    | Subnet - Nome | Immettere *mySubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |
    |||
3. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.

### <a name="create-virtual-machine"></a>Creare la macchina virtuale

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**.

2. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **Europa occidentale**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: Dischi**.

1. In **Crea macchina virtuale - Dischi** mantenere le impostazioni predefinite e selezionare **Avanti: Rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | valore |
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

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Creare un server singolo per database di Azure per PostgreSQL

In questa sezione verrà creato un database di Azure per il server PostgreSQL in Azure. 

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **Databases** > database di**Azure per PostgreSQL**.

1. Nell' **opzione di distribuzione database di Azure per PostgreSQL**selezionare **server singolo** e specificare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **Dettagli del server** |  |
    |Nome server  | Immettere *myserver*. Se il nome è già usato, creare un nome univoco.|
    | Nome utente amministratore| Immettere un nome amministratore a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 8 caratteri e soddisfare i requisiti definiti. |
    | Location | Selezionare un'area di Azure in cui si vuole che il server PostgreSQL si trovi. |
    |Versione  | Selezionare la versione del database del server PostgreSQL richiesta.|
    | Calcolo e archiviazione| Selezionare il piano tariffario necessario per il server in base al carico di lavoro. |
    |||
 
7. Selezionare **OK**. 
8. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
9. Quando viene visualizzato il messaggio convalida superata, selezionare **Crea**. 
10. Quando viene visualizzato il messaggio Convalida superata, selezionare Crea. 

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

In questa sezione si creerà un server PostgreSQL a cui verrà aggiunto un endpoint privato. 

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **rete** > **privato collegamento**.
2. In **Centro collegamento privato - Informazioni generali** selezionare **Avvia** per l'opzione **Crea una connessione privata a un servizio**.

    ![Panoramica sul collegamento privato](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. In **Crea un endpoint privato-nozioni di base**immettere o selezionare queste informazioni:

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
    | Tipo di risorsa | Selezionare **Microsoft. DBforPostgreSQL/Servers**. |
    | Risorsa |Selezionare *myServer*.|
    |Sottorisorsa di destinazione |Seleziona *postgresqlServer*|
    |||
7. Selezionare **Avanti: Configurazione**.
8. In **Crea un endpoint privato-configurazione**immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    |**RETE**| |
    | Rete virtuale| Selezionare *MyVirtualNetwork*. |
    | Subnet | Selezionare  *mySubnet*. |
    |**INTEGRAZIONE DNS PRIVATO**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Select *(nuovo) privatelink. postgres. database. Azure. com* |
    |||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
2. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**. 

    ![Collegamento privato creato](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

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

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Accedere al server PostgreSQL privatamente dalla macchina virtuale

1. Nel desktop remoto di  *myVM* aprire PowerShell.

2. Immettere  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Testare la connessione del collegamento privato per il server PostgreSQL usando un client disponibile. Nell'esempio seguente è stato usato [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) per eseguire l'operazione.

4. In **nuova connessione**immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Tipo di server| Selezionare **PostgreSQL**.|
    | Nome server| Seleziona *mydemopostgresserver.privatelink.postgres.database.Azure.com* |
    | Nome utente | Immettere username ( username@servername nome utente) come specificato durante la creazione del server PostgreSQL. |
    |Password |Immettere una password specificata durante la creazione del server PostgreSQL. |
    |SSL|Selezionare **required**.|
    ||

5. Selezionare Connetti.

6. Esplorare i database dal menu a sinistra.

7. Facoltativamente Creare o eseguire query sulle informazioni dal server postgreSQL.

8. Chiudere la connessione Desktop remoto a myVm.

## <a name="clean-up-resources"></a>Pulizia delle risorse
Al termine dell'uso dell'endpoint privato, del server PostgreSQL e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere myResourceGroup per **digitare il nome del gruppo di risorse** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa procedura è stata creata una VM in una rete virtuale, un database di Azure per PostgreSQL-server singolo e un endpoint privato per l'accesso privato. È stata effettuata la connessione a una VM da Internet e la comunicazione protetta con il server PostgreSQL è stata effettuata tramite un collegamento privato. Per altre informazioni sugli endpoint privati, vedere [che cos'è endpoint privato di Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
