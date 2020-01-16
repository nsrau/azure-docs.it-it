---
title: Configurare la replica transazionale tra due istanze gestite e SQL Server
description: In questa esercitazione viene configurata la replica tra un'istanza gestita del server di pubblicazione, un'istanza gestita del server di distribuzione e un sottoscrittore di SQL Server in una VM di Azure, insieme ai componenti di rete necessari come una zona DNS privato e il peering VPN.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: e1ee616adadcabb2deb3fa08a5f3d4f768fddfd5
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045658"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Esercitazione: Configurare la replica transazionale tra due istanze gestite e SQL Server


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> - Configurare un'istanza gestita come server di pubblicazione di replica. 
> - Configurare un'istanza gestita come server di distribuzione di replica. 
> - Configurare un'istanza di SQL Server come sottoscrittore. 

![Replica tra un server di pubblicazione di istanza gestita SQL, un server di distribuzione di istanza gestita SQL e un sottoscrittore di SQL Server](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Questa esercitazione è destinata a un pubblico esperto e presuppone che l'utente abbia familiarità con la distribuzione e la connessione a entrambe le istanze gestite e con le VM di SQL Server in Azure. Di conseguenza, in questa esercitazione vengono ignorati alcuni passaggi. 

Per altre informazioni, vedere gli articoli sulla [panoramica delle istanze gestite di Database SQL di Azure](sql-database-managed-instance-index.yml), sulle [funzionalità](sql-database-managed-instance.md) e sulla [replica transazionale SQL](sql-database-managed-instance-transactional-replication.md).

Per configurare la replica tra un server di pubblicazione di istanza gestita e un sottoscrittore di istanza gestita, vedere [Configurare la replica transazionale tra due istanze gestite](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Prerequisites

Per completare questa esercitazione, verificare di avere i prerequisiti seguenti:

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/). 
- Esperienza nella distribuzione di due istanze gestite all'interno della stessa rete virtuale. 
- Un sottoscrittore di SQL Server, in locale o in una VM di Azure. Questa esercitazione usa una VM di Azure.  
- [SQL Server Management Studio (SSMS) 18.0 o versione successiva](/sql/ssms/download-sql-server-management-studio-ssms).
- La versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Le porte 445 e 1433 consentono il traffico SQL su Firewall di Azure e Windows Firewall. 

## <a name="1---create-the-resource-group"></a>1 - Creare il gruppo di risorse
Usare il frammento di codice di PowerShell seguente per creare un nuovo gruppo di risorse:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - Creare due istanze gestite
Creare due istanze gestite all'interno di questo nuovo gruppo di risorse usando il [portale di Azure](https://portal.azure.com). 

- Il nome dell'istanza gestita del server di pubblicazione deve essere `sql-mi-publisher` (oltre ad alcuni caratteri per la sequenza casuale) e il nome della rete virtuale deve essere `vnet-sql-mi-publisher`.
- Il nome dell'istanza gestita del server di distribuzione deve essere `sql-mi-distributor` (oltre ad alcuni caratteri per la sequenza casuale) e deve trovarsi _nella stessa rete virtuale dell'istanza gestita del server di pubblicazione_.

   ![Usare la rete virtuale del server di pubblicazione per il server di distribuzione](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Per altre informazioni sulla creazione di un'istanza gestita, vedere [Creare un'istanza gestita nel portale](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > Per semplicità, e poiché si tratta della configurazione più comune, questa esercitazione suggerisce di inserire l'istanza del server di distribuzione nella stessa rete virtuale del server di pubblicazione. È tuttavia possibile creare il server di distribuzione in una rete virtuale distinta. A tale scopo, sarà necessario configurare il peering VPN tra la rete virtuale del server di pubblicazione e quella del server di distribuzione e quindi tra la rete virtuale del server di distribuzione e quella del sottoscrittore. 

## <a name="3---create-a-sql-server-vm"></a>3 - Creare una VM di SQL Server
Creare una macchina virtuale di SQL server usando il [portale di Azure](https://portal.azure.com). La macchina virtuale di SQL Server deve avere le caratteristiche seguenti:

- Nome: `sql-vm-sub`
- Immagine: SQL Server 2016 o versione successiva
- Gruppo di risorse: uguale a quello dell'istanza gestita
- Rete virtuale: `sql-vm-sub-vnet` 

Per altre informazioni sulla distribuzione di una VM di SQL Server in Azure, vedere [Avvio rapido: Creare una VM di SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4 - Configurare il peering VPN
Configurare il peering VPN per consentire la comunicazione tra la rete virtuale delle due istanze gestite e la rete virtuale di SQL Server. A questo scopo, usare questo frammento di codice di PowerShell:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet 

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Una volta stabilito il peering VPN, testare la connettività avviando SQL Server Management Studio (SSMS) in SQL Server e connettendosi a entrambe le istanze gestite. Per altre informazioni sulla connessione a un'istanza gestita con SSMS, vedere [Usare SSMS per connettersi all'istanza gestita](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance). 

![Testare la connettività alle istanze gestite](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - Creare la zona DNS privato

Una zona DNS privato consente il routing DNS tra le istanze gestite e SQL Server. 

### <a name="create-private-dns-zone"></a>Creare la zona DNS privato
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa** per creare una nuova risorsa di Azure. 
1. Cercare `private dns zone` in Azure Marketplace. 
1. Scegliere la risorsa **Zona DNS privato** pubblicata da Microsoft e quindi selezionare **Crea** per creare la zona DNS. 
1. Scegliere la sottoscrizione e il gruppo di risorse dal menu a discesa. 
1. Specificare un nome arbitrario per la zona DNS, ad esempio `repldns.com`. 

   ![Creare la zona DNS privato](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Selezionare **Rivedi e crea**. Rivedere i parametri della zona DNS privato e quindi selezionare **Crea** per creare la risorsa. 

### <a name="create-a-record"></a>Creare il record A

1. Passare alla nuova **zona DNS privato** e selezionare **Panoramica**. 
1. Selezionare **+ Set di record** per creare un nuovo record A. 
1. Specificare il nome della VM di SQL Server oltre all'indirizzo IP interno privato. 

   ![Configurare il record A](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Selezionare **OK** per creare il record A. 

### <a name="link-the-virtual-network"></a>Collegare la rete virtuale

1. Passare alla nuova **zona DNS privato** e selezionare **Collegamenti di rete virtuale**. 
1. Selezionare **+ Aggiungi**. 
1. Specificare un nome per il collegamento, ad esempio `Pub-link`. 
1. Scegliere la sottoscrizione dal menu a discesa e quindi selezionare la rete virtuale per l'istanza gestita del server di pubblicazione. 
1. Fare clic sulla casella accanto a **Abilita registrazione automatica**. 

   ![Creare un collegamento di rete virtuale](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Selezionare **OK** per collegarsi alla rete virtuale. 
1. Ripetere questi passaggi per aggiungere un collegamento per la rete virtuale del server di sottoscrizione, con un nome come `Sub-link`. 


## <a name="6---create-azure-storage-account"></a>6 - Creare l'account di archiviazione di Azure

[Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) per la directory di lavoro e quindi creare una [condivisione file](../storage/files/storage-how-to-create-file-share.md) al suo interno. 

Copiare il percorso della condivisione file nel formato `\\storage-account-name.file.core.windows.net\file-share-name`   

Esempio: `\\replstorage.file.core.windows.net\replshare`

Copiare la stringa di connessione della chiave di accesso alle risorse di archiviazione nel formato `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Esempio: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-keys-manage.md). 


## <a name="7---create-a-database"></a>7 - Creare un database
Creare un nuovo database nell'istanza gestita del server di pubblicazione. A questo scopo, attenersi alla procedura seguente:

1. Avviare SQL Server Management Studio in SQL Server. 
1. Connettersi all'istanza gestita `sql-mi-publisher`. 
1. Aprire una finestra **Nuova query** ed eseguire la query T-SQL seguente per creare il database:

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - Configurare la distribuzione 
Una volta stabilita la connettività e creato un database di esempio, è possibile configurare la distribuzione nell'istanza gestita `sql-mi-distributor`. A questo scopo, attenersi alla procedura seguente:

1. Avviare SQL Server Management Studio in SQL Server. 
1. Connettersi all'istanza gestita `sql-mi-distributor`. 
1. Aprire una finestra **Nuova query** ed eseguire il codice Transact-SQL seguente per configurare la distribuzione nell'istanza gestita del server di distribuzione: 

   ```sql
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Assicurarsi di usare solo barre rovesciate (`\`) per il parametro @working_directory. L'uso di una barra (`/`) può generare un errore durante la connessione alla condivisione file. 

1. Connettersi all'istanza gestita `sql-mi-publisher`. 
1. Aprire una finestra **Nuova query** ed eseguire il codice Transact-SQL seguente per registrare il server di distribuzione nel server di pubblicazione: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - Creare la pubblicazione
Una volta configurata la distribuzione, è ora possibile creare la pubblicazione. A questo scopo, attenersi alla procedura seguente: 

1. Avviare SQL Server Management Studio in SQL Server. 
1. Connettersi all'istanza gestita `sql-mi-publisher`. 
1. In **Esplora oggetti** espandere il nodo **Replica** e fare clic con il pulsante destro del mouse sulla cartella **Local Publication**. Selezionare **Nuova pubblicazione**. 
1. Selezionare **Avanti** per uscire dalla pagina iniziale. 
1. Nella pagina **Database di pubblicazione** selezionare il database `ReplTutorial` creato in precedenza. Selezionare **Avanti**. 
1. Nella pagina **Tipo di pubblicazione** selezionare **Pubblicazione transazionale**. Selezionare **Avanti**. 
1. Nella pagina **Articoli** selezionare la casella accanto a **Tabelle**. Selezionare **Avanti**. 
1. Nella pagina **Filtro righe tabella** selezionare **Avanti** senza aggiungere alcun filtro. 
1. Nella pagina **Agente snapshot** selezionare la casella di controllo **Crea uno snapshot immediatamente e mantieni lo snapshot disponibile per l'inizializzazione delle sottoscrizioni**. Selezionare **Avanti**. 
1. Nella pagina **Sicurezza agente** selezionare **Impostazioni di sicurezza**. Specificare le credenziali di accesso a SQL Server da usare per l'agente di snapshot e per connettersi al server di pubblicazione. Selezionare **OK** per chiudere la pagina **Sicurezza agente snapshot**. Selezionare **Avanti**. 

   ![Configurare la sicurezza dell'agente di snapshot](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Nella pagina **Azioni procedura guidata** scegliere **Crea la pubblicazione** e, facoltativamente, **Genera un file script con i passaggi per la creazione della pubblicazione**, se si vuole salvare lo script per un uso successivo. 
1. Nella pagina **Completare la procedura guidata** assegnare alla pubblicazione il nome `ReplTest` e selezionare **Avanti** per creare la pubblicazione. 
1. Dopo aver creato la pubblicazione, aggiornare il nodo **Replica** in **Esplora oggetti** ed espandere **Pubblicazioni locali** per visualizzare la nuova pubblicazione. 


## <a name="10---create-the-subscription"></a>10 - Creare la sottoscrizione 

Una volta creata la pubblicazione, è possibile creare la sottoscrizione. A questo scopo, attenersi alla procedura seguente: 

1. Avviare SQL Server Management Studio in SQL Server. 
1. Connettersi all'istanza gestita `sql-mi-publisher`. 
1. Aprire una finestra **Nuova query** ed eseguire il codice Transact-SQL seguente per aggiungere l'agente di sottoscrizione e distribuzione. Usare il DNS come parte del nome del sottoscrittore. 

```sql
use [ReplTutorial]
exec sp_addsubscription 
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub', 
@subscription_type = N'Push', 
@sync_type = N'automatic', 
@article = N'all', 
@update_mode = N'read only', 
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub', 
@job_login = N'azureuser', 
@job_password = '<Complex Password>', 
@subscriber_security_mode = 0, 
@subscriber_login = N'azureuser', 
@subscriber_password = '<Complex Password>', 
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - Testare la replica 

Dopo aver configurato la replica, è possibile testarla inserendo nuovi elementi nel server di pubblicazione e osservando le modifiche che si propagano nel sottoscrittore. 

Eseguire il frammento di codice T-SQL seguente per visualizzare le righe nel sottoscrittore:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Eseguire il frammento di codice T-SQL seguente per inserire righe aggiuntive nel server di pubblicazione, quindi controllarle di nuovo nel sottoscrittore. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Pulire le risorse

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com). 
1. Selezionare l'istanza o le istanze gestite, quindi selezionare **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. L'esecuzione di questa procedura in background può richiedere del tempo e, finché non viene completata, non sarà possibile eliminare il *cluster virtuale* o altre risorse dipendenti. Monitorare l'eliminazione nella scheda Attività per verificare che l'istanza gestita sia stata eliminata. 
1. Una volta eliminata l'istanza gestita, eliminare il *cluster virtuale* selezionandolo nel gruppo di risorse e quindi scegliendo **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. 
1. Eliminare eventuali risorse rimanenti. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. 
1. Eliminare il gruppo di risorse selezionando **Elimina gruppo di risorse**, digitando il relativo nome, `myResourceGroup`, quindi selezionando **Elimina**. 

## <a name="known-errors"></a>Errori noti

### <a name="windows-logins-are-not-supported"></a>Gli account di accesso di Windows non sono supportati

`Exception Message: Windows logins are not supported in this version of SQL Server.`

L'agente è stato configurato con un account di accesso di Windows ed è necessario usare invece un account di accesso di SQL server. Usare la pagina **Sicurezza agente** di **Proprietà pubblicazione** per sostituire le credenziali di accesso con quelle di SQL Server. 


### <a name="failed-to-connect-to-azure-storage"></a>Non è possibile connettersi ad Archiviazione di Azure


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Ottenuta la stringa di connessione di Archiviazione di Azure per replstorage 2019-11-19 02:21:05.07 Connessione ad Archiviazione di Azure '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Non è stato possibile connettersi all'archiviazione di Azure " con errore del sistema operativo: 53.


Questo problema è probabilmente dovuto al fatto che la porta 445 è chiusa in Firewall di Azure, Windows Firewall o entrambi. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

L'errore può essere causato dall'uso di una barra al posto di una barra rovesciata nel percorso della condivisione file. Questo è il formato corretto: `\\replstorage.file.core.windows.net\replshare` Questo formato può generare un errore 55 del sistema operativo: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Non è stato possibile connettersi al sottoscrittore

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Possibili soluzioni:
- Assicurarsi che la porta 1433 sia aperta. 
- Assicurarsi che TCP/IP sia abilitato nel sottoscrittore. 
- Verificare che sia stato usato il nome DNS quando è stato creato il sottoscrittore. 
- Verificare che le reti virtuali siano correttamente collegate nella zona DNS privato. 
- Verificare che il record A sia configurato correttamente. 
- Verificare che il peering VPN sia configurato correttamente. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Nessuna pubblicazione disponibile per la sottoscrizione

Quando si aggiunge una nuova sottoscrizione tramite la procedura guidata **Nuova sottoscrizione**, nella pagina **Pubblicazione** è possibile che non siano presenti database e pubblicazioni come opzioni disponibili e che venga visualizzato il messaggio di errore seguente:

`There are no publications to which yuo can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Anche se è possibile che questo messaggio di errore sia accurato e che non ci siano effettivamente pubblicazioni disponibili nel server di pubblicazione a cui si è connessi o che non si abbiano autorizzazioni sufficienti, questo errore può essere causato anche da una versione meno recente di SQL Server Management Studio. Provare a eseguire l'aggiornamento a SQL Server Management Studio 18.0 o versione successiva per escludere che sia questa la causa radice. 


## <a name="next-steps"></a>Passaggi successivi

### <a name="enable-security-features"></a>Abilitare le funzionalità di sicurezza

Vedere l'articolo seguente sulle [funzionalità di sicurezza delle istanze gestite](sql-database-managed-instance.md#azure-sql-database-security-features) per un elenco completo di soluzioni per proteggere il database. Sono illustrate le funzionalità di sicurezza seguenti:

- [Controllo delle istanze gestite](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Introduzione al rilevamento delle minacce](sql-database-managed-instance-threat-detection.md) 
- [Maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking)
- [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) 
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funzionalità dell'istanza gestita

Per una panoramica completa delle funzionalità delle istanze gestite, vedere:

> [!div class="nextstepaction"]
> [Capacità di Istanza gestita](sql-database-managed-instance.md)
