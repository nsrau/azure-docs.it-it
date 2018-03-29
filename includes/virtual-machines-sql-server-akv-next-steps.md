## <a name="next-steps"></a>Passaggi successivi

Dopo aver attivato l'integrazione dell'insieme di credenziali delle chiavi di Azure, è possibile abilitare la crittografia di SQL Server nella macchina virtuale di SQL. Innanzitutto, è necessario creare una chiave asimmetrica nell'insieme di credenziali delle chiavi e una chiave simmetrica in SQL Server nella macchina virtuale. A questo punto, sarà possibile eseguire istruzioni T-SQL per abilitare la crittografia per i database e i backup.

Esistono diversi tipi di crittografia di cui è possibile usufruire:

* [Transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Backup crittografati](https://msdn.microsoft.com/library/dn449489.aspx)
* [Crittografia a livello di colonna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Gli script Transact-SQL seguenti forniscono esempi per ognuna di queste aree.

### <a name="prerequisites-for-examples"></a>Prerequisiti per gli esempi

Ogni esempio è basato su due prerequisiti: una chiave asimmetrica dall'insieme di credenziali delle chiavi denominata **CONTOSO_KEY** e una credenziale creata dalla funzionalità di integrazione di AKV denominata **Azure_EKM_TDE_cred**. I comandi Transact-SQL seguenti consentono di configurare questi prerequisiti per l'esecuzione degli esempi.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that have sysadmin permissions, this will allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent data encryption (TDE)

1. Creare un account di accesso di SQL Server che può essere usato dal motore di database per la TDE, quindi aggiungere la credenziale.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Creare la chiave di crittografia del database che verrà usata per la TDE.

   ``` sql
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
   ```

### <a name="encrypted-backups"></a>Backup crittografati

1. Creare un account di accesso di SQL Server che può essere usato dal motore di database per la crittografia dei backup, quindi aggiungere la credenziale.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Eseguire il backup del database specificando la crittografia con la chiave asimmetrica archiviata nell'insieme di credenziali delle chiavi.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Crittografia a livello di colonna (CLE)

Questo script crea una chiave simmetrica protetta dalla chiave asimmetrica nell'insieme di credenziali delle chiavi e quindi usa la chiave simmetrica per crittografare i dati nel database.

``` sql
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
```

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni su come usare queste funzionalità di crittografia, vedere l'argomento relativo all' [uso di EKM con le funzionalità di crittografia di SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Si noti che i passaggi in questo articolo presuppongono che si disponga già di SQL Server in esecuzione in una macchina virtuale di Azure. In caso contrario, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server in Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Per altre informazioni dettagliate sull'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server nella panoramica delle macchine virtuali di Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
