---
title: Avvio rapido - Gestire endpoint privati in Azure
description: Questa guida di avvio rapido illustra come creare un endpoint privato con il portale di Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 22614b28023a0628fb12c170e934aaab4d1ddfe4
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74899856"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Guida introduttiva: Creare un endpoint privato con il portale di Azure

Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. Questa guida di avvio rapido illustrerà come creare una VM in una rete virtuale di Azure e un server di database SQL con un endpoint privato di Azure usando il portale di Azure. Si potrà quindi accedere in modo sicuro al server di database SQL dalla VM.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


> [!NOTE]
> Gli endpoint privati non sono consentiti in combinazione con gli endpoint di servizio nella stessa subnet.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vm"></a>Creare una macchina virtuale
In questa sezione si creeranno la rete virtuale e la subnet che ospiteranno la VM usata per accedere alla risorsa Collegamento privato (in questo esempio, un server SQL in Azure).

### <a name="create-the-virtual-network"></a>Creare la rete virtuale


In questa sezione si creeranno la rete virtuale e la subnet che ospiteranno la VM usata per accedere alla risorsa Collegamento privato.

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Location | Selezionare **Stati Uniti centro-occidentali**.|
    | Subnet - Nome | Immettere *mySubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |
    |||
1. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.


### <a name="create-virtual-machine"></a>Creare la macchina virtuale

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**.

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **Stati Uniti centro-occidentali**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Image | Selezionare **Windows Server 2019 Datacenter**. |
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

## <a name="create-a-sql-database-server"></a>Creare un server di database SQL
In questa sezione si creerà un server di database SQL in Azure. 

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Database** > **Database SQL**.

1. In **Crea database SQL - Informazioni di base** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Dettagli database** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome database  | Immettere *mydatabase*. Se il nome è già usato, creare un nome univoco. |
    |||
5. In **Server** selezionare **Crea nuovo**. 
6. In **Nuovo server** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |Nome server  | Immettere *myserver*. Se il nome è già usato, creare un nome univoco.|
    | Accesso amministratore server| Immettere un nome amministratore a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 8 caratteri e soddisfare i requisiti definiti. |
    | Location | Selezionare un'area di Azure in cui si vuole che risieda il server SQL. |
    
7. Selezionare **OK**. 
8. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
9. Quando viene visualizzato il messaggio Convalida superata, selezionare **Crea**. 
10. Quando viene visualizzato il messaggio Convalida superata, selezionare Crea. 

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

In questa sezione si creerà un server SQL e vi si aggiungerà un endpoint privato. 

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Rete** > **Centro collegamento privato (anteprima)** .
2. In **Centro collegamento privato - Informazioni generali** selezionare **Avvia** per l'opzione **Crea una connessione privata a un servizio**.
1. In **Crea un endpoint privato (anteprima) - Informazioni di base** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **DETTAGLI DELL'ISTANZA** |  |
    | NOME | Immettere *myPrivateEndpoint*. Se il nome è già usato, creare un nome univoco. |
    |Region|Selezionare **Stati Uniti centro-occidentali**.|
    |||
5. Selezionare **Avanti: Risorsa**.
6. In **Crea un endpoint privato - Risorsa** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |Connection Method (Metodo di connessione)  | Selezionare Connettersi a una risorsa di Azure nella directory.|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft.Sql/servers**. |
    | Risorsa |Selezionare *myServer*.|
    |Sottorisorsa di destinazione |Selezionare *sqlServer*.|
    |||
7. Selezionare **Avanti: Configurazione**.
8. In **Crea un endpoint privato (anteprima) - Configurazione** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |**RETE**| |
    | Rete virtuale| Selezionare *MyVirtualNetwork*. |
    | Subnet | Selezionare  *mySubnet*. |
    |**INTEGRAZIONE DNS PRIVATO**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Selezionare *(Nuovo)privatelink.database.windows.net* |
    |||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
2. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**. 
 
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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Accedere al server di database SQL privatamente dalla VM

1. Nel desktop remoto di  *myVM* aprire PowerShell.

2. Immettere  `nslookup myserver.database.windows.net`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Installare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connetti a server** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Tipo di server| Selezionare **Motore di database**.|
    | Nome server| Selezionare *myserver.database.windows.net* |
    | Nome utente | Immettere un nome utente come username@servername, specificato durante la creazione di SQL Server. |
    |Password |Immettere una password specificata durante la creazione di SQL Server. |
    |Memorizza password|Selezionare **Sì**.|
    |||
1. Selezionare **Connessione**.
2. Esplorare i database dal menu a sinistra.
3. (Facoltativamente) Creare o eseguire query sulle informazioni di mydatabase.
4. Chiudere la connessione Desktop remoto a  *myVm*. 

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non si ha più bisogno dell'endpoint privato, del server SQL e della macchina virtuale, rimuovere il gruppo di risorse e tutte le risorse che contiene: 
1. Immettere *myResourceGroup* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca. 
2. Selezionare **Elimina gruppo di risorse**. 
3. Immettere myResourceGroup in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva sono stati creati una macchina virtuale in una rete virtuale, un server di database SQL e un endpoint privato per l'accesso privato. È stata effettuata la connessione a una macchina virtuale da Internet ed è stata stabilita una comunicazione al server di database SQL usando Collegamento privato. Per altre informazioni sugli endpoint privati, vedere [Informazioni sull'endpoint privato di Azure](private-endpoint-overview.md).
