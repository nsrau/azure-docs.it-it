## <a name="prepare-for-akv-integration"></a>Preparare l'integrazione di AKV
Per usare l'integrazione dell'insieme di credenziali delle chiavi di Azure per configurare la macchina virtuale di SQL Server, sono necessari diversi prerequisiti: 

1. [Installare Azure PowerShell](#install-azure-powershell)
2. [Creare un'istanza di Azure Active Directory](#create-an-azure-active-directory)
3. [Creare un insieme di credenziali delle chiavi](#create-a-key-vault)

Le sezioni seguenti descrivono tali prerequisiti e le informazioni da raccogliere per eseguire i cmdlet di PowerShell in un secondo momento.

### <a name="install-azure-powershell"></a>Installare Azure PowerShell
Assicurarsi che sia installata la versione più recente di Azure PowerShell SDK. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="create-an-azure-active-directory"></a>Creare un'istanza di Azure Active Directory
Innanzitutto, è necessario che un'istanza di [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) sia presente nella sottoscrizione. AAD offre numerosi vantaggi, ad esempio, consente di concedere l'autorizzazione all'insieme di credenziali delle chiavi per determinati utenti e applicazioni.

Successivamente, registrare un'applicazione con AAD. In questo modo, è disponibile per l'utente un account dell'entità servizio con accesso all'insieme di credenziali delle chiavi necessario per la macchina virtuale. Nell'articolo sull'insieme di credenziali delle chiavi di Azure è possibile trovare questi passaggi nella sezione [Registrare un'applicazione con Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register). In alternativa, è possibile vedere i passaggi con gli screenshot nella sezione **Get an identity for the application** (Ottenere un'identità per l'applicazione) di [questo post di blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Prima di completare questi passaggi, si noti che è necessario raccogliere le informazioni seguenti durante la registrazione, utili in seguito quando si abilita l'integrazione dell'insieme di credenziali delle chiavi di Azure nella macchina virtuale di SQL.

* Dopo aver aggiunto l'applicazione, trovare l'**ID CLIENT** nella scheda **CONFIGURA**. 
    ![ID client di Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
  
    L'ID client viene assegnato successivamente al parametro **$spName** (nome entità servizio) nello script di PowerShell, per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure. 
* Inoltre, durante questa procedura quando si crea la chiave, copiare il segreto della chiave come illustrato nella schermata seguente. Tale segreto della chiave viene assegnato successivamente al parametro **$spSecret** (segreto entità servizio) nello script di PowerShell.  
    ![Segreto di Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
* È necessario autorizzare questo nuovo ID client per disporre delle autorizzazioni di accesso seguenti: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign** e **verify**. Questa operazione viene eseguita con il cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) . Per altre informazioni, vedere [Autorizzare l'applicazione a usare la chiave o il segreto](../articles/key-vault/key-vault-get-started.md#authorize).

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
Per usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi da usare per la crittografia nella macchina virtuale, è necessario accedere a un insieme di credenziali delle chiavi. Se l'insieme di credenziali delle chiavi non è ancora stato configurato, crearne uno seguendo i passaggi nell'argomento [Introduzione all'insieme di credenziali delle chiavi di Azure](../articles/key-vault/key-vault-get-started.md) . Prima di completare questi passaggi, si noti che è necessario raccogliere alcune informazioni durante l'impostazione, utili in seguito quando si abilita l'integrazione dell'insieme di credenziali delle chiavi di Azure nella macchina virtuale di SQL.

Al passaggio Creare un insieme di credenziali delle chiavi, si noti la proprietà **vaultUri** restituita, ovvero l'URL dell'insieme di credenziali delle chiavi. Nell'esempio fornito in questo passaggio, riportato di seguito, il nome dell'insieme di credenziali delle chiavi è ContosoKeyVault, quindi l'URL dell'insieme di credenziali delle chiavi è https://contosokeyvault.vault.azure.net/.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

L'URL dell'insieme di credenziali delle chiavi viene assegnato successivamente al parametro **$akvURL** nello script di PowerShell, per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure.



<!--HONumber=Dec16_HO1-->


