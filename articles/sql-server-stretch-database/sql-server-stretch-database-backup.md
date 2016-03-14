<properties
	pageTitle="Backup e ripristino di database con estensione abilitata | Microsoft Azure"
	description="Scoprire come eseguire backup e ripristino di database con estensione abilitata."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>


# Eseguire backup e ripristino di database con estensione abilitata 

Per eseguire il backup e il ripristino di database con estensione abilitata, è possibile continuare a usare i metodi attualmente in uso. Per sapere di più su backup e ripristino di SQL Server, vedere l'articolo su come [eseguire il backup e il ripristino di database di SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

Il backup di un database con estensione abilitata è un backup superficiale che non include i dati migrati al server remoto.

Il Database Estensione offre supporto completo per il ripristino temporizzato. Dopo aver ripristinato il database di SQL Server a un punto nel tempo e autorizzato nuovamente la connessione ad Azure, Database Estensione riconcilia i dati remoti allo stesso punto nel tempo. Per sapere di più sul ripristino temporizzato in SQL Server, vedere l'articolo sul [ripristino di un database di SQL Server in un punto nel tempo (modello di recupero con registrazione completa)](https://msdn.microsoft.com/library/ms179451.aspx). Per informazioni sulla stored procedure da eseguire dopo un ripristino per autorizzare nuovamente la connessione ad Azure, vedere [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx).

## <a name="Reconnect"></a>Ripristinare un database con estensione abilitata da un backup

1.  Ripristinare il database da un backup.

2.  Creare una chiave master del database per database con estensione abilitata. Per altre informazioni, vedere l'articolo su come [CREARE UNA CHIAVE MASTER (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx)

3.  Creare credenziali con ambito database per il database con estensione abilitata. Per sapere di più, vedere l'articolo su come [CREARE CREDENZIALI CON AMBITO DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

4.  Eseguire la stored procedure [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) per connettere nuovamente il database locale con estensione abilitata in Azure. Specificare le credenziali con ambito database create nel passaggio precedente come sysname o valore varchar (128). Non usare varchar(max).

    ```tsql
    Declare @credentialName nvarchar(128);
    SET @credentialName = N’<database_scoped_credential_name_created_previously>’;
    EXEC sp_rda_reauthorize_db @credentialName;
    ```

## <a name="MoreInfo"></a>Altre informazioni su backup e ripristino
I backup in un database con Database Estensione abilitato contengono solo dati locali e dati idonei in corrispondenza del punto nel tempo in cui viene eseguito il backup. Questi backup contengono anche informazioni sull'endpoint remoto in cui risiedono i dati remoti del database. Questo backup è noto come "backup superficiale". Backup completi che contengono tutti i dati nel database locale e remoto non sono supportati.

![Diagramma di backup di Database Estensione][StretchBackupImage1]

Quando si ripristina un backup di un database con Database Estensione abilitato, questa operazione consente di ripristinare i dati locali e idonei per il database come previsto. I dati idonei sono dati non ancora spostati, ma che lo saranno in Azure in base alla configurazione di Database Estensione delle tabelle. Dopo aver eseguito l'operazione di ripristino, il database conterrà i dati locali e idonei del punto nel tempo in cui è stato eseguito il backup, ma non disporrà delle credenziali e degli elementi necessari per la connessione all'endpoint remoto.

![Database Estensione dopo il backup][StretchBackupImage2]

È necessario eseguire la stored procedure **sys.sp\_rda\_reauthorize\_db** per ristabilire la connessione tra il database locale e il relativo endpoint remoto. Solo un db\_owner può eseguire questa operazione. Per questa stored procedure sono necessari anche nome utente e password dell'amministratore dell'endpoint remoto. Questo significa che è necessario fornire accesso e password dell'amministratore per il server Azure di destinazione.

![Database Estensione dopo il backup][StretchBackupImage3]

Dopo aver ristabilito la connessione, Database Estensione tenta di riconciliare i dati idonei nel database locale con i dati remoti creando una copia dei dati remoti nell'endpoint remoto e collegandolo al database locale. Questo processo è automatico e non necessita dell'intervento dell'utente. Dopo aver eseguito la riconciliazione, il database locale e l'endpoint remoto sono in uno stato di coerenza.

![Database Estensione dopo il backup][StretchBackupImage4]

## Vedere anche
Articoli relativi a [gestione e risoluzione problemi di Database Estensione](sql-server-stretch-database-manage.md), [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) ed [esecuzione di backup e ripristino di database SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!--Image references-->
[StretchBackupImage1]: ./media/sql-server-stretch-database-backup/StretchDBBackup1.png
[StretchBackupImage2]: ./media/sql-server-stretch-database-backup/StretchDBBackup2.png
[StretchBackupImage3]: ./media/sql-server-stretch-database-backup/StretchDBBackup3.png
[StretchBackupImage4]: ./media/sql-server-stretch-database-backup/StretchDBBackup4.png

<!---HONumber=AcomDC_0302_2016-->