<properties
	pageTitle="Abilitare Database Estensione per un database | Microsoft Azure"
	description="Scoprire come configurare un database per Database Estensione."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Abilitare Database Estensione per un database

Per configurare un database per Database Estensione, selezionare **Attività | Estensione | Abilita** per un database in SQL Server Management Studio per aprire la procedura guidata **Abilitare il database per l’estensione**. È possibile anche usare Transact-SQL per abilitare Database Estensione per un database.

Se si seleziona **Attività | Estensione | Abilita** per una tabella e non è stato ancora abilitato il database per Database Estensione, la procedura guidata consente di configurare il database per Database Estensione e le tabelle come parte del processo. Seguire la procedura illustrata in questo argomento anziché quella descritta nell'articolo relativo all'[abilitazione di Database Estensione per una tabella](sql-server-stretch-database-enable-database.md).

Per abilitare Database Estensione in un database o una tabella è necessario disporre delle autorizzazioni db\_owner. Per abilitare Database Estensione in un database è necessario disporre anche delle autorizzazioni CONTROL DATABASE.

## Prima di iniziare

-   Prima di configurare un database per l'estensione, è consigliabile eseguire l'ottimizzazione guidata Database Estensione per identificare i database e le tabelle idonei per l'estensione. L'ottimizzazione guidata Database Estensione consente anche di identificare i problemi di blocco. Per altre informazioni, vedere l'articolo relativo all'[identificazione di database e tabelle per Database Estensione](sql-server-stretch-database-identify-databases.md).

-   Vedere [Limitazioni di Estensione database](sql-server-stretch-database-limitations.md).

-   Database Estensione esegue la migrazione dei dati in Azure. Per questo motivo è necessario disporre di un account Azure e di una sottoscrizione per la fatturazione. Per avere un account Azure, [fare clic qui](http://azure.microsoft.com/pricing/free-trial/).

-   Avere le giuste informazioni per creare un nuovo database remoto o per selezionare un database remoto esistente e per creare una regola del firewall che consenta al server locale di comunicare con il server remoto.

## <a name="EnableTSQLServer"></a>Prerequisito: Autorizzazioni per abilitare Database Estensione sul server
Prima di abilitare Database Estensione in un database o una tabella, è necessario abilitarla nel server locale. Per eseguire questa operazione è necessario disporre delle autorizzazioni sysadmin o serveradmin.

-   Se si dispone delle autorizzazioni amministrative necessarie, la procedura guidata **Abilitare il database per l’estensione** consente di configurare il server per l'estensione.

-   Se non si dispone delle autorizzazioni necessarie, un amministratore deve abilitare l'opzione manualmente eseguendo **sp\_configure** prima di eseguire la procedura guidata o eseguire la procedura guidata stessa.

Per abilitare manualmente Database Estensione sul server, eseguire **sp\_configure** e attivare l'opzione dell'**archivio dati remoto**. L'esempio seguente consente di abilitare l'opzione dell'**archivio dati remoto** impostandone il valore su 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO
RECONFIGURE;
GO
```
Per altre informazioni, vedere l'articolo su come [configurare l'opzione di configurazione del server dell'archivio dati remoto](https://msdn.microsoft.com/library/mt143175.aspx) e su [sp\_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Con la procedura guidata abilitare Database Estensione in un database
Per informazioni sulla procedura guidata Abilitare il database per l'estensione, incluse le informazioni da immettere e le opzioni da selezionare, vedere [Introduzione all'esecuzione della procedura guidata Abilitare il database per l'estensione](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Con Transact-SQL abilitare Database Estensione in un database
Prima di abilitare Database Estensione nelle singole tabelle, è necessario abilitarlo nel database.

Per abilitare Database Estensione in un database o una tabella è necessario disporre delle autorizzazioni db\_owner. Per abilitare Database Estensione in un database è necessario disporre anche delle autorizzazioni CONTROL DATABASE.

1.  Prima di iniziare, scegliere un server Azure esistente per i dati di cui Database Estensione esegue la migrazione o creare un nuovo server.

2.  Nel server Azure, creare una regola del firewall con l'indirizzo IP o l'intervallo di indirizzi IP di SQL Server per consentire a SQL Server di comunicare con il server remoto.

3.  Per configurare un database di SQL Server per Database Estensione, il database deve avere una chiave master del database. La chiave master del database consente di proteggere le credenziali usate da Database Estensione per la connessione al database remoto. Per creare manualmente la chiave master del database, vedere l'articolo su come [CREARE UNA CHIAVE MASTER (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e su come [creare una chiave master del database](https://msdn.microsoft.com/library/aa337551.aspx).

    ```tsql
    USE <database>
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>'
    ```

4.  Quando si configura un database per Database Estensione, è necessario specificare le relative credenziali da usare per la comunicazione tra SQL Server locale e il server Azure remoto. Sono disponibili due opzioni.

    -   È possibile fornire credenziali di amministratore.

        -   Se si abilita Database Estensione eseguendo la procedura guidata, è possibile creare le credenziali in questa fase.

        -   Se si abilita Database Estensione eseguendo **ALTER DATABASE**, è necessario creare manualmente le credenziali prima di abilitare Database Estensione.

        Per creare manualmente le credenziali, vedere l'articolo relativo alla [CREAZIONE DI CREDENZIALI CON AMBITO DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Per la creazione di credenziali sono necessarie le autorizzazioni ALTER ANY CREDENTIAL.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>'
        GO
        ```

    -   È possibile usare un account del servizio federato per SQL Server per comunicare con il server Azure remoto quando le condizioni seguenti sono tutte true.

        -   L'account del servizio in cui è in esecuzione l'istanza di SQL Server è un account di dominio.

        -   L'account di dominio appartiene a un dominio la cui Active Directory è federata con Azure Active Directory.

        -   Il server Azure remoto è configurato per supportare l'autenticazione di Azure Active Directory.

        -   L'account del servizio in cui è in esecuzione l'istanza di SQL Server deve essere configurato come un account dbmanager o sysadmin nel server Azure remoto.

5.  Per configurare un database per Database Estensione, eseguire il comando ALTER DATABASE.

    1.  Per l'argomento SERVER, specificare il nome di un server Azure esistente, tra cui la parte `.database.windows.net` del nome, ad esempio `MyStretchDatabaseServer.database.windows.net`.

    2.  Immettere le credenziali amministratore esistenti con l'argomento CREDENTIAL o specificare FEDERATED\_SERVICE\_ACCOUNT = ON. Nell'esempio seguente vengono fornite credenziali esistenti.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO;
    ```

## Passaggi successivi
Abilitare tabelle aggiuntive per Database Estensione. Monitorare la migrazione dei dati e gestire tabelle e database con estensione abilitata.

-   [Abilitare Database Estensione per una tabella](sql-server-stretch-database-enable-table.md) per abilitare tabelle aggiuntive.

-   [Monitorare Database Estensione](sql-server-stretch-database-monitor.md) per visualizzare lo stato della migrazione dei dati.

-   [Sospendere e riprendere Database Estensione](sql-server-stretch-database-pause.md)

-   [Gestire e risolvere i problemi di Database Estensione](sql-server-stretch-database-manage.md)

-   [Eseguire backup e ripristino di database con estensione abilitata](sql-server-stretch-database-backup.md)

## Vedere anche

Articolo relativo all'[identificazione di database e tabelle per l'estensione database](sql-server-stretch-database-identify-databases.md)

[Opzioni ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

<!---HONumber=AcomDC_0615_2016-->