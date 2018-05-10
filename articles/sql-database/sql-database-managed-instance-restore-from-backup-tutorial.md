---
title: Ripristinare un backup in un'istanza gestita di database SQL di Azure | Microsoft Docs
description: Ripristinare un backup di database in un'istanza gestita di database SQL di Azure con SSMS.
keywords: esercitazione su database SQL, istanza gestita di database SQL, ripristinare un backup
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: ff605b7512a27f81b111560f5d151010dbb62273
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Ripristinare un backup di database in un'istanza gestita di database SQL di Azure

Questa esercitazione illustra come ripristinare un backup di un database archiviato nell'archivio BLOB di Azure in Istanza gestita usando il file di backup standard di Wide World Importers. Questo metodo prevede un tempo di inattività. Per un'esercitazione sull'uso di Servizio Migrazione del database di Azure per la migrazione, vedere l'articolo relativo alla [migrazione a Istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md). Per informazioni sui vari metodi di migrazione, vedere [Migrazione di un'istanza di SQL Server a Istanza gestita di database SQL di Azure](sql-database-managed-instance-migrate.md).

> [!div class="checklist"]
> * Scaricare il file di backup standard di Wide World Importers
> * Creare un account di archiviazione di Azure e caricare il file di backup
> * Ripristinare il database Wide World Importers da un file di backup

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione vengono usate come punto di partenza le risorse create nell'esercitazione [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-create-tutorial-portal.md).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Scaricare il file di backup standard di Wide World Importers

Usare la procedura seguente per scaricare il file di backup standard di Wide World Importers.

Usando Internet Explorer, immettere https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak nella casella dell'indirizzo URL e quindi, quando richiesto, fare clic su **Salva** per salvare il file nella cartella **Download**.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Creare un account di archiviazione di Azure e caricare il file di backup

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare **Archiviazione** e quindi fare clic su **Account di archiviazione** per aprire il relativo modulo.

   ![archiviazione di Azure](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Compilare il modulo relativo all'account di archiviazione con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Modello di distribuzione**|Modello di risorsa||
   |**Tipo di account**|Archiviazione BLOB||
   |**Prestazioni**|Standard o Premium|Unità magnetiche o SSD|
   |**Replica**|Archiviazione con ridondanza locale||
   |**Livello di accesso (predefinito)|Sporadico o Frequente||
   |**Trasferimento sicuro necessario**|Disabled||
   |**Sottoscrizione**|Sottoscrizione in uso|Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni).|
   |**Gruppo di risorse**|Gruppo di risorse creato in precedenza|| 
   |**Posizione**|Località selezionata in precedenza||
   |**Reti virtuali**|Disabled||

4. Fare clic su **Crea**.

   ![Dettagli dell'account di archiviazione](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Al termine della distribuzione dell'account di archiviazione, aprire il nuovo account di archiviazione.
6. In **Impostazioni** fare clic su **Firma di accesso condiviso** per aprire il relativo modulo.

   ![Modulo relativo alla firma di accesso condiviso](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. Nel modulo Firma di accesso condiviso modificare i valori predefiniti in base alle esigenze. Si noti che data e ora di scadenza corrispondono per impostazione predefinita a 8 ore soltanto.
8. Fare clic su **Genera firma di accesso condiviso**.

   ![Modulo relativo alla firma di accesso condiviso completato](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Copiare e salvare di valori di **Token di firma di accesso condiviso** e **URL di firma di accesso condiviso del servizio BLOB**.
10. In **Impostazioni** fare clic su **Contenitori**.

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. Fare clic su **+ Contenitore** per creare un contenitore in cui inserire il file di backup.
12. Compilare il modulo relativo al contenitore con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

    | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Livello di accesso pubblico**|Private (no anonymous access) (Privato - nessun accesso anonimo)||

    ![Dettagli del contenitore](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Fare clic su **OK**.
14. Al termine della creazione del contenitore, aprirlo.

    ![Contenitore](./media/sql-database-managed-instance-tutorial/container.png)

15. Fare clic su **Proprietà del contenitore** e quindi copiare l'URL del contenitore.

    ![URL del contenitore](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Fare clic su **Carica** per aprire il modulo **Carica BLOB**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. Passare alla cartella di download e selezionare il file **WideWorldImporters-Standard.bak**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Fare clic su **Carica**.
19. Non continuare finché il caricamento non è completato.

    ![Caricamento completato](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Ripristinare il database Wide World Importers da un file di backup

Con SSMS, usare la procedura seguente per ripristinare il database Wide World Importers nell'istanza gestita da un file di backup.

1. In SSMS aprire una nuova finestra di query.
2. Usare lo script seguente per creare le credenziali di firma di accesso condiviso, specificando l'URL del contenitore dell'account di archiviazione e la chiave di firma di accesso condiviso come indicato.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credential](./media/sql-database-managed-instance-tutorial/credential.png)

3. Usare lo script per controllare le credenziali di firma di accesso condiviso e la validità del backup, specificando l'URL del contenitore con il file di backup:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![Elenco file](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Usare lo script per ripristinare il database Wide World Importers da un file di backup, specificando l'URL del contenitore con il file di backup:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![Ripristino in esecuzione](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Per monitorare lo stato del ripristino, eseguire questa query in una nuova sessione di query:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![Percentuale di completamento del ripristino](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Al termine del ripristino, visualizzarlo in Esplora oggetti. 

    ![Ripristino completato](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come ripristinare un backup di un database archiviato nell'archivio BLOB di Azure in Istanza gestita usando il file di backup standard di Wide World Importers. Si è appreso come: 

> [!div class="checklist"]
> * Scaricare il file di backup standard di Wide World Importers
> * Creare un account di archiviazione di Azure e caricare il file di backup
> * Ripristinare il database Wide World Importers da un file di backup

Passare all'esercitazione successiva per apprendere come eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure con Servizio Migrazione del database.

> [!div class="nextstepaction"]
>[Eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure con Servizio Migrazione del database di Azure](../dms/tutorial-sql-server-to-managed-instance.md)