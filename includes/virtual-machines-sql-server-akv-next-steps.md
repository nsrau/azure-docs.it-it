## <a name="next-steps"></a>Passaggi successivi
Dopo aver attivato l'integrazione dell'insieme di credenziali delle chiavi di Azure, è possibile abilitare la crittografia di SQL Server nella macchina virtuale di SQL. Innanzitutto, è necessario creare una chiave asimmetrica nell'insieme di credenziali delle chiavi e una chiave simmetrica in SQL Server nella macchina virtuale. A questo punto, sarà possibile eseguire istruzioni T-SQL per abilitare la crittografia per i database e i backup.

Esistono diversi tipi di crittografia di cui è possibile usufruire:

* [Transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Backup crittografati](https://msdn.microsoft.com/library/dn449489.aspx)
* [Crittografia a livello di colonna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Gli script Transact-SQL seguenti forniscono esempi per ognuna di queste aree.

> [!NOTE]
> Ogni esempio è basato su due prerequisiti: una chiave asimmetrica dall'insieme di credenziali delle chiavi denominata **CONTOSO_KEY** e una credenziale creata dalla funzionalità di integrazione di AKV denominata **Azure_EKM_TDE_cred**.
> 
> 

### <a name="transparent-data-encryption-tde"></a>Transparent data encryption (TDE)
1. Creare un account di accesso di SQL Server che può essere usato dal motore di database per la TDE, quindi aggiungere la credenziale.
   
        USE master;
        -- Create a SQL Server login associated with the asymmetric key 
        -- for the Database engine to use when it loads a database 
        -- encrypted by TDE.
        CREATE LOGIN TDE_Login 
        FROM ASYMMETRIC KEY CONTOSO_KEY;
        GO
   
        -- Alter the TDE Login to add the credential for use by the 
        -- Database Engine to access the key vault
        ALTER LOGIN TDE_Login 
        ADD CREDENTIAL Azure_EKM_TDE_cred;
        GO
2. Creare la chiave di crittografia del database che verrà usata per la TDE.
   
        USE ContosoDatabase;
        GO
   
        CREATE DATABASE ENCRYPTION KEY 
        WITH ALGORITHM = AES_128 
        ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
        GO
   
        -- Alter the database to enable transparent data encryption.
        ALTER DATABASE ContosoDatabase 
        SET ENCRYPTION ON;
        GO

### <a name="encrypted-backups"></a>Backup crittografati
1. Creare un account di accesso di SQL Server che può essere usato dal motore di database per la crittografia dei backup, quindi aggiungere la credenziale.
   
        USE master;
        -- Create a SQL Server login associated with the asymmetric key 
        -- for the Database engine to use when it is encrypting the backup.
        CREATE LOGIN Backup_Login 
        FROM ASYMMETRIC KEY CONTOSO_KEY;
        GO 
   
        -- Alter the Encrypted Backup Login to add the credential for use by 
        -- the Database Engine to access the key vault
        ALTER LOGIN Backup_Login 
        ADD CREDENTIAL Azure_EKM_Backup_cred ;
        GO
2. Eseguire il backup del database specificando la crittografia con la chiave asimmetrica archiviata nell'insieme di credenziali delle chiavi.
   
        USE master;
        BACKUP DATABASE [DATABASE_TO_BACKUP]
        TO DISK = N'[PATH TO BACKUP FILE]' 
        WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
        ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
        GO

### <a name="column-level-encryption-cle"></a>Crittografia a livello di colonna (CLE)
Questo script crea una chiave simmetrica protetta dalla chiave asimmetrica nell'insieme di credenziali delle chiavi e quindi usa la chiave simmetrica per crittografare i dati nel database.

    CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
    WITH ALGORITHM=AES_256
    ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

    DECLARE @DATA VARBINARY(MAX);

    --Open the symmetric key for use in this session
    OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
    DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

    --Encrypt syntax
    SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

    -- Decrypt syntax
    SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

    --Close the symmetric key
    CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## <a name="additional-resources"></a>Risorse aggiuntive
Per altre informazioni su come usare queste funzionalità di crittografia, vedere l'argomento relativo all' [uso di EKM con le funzionalità di crittografia di SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Si noti che i passaggi in questo articolo presuppongono che si disponga già di SQL Server in esecuzione in una macchina virtuale di Azure. In caso contrario, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server in Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Per altre informazioni dettagliate sull'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server nella panoramica delle macchine virtuali di Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).



<!--HONumber=Jan17_HO2-->


