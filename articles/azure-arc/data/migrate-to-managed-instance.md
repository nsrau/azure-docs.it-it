---
title: Eseguire la migrazione di un database da SQL Server ad Azure Arc abilitato SQL Istanza gestita
description: Eseguire la migrazione del database da SQL Server ad Azure Arc abilitato SQL Istanza gestita
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939630"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>Migrate: SQL Server ad Azure Arc abilitato SQL Istanza gestita

Questo scenario illustra i passaggi per eseguire la migrazione di un database da un'istanza di SQL Server all'istanza gestita di SQL di Azure in Azure Arc tramite due metodi di backup e ripristino diversi.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Usare l'archiviazione BLOB di Azure 

Usare l'archiviazione BLOB di Azure per eseguire la migrazione ad Azure Arc Enabled SQL Istanza gestita.

Questo metodo usa l'archivio BLOB di Azure come posizione di archiviazione temporanea di cui è possibile eseguire il backup e quindi il ripristino da.

### <a name="prerequisites"></a>Prerequisiti

- [Installare Azure Data Studio](install-client-tools.md)
- [Installa Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- Sottoscrizione di Azure

### <a name="step-1-provision-azure-blob-storage"></a>Passaggio 1: eseguire il provisioning di archiviazione BLOB

1. Seguire i passaggi descritti in [creare un account di archiviazione BLOB di Azure](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)
1. Avvia Azure Storage Explorer
1. Accedere [ad Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) per accedere all'archivio BLOB creato nel passaggio precedente
1. Fare clic con il pulsante destro del mouse sull'account di archiviazione BLOB e selezionare **Crea contenitore BLOB** per creare un nuovo contenitore in cui verrà archiviato il file di backup

### <a name="step-2-get-storage-blob-credentials"></a>Passaggio 2: ottenere le credenziali del BLOB di archiviazione

1. In Azure Storage Explorer fare clic con il pulsante destro del mouse sul contenitore BLOB appena creato e selezionare **Ottieni firma di accesso condiviso** .

1. Selezionare **lettura**, **scrittura** ed **elenco**

1. Selezionare **Crea**

   Prendere nota dell'URI e della stringa di query da questa schermata. Questi saranno necessari nei passaggi successivi. Fare clic sul pulsante **copia** per salvare un blocco note/OneNote e così via.

1. Chiudere la finestra **firma di accesso condiviso** .

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>Passaggio 3: eseguire il backup del file di database nell'archiviazione BLOB di Azure

In questo passaggio verrà effettuata la connessione al SQL Server di origine e verrà creato il file di backup del database di cui si vuole eseguire la migrazione a SQL Istanza gestita-Azure Arc.

1. Avvia Azure Data Studio
1. Connettersi all'istanza di SQL Server che contiene il database di cui si vuole eseguire la migrazione a SQL Istanza gestita-Azure Arc
1. Fare clic con il pulsante destro del mouse sul database e scegliere **nuova query** .
1. Preparare la query nel formato seguente sostituendo i segnaposto indicati da `<...>` usando le informazioni della firma di accesso condiviso nei passaggi precedenti.  Una volta sostituiti i valori, eseguire la query.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. Analogamente, preparare il comando **backup database** come indicato di seguito per creare un file di backup nel contenitore BLOB.  Una volta sostituiti i valori, eseguire la query.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Aprire Azure Storage Explorer e verificare che il file di backup creato nel passaggio precedente sia visibile nel contenitore BLOB

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>Passaggio 4: ripristinare il database dall'archiviazione BLOB di Azure a SQL Istanza gestita-Azure Arc

1. Da Azure Data Studio, effettuare l'accesso e connettersi a SQL Istanza gestita-Azure Arc.
1. Espandere i **database di sistema**, fare clic con il pulsante destro del mouse su database **Master** e selezionare **nuova query**.
1. Nella finestra dell'editor di query preparare ed eseguire la stessa query del passaggio precedente per creare le credenziali.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Preparare ed eseguire il comando seguente per verificare che il file di backup sia leggibile e intatto.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Preparare ed eseguire il comando **Restore database** come indicato di seguito per ripristinare il file di backup in un database in SQL istanza gestita-Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

Altre informazioni sul backup nell'URL sono disponibili qui:

[Documentazione di backup in URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Eseguire il backup nell'URL usando SQL Server Management Studio (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>Metodo 2: copiare il file di backup in un Istanza gestita SQL di Azure-pod di Azure Arc con kubectl

Questo metodo illustra come eseguire un file di backup creato tramite qualsiasi metodo e quindi copiarlo nella risorsa di archiviazione locale nel pod dell'istanza gestita di SQL di Azure, in modo da poter eseguire il ripristino da un tipo di file system tipico in Windows o Linux. In questo scenario si utilizzerà il comando `kubectl cp` per copiare il file da una posizione all'file System del Pod.

### <a name="prerequisites"></a>Prerequisiti

- Installare e configurare kubectl per puntare al cluster Kubernetes in cui è distribuito Azure Arc Data Services
- Disporre di uno strumento come Azure Data Studio o SQL Server server di gestione installato e connesso al SQL Server in cui si desidera creare il file di backup o avere un file con estensione bak esistente già creato nel file system locale.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>Passaggio 1: eseguire il backup del database, se non è già stato fatto

Eseguire il backup del database SQL Server nel percorso del file locale, ad esempio un backup di SQL Server tipico su disco:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>Passaggio 2: copiare il file di backup nel file system del Pod

Trovare il nome del pod in cui è distribuita l'istanza di SQL. In genere dovrebbe essere simile a `pod/<sqlinstancename>-0`

Ottenere l'elenco di tutti i pod eseguendo:

 ```console
kubectl get pods -n <namespace of data controller>
```

Esempio:

Copiare il file di backup dalla risorsa di archiviazione locale al Pod SQL del cluster.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>Passaggio 3: ripristinare il database

Preparare ed eseguire il comando RESTOre per ripristinare il file di backup nell'istanza gestita di SQL di Azure-Azure Arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Esempio:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sulle funzionalità e sulle funzionalità di Azure Arc abilitate per SQL Istanza gestita](managed-instance-features.md)

[Iniziare creando un controller dati](create-data-controller.md)

[Il controller dati è già stato creato? Creare un Istanza gestita SQL abilitato per Azure Arc](create-sql-managed-instance.md)