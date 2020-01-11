---
title: Importare o esportare un database SQL
description: Importare o esportare un database SQL di Azure senza consentire ai servizi di Azure di accedere al server.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897845"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importare o esportare un database SQL di Azure senza consentire ai servizi di Azure di accedere al server

Questo articolo illustra come importare o esportare un database SQL di Azure quando il *servizio Consenti servizi di Azure* è impostato su *disattivato* nel server SQL di Azure. Il flusso di lavoro usa una macchina virtuale di Azure per eseguire SqlPackage per eseguire l'operazione di importazione o esportazione.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Creare la macchina virtuale di Azure

Creare una macchina virtuale di Azure selezionando il pulsante **Distribuisci in Azure** .

Questo modello consente di distribuire una semplice macchina virtuale Windows usando alcune opzioni diverse per la versione di Windows, usando la versione più recente con patch. Verrà distribuita una macchina virtuale di dimensioni a2 nella posizione del gruppo di risorse e verrà restituito il nome di dominio completo della macchina virtuale.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Per altre informazioni, vedere [distribuzione molto semplice di una macchina virtuale Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

Nei passaggi seguenti viene illustrato come connettersi alla macchina virtuale tramite una connessione Desktop remoto.

1. Al termine della distribuzione, passare alla risorsa macchina virtuale.

    ![VM](./media/import-export-from-vm/vm.png)  

2. Selezionare **Connetti**.

   Verrà visualizzato un file con estensione RDP (Remote Desktop Protocol) con l'indirizzo IP pubblico e il numero di porta per la macchina virtuale.

   ![Modulo RDP](./media/import-export-from-vm/rdp.png)  

3. Selezionare **Scarica file RDP**.

   > [!NOTE]
   > È anche possibile usare SSH per connettersi alla macchina virtuale.

4. Chiudere il modulo **Connettere la macchina virtuale**.
5. Per connettersi alla VM, aprire il file RDP scaricato.
6. Quando richiesto, selezionare **Connect** (Connetti). In Mac, è necessario un client RDP come questo [client Desktop remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponibile nel Mac App Store.

7. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale e quindi scegliere **OK**.

8. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Scegliere **Sì** o **Continua** per procedere con la connessione.



## <a name="install-sqlpackage"></a>Installare SqlPackage

[Scaricare e installare la versione più recente di SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Per ulteriori informazioni, vedere [SqlPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Creare una regola del firewall per consentire alla macchina virtuale di accedere al database

Aggiungere l'indirizzo IP pubblico della macchina virtuale al firewall del server di database SQL.

La procedura seguente crea una regola del firewall IP a livello di server per l'indirizzo IP pubblico della macchina virtuale e Abilita la connettività dalla macchina virtuale.

1. Selezionare **database SQL** dal menu a sinistra e quindi selezionare il database nella pagina **database SQL** . Verrà visualizzata la pagina Panoramica per il database, in cui viene visualizzato il nome completo del server (ad esempio **servername.database.Windows.NET**) e vengono fornite le opzioni per un'ulteriore configurazione.

2. Copiare il nome completo del server da utilizzare per la connessione al server e ai relativi database.

   ![Nome del server](./media/sql-database-get-started-portal/server-name.png)

3. Selezionare **Imposta firewall server** sulla barra degli strumenti. Verrà visualizzata la pagina **Impostazioni del firewall** per il server di database.

   ![regola del firewall IP a livello di server](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Scegliere **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP pubblico della macchina virtuale a una nuova regola del firewall IP a livello di server. Una regola del firewall IP a livello di server può aprire la porta 1433 per un singolo indirizzo IP o un intervallo di indirizzi IP.

5. Selezionare **Salva**. Viene creata una regola del firewall IP a livello di server per l'indirizzo IP pubblico della macchina virtuale che apre la porta 1433 sul server di database SQL.

6. Chiudere la pagina **Impostazioni del firewall**.



## <a name="export-a-database-using-sqlpackage"></a>Esportare un database usando SqlPackage

Per esportare un database SQL tramite l'utilità della riga di comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), vedere la sezione relativa ai [parametri e proprietà dell'importazione](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). L'utilità SqlPackage viene fornita con le versioni più recenti di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)oppure è possibile scaricare la versione più recente di [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Si consiglia di utilizzare l'utilità SqlPackage per la scalabilità e le prestazioni nella maggior parte degli ambienti di produzione. Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Questo esempio illustra come esportare un database usando SqlPackage. exe con Active Directory autenticazione universale. Sostituire con valori specifici per l'ambiente in uso.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Importare un database usando SqlPackage

Per importare un database SQL Server tramite l'utilità della riga di comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), vedere la sezione relativa a [parametri e proprietà dell'importazione](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage include le [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)più recenti. È anche possibile scaricare la versione più recente di [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Per la scalabilità e le prestazioni, è consigliabile usare SqlPackage, anziché il portale di Azure, nella maggior parte degli ambienti di produzione. Per informazioni da parte del team di consulenza clienti di SQL Server sull'uso di file `BACPAC` per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Il comando SqlPackage seguente importa il database **AdventureWorks2017** dalla risorsa di archiviazione locale a un server di database SQL di Azure. Crea un nuovo database denominato **myMigratedDatabase** con un livello di servizio **Premium** e un obiettivo di servizio **P6**. Modificare questi valori in base alle esigenze specifiche dell'ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Per connettersi a un server di database SQL con la gestione di un singolo database protetto da un firewall aziendale, è necessario che nel firewall sia aperta la porta 1433. Per connettersi a un'istanza gestita, è necessario disporre di una [connessione da punto a sito](sql-database-managed-instance-configure-p2s.md) o una connessione ExpressRoute.

Questo esempio illustra come importare un database usando SqlPackage con l'autenticazione universale di Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Le velocità di esportazione variano a causa di molti fattori, ad esempio la forma dei dati, quindi è impossibile prevedere la velocità prevista. SqlPackage potrebbe richiedere molto tempo, in particolare per i database di grandi dimensioni.

Per ottenere prestazioni ottimali, è possibile provare le strategie seguenti:

1. Assicurarsi che nessun altro carico di lavoro sia in esecuzione nel database. Creare una copia prima dell'esportazione potrebbe essere la soluzione migliore per assicurarsi che non siano in esecuzione altri carichi di lavoro.
2. Aumentare l'obiettivo del livello di servizio (SLO) del database per gestire meglio il carico di lavoro di esportazione (principalmente lettura I/O). Se il database è attualmente GP_Gen5_4, è possibile che un livello di business critical contribuisca al carico di lavoro di lettura.
3. Verificare che siano presenti indici cluster in particolare per le tabelle di grandi dimensioni. 
4. Le macchine virtuali (VM) devono trovarsi nella stessa area del database per evitare vincoli di rete.
5. Le macchine virtuali devono avere unità SSD con dimensioni appropriate per la generazione di elementi temporanei prima del caricamento nell'archivio BLOB.
6. Le macchine virtuali devono avere una configurazione di core e memoria adeguata per il database specifico.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Archiviare l'oggetto importato o esportato. File BACPAC

Il. Il file BACPAC può essere archiviato in [BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)o in [file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Per ottenere prestazioni ottimali, utilizzare File di Azure. SqlPackage opera con il file System, in modo che possa accedere direttamente a File di Azure.

Per ridurre i costi, usare i BLOB di Azure, che hanno un costo inferiore rispetto a una condivisione file di Azure Premium. Tuttavia, sarà necessario copiare il [. File BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) tra il BLOB e il file system locale prima dell'operazione di importazione o esportazione. Di conseguenza, il processo sarà più lungo.

Per caricare o scaricare. File BACPAC, vedere [trasferire dati con AzCopy e archiviazione BLOB](../storage/common/storage-use-azcopy-blobs.md)e [trasferire dati con AzCopy e archiviazione file](../storage/common/storage-use-azcopy-files.md).

A seconda dell'ambiente, potrebbe essere necessario configurare i [firewall e le reti virtuali di archiviazione di Azure](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come connettersi ed eseguire query su un database SQL importato, vedere [Guida introduttiva: database SQL di Azure: usare SQL Server Management Studio per connettersi ed eseguire query sui dati](sql-database-connect-query-ssms.md).
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
- Per una descrizione dell'intero processo di migrazione del database SQL Server, con raccomandazioni sulle prestazioni, vedere [Migrazione di un database SQL Server al database SQL di Azure](sql-database-single-database-migrate.md).
- Per informazioni su come gestire e condividere chiavi di archiviazione e firme di accesso condiviso in modo sicuro, vedere [Guida alla sicurezza di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
