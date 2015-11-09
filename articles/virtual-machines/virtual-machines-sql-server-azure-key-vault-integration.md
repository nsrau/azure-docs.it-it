<properties 
	pageTitle="Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure"
	description="Informazioni su come automatizzare la configurazione della crittografia di SQL Server per l'uso con l'insieme di credenziali delle chiavi di Azure. Questo argomento illustra come usare l'integrazione dell'insieme di credenziali delle chiavi di Azure con le macchine virtuali di SQL Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure

## Panoramica
Esistono più funzionalità di crittografia di SQL Server, ad esempio [transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [crittografia a livello di colonna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) e [crittografia di backup](https://msdn.microsoft.com/library/dn449489.aspx). Queste modalità di crittografia richiedono la gestione e l'archiviazione delle chiavi usate per la crittografia. Il servizio dell'insieme di credenziali delle chiavi di Azure (AKV) è progettato per migliorare la sicurezza e la gestione di queste chiavi in una posizione sicura e a elevata disponibilità. Il [connettore di SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) consente a SQL Server di usare queste chiavi dall'insieme di credenziali delle chiavi di Azure.

Se si esegue SQL Server con computer locali, sono disponibili [passaggi per accedere all'insieme di credenziali delle chiavi di Azure dal computer locale con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Se si esegue SQL Server in macchine virtuali di Azure, è possibile risparmiare tempo usando la funzionalità di *integrazione dell'insieme di credenziali delle chiavi di Azure*. Con alcuni cmdlet di Azure PowerShell che abilitano questa funzionalità, è possibile automatizzare la configurazione necessaria per l'accesso all'insieme di credenziali delle chiavi di una macchina virtuale di SQL.

Quando questa funzionalità è abilitata, installa automaticamente il connettore di SQL Server, configura il provider EKM per accedere all'insieme di credenziali delle chiavi di Azure e crea le credenziali per consentire l'accesso all'insieme di credenziali. Se sono stati esaminati i passaggi nella documentazione locale menzionati in precedenza, si noterà che questa funzionalità consente di automatizzare i passaggi 2 e 3. L'unica attività che è comunque necessario eseguire manualmente è la creazione delle chiavi e dell'insieme di credenziali delle chiavi. Una volta completata questa operazione, l'intera installazione della macchina virtuale di SQL è automatizzata. Quando la funzionalità ha completato l'installazione, è possibile eseguire istruzioni T-SQL per iniziare la crittografia dei database o del backup regolarmente.

## Preparare l'integrazione di AKV
Per usare l'integrazione dell'insieme di credenziali delle chiavi di Azure per configurare la macchina virtuale di SQL Server, sono necessari diversi prerequisiti:

1.	[Installare Azure PowerShell](#install-azure-powershell)
2.	[Creare un'istanza di Azure Active Directory](#create-an-azure-active-directory)
3.	[Creare un insieme di credenziali delle chiavi](#create-a-key-vault)

Le sezioni seguenti descrivono tali prerequisiti e le informazioni da raccogliere per eseguire i cmdlet di PowerShell in un secondo momento.

### Installare Azure PowerShell
Assicurarsi che sia installata la versione più recente di Azure PowerShell SDK. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

### Creare un'istanza di Azure Active Directory
Innanzitutto, è necessario che un'istanza di [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) sia presente nella sottoscrizione. AAD offre numerosi vantaggi, ad esempio, consente di concedere l'autorizzazione all'insieme di credenziali delle chiavi per determinati utenti e applicazioni.

Successivamente, registrare un'applicazione con AAD. In questo modo, è disponibile per l'utente un account dell'entità servizio con accesso all'insieme di credenziali delle chiavi necessario per la macchina virtuale. Nell'articolo relativo all'insieme di credenziali delle chiavi di Azure, è possibile trovare questi passaggi nella sezione [Registrare un'applicazione con Azure Active Directory](../key-vault/key-vault-get-started.md#register). In alternativa, è possibile vedere i passaggi nelle schermate della sezione contenente informazioni per **ottenere un'identità per l'applicazione** di [questo post di blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Prima di completare questi passaggi, si noti che è necessario raccogliere le informazioni seguenti durante la registrazione, utili in seguito quando si abilita l'integrazione dell'insieme di credenziali delle chiavi di Azure nella macchina virtuale di SQL.

- Dopo aver aggiunto l'applicazione, trovare l'**ID CLIENT** nella scheda **CONFIGURA**. ![ID client di Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	L'ID client viene assegnato successivamente al parametro **$spName** (nome entità servizio) nello script di PowerShell, per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure. 
- Inoltre, durante questa procedura quando si crea la chiave, copiare il segreto della chiave come illustrato nella schermata seguente. Tale segreto della chiave viene assegnato successivamente al parametro **$spSecret** (segreto entità servizio) nello script di PowerShell. ![Segreto di Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- È necessario autorizzare questo nuovo ID client per disporre delle autorizzazioni di accesso seguenti: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign** e **verify**. Questa operazione viene eseguita con il cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx). Per altre informazioni, vedere [Autorizzare l'applicazione a usare la chiave o il segreto](../key-vault/key-vault-get-started.md#authorize).

### Creare un insieme di credenziali delle chiavi
Per usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi da usare per la crittografia nella macchina virtuale, è necessario accedere a un insieme di credenziali delle chiavi. Se l'insieme di credenziali delle chiavi non è ancora stato configurato, crearne uno seguendo i passaggi nell'argomento [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md). Prima di completare questi passaggi, si noti che è necessario raccogliere alcune informazioni durante l'impostazione, utili in seguito quando si abilita l'integrazione dell'insieme di credenziali delle chiavi di Azure nella macchina virtuale di SQL.

Al passaggio Creare un insieme di credenziali delle chiavi, si noti la proprietà **vaultUri** restituita, ovvero l'URL dell'insieme di credenziali delle chiavi. Nell'esempio fornito in questo passaggio, riportato di seguito, il nome dell'insieme di credenziali delle chiavi è ContosoKeyVault, pertanto l'URL dell'insieme di credenziali delle chiavi è https://contosokeyvault.vault.azure.net/.

![Segreto di Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
L'URL dell'insieme di credenziali delle chiavi viene assegnato successivamente al parametro **$akvURL** nello script di PowerShell, per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

## Configurare l'integrazione di AKV
Usare PowerShell per configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure. Le sezioni seguenti forniscono una panoramica dei parametri necessari e quindi uno script di PowerShell di esempio.

### Parametri di input
La tabella seguente elenca i parametri necessari per eseguire lo script di PowerShell nella sezione successiva.

|Parametro|Descrizione|Esempio|
|---|---|---|
|**$akvURL**|**URL dell'insieme di credenziali delle chiavi**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Nome entità servizio**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Segreto entità servizio**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**Nome della credenziale**: l'integrazione di AKV crea una credenziale all'interno di SQL Server, consentendo alla macchina virtuale di avere accesso all'insieme di credenziali delle chiavi. Scegliere un nome per la credenziale.|"mycred1"|
|**$vmName**|**Nome macchina virtuale**: il nome di una macchina virtuale di SQL creato in precedenza.|"myvmname"|
|**$serviceName**|**Nome servizio**: il nome del servizio cloud associato alla macchina virtuale di SQL.|"mycloudservicename"|

### Abilitare l'integrazione di AKV con PowerShell
Il cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** crea un oggetto di configurazione per la funzionalità di integrazione dell'insieme di credenziali delle chiavi di Azure. Il cmdlet **Set-AzureVMSqlServerExtension** configura l'integrazione con il parametro **KeyVaultCredentialSettings**. I passaggi seguenti illustrano come usare questi comandi.

1. In Azure PowerShell, configurare innanzitutto i parametri di input con i valori specifici, come descritto nelle sezioni precedenti di questo argomento. Di seguito è riportato uno script di esempio.
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	Usare quindi lo script seguente per configurare e abilitare l'integrazione di AKV.
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

L'estensione dell'agente IaaS di SQL aggiornerà la macchina virtuale di SQL con questa nuova configurazione.

## Passaggi successivi
Dopo aver attivato l'integrazione dell'insieme di credenziali delle chiavi di Azure, è possibile abilitare la crittografia di SQL Server nella macchina virtuale di SQL. Innanzitutto, è necessario creare una chiave asimmetrica nell'insieme di credenziali delle chiavi e una chiave simmetrica in SQL Server nella macchina virtuale. A questo punto, sarà possibile eseguire istruzioni T-SQL per abilitare la crittografia per i database e i backup.

Esistono diversi tipi di crittografia di cui è possibile usufruire:

- [Transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [Backup crittografati](https://msdn.microsoft.com/library/dn449489.aspx)
- [Crittografia a livello di colonna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Gli script Transact-SQL seguenti forniscono esempi per ognuna di queste aree.

>[AZURE.NOTE]Ogni esempio è basato su due prerequisiti: una chiave asimmetrica dall'insieme di credenziali delle chiavi denominata **CONTOSO\_KEY** e una credenziale creata dalla funzionalità di integrazione di AKV denominata **Azure\_EKM\_TDE\_cred**.

### Transparent data encryption (TDE)
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

### Backup crittografati
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

### Crittografia a livello di colonna (CLE)
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

## Risorse aggiuntive
Per altre informazioni su come usare queste funzionalità di crittografia, vedere l'argomento relativo all'[uso di EKM con le funzionalità di crittografia di SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Si noti che i passaggi in questo articolo presuppongono che si disponga già di SQL Server in esecuzione in una macchina virtuale di Azure. In caso contrario, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md). Per altre informazioni dettagliate relative all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server nella panoramica delle Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Nov15_HO1-->