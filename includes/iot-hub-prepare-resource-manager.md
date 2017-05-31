## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Prepararsi all'autenticazione delle richieste di Azure Resource Manager
Si devono autenticare tutte le attività da eseguire sulle risorse mediante [Gestione risorse di Azure][lnk-authenticate-arm] con Azure Active Directory (AD). Il modo più semplice per configurare questa impostazione è usare PowerShell o l’interfaccia della riga di comando di Azure.

Installare i [cmdlet di Azure PowerShell][lnk-powershell-install] prima di continuare.

La procedura seguente illustra come configurare l'autenticazione della password per un'applicazione di AD mediante PowerShell. È possibile eseguire questi comandi in una sessione standard di PowerShell.

1. Accedere alla sottoscrizione di Azure usando il comando seguente:

    ```powershell
    Login-AzureRmAccount
    ```

1. Se si usano più sottoscrizioni Azure e si esegue l'accesso ad Azure, è possibile accedere a tutte le sottoscrizioni di Azure associate alle credenziali. Usare il comando seguente per elencare gli account Azure che è possibile usare:

    ```powershell
    Get-AzureRMSubscription
    ```

    Usare il comando seguente per selezionare la sottoscrizione che si vuole usare per eseguire i comandi per gestire l'hub IoT. È possibile usare il nome o l'ID della sottoscrizione dall'output del comando precedente:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Prendere nota dei valori di **TenantId** e **SubscriptionId**. Saranno necessari più avanti.
3. Creare una nuova applicazione Azure Active Directory mediante il comando seguente, sostituendo i segnaposto:
   
   * **{Display name}:** un nome visualizzato per l'applicazione, ad esempio **MySampleApp**.
   * **{Home page URL}:** l'URL della home page dell'app, ad esempio **http://mysampleapp/home**. Non è necessario che questo URL punti a un'applicazione reale.
   * **{Application identifier}:** un identificatore univoco, ad esempio **http://mysampleapp**. Non è necessario che questo URL punti a un'applicazione reale.
   * **{Password}:** password da usare per l'autenticazione con l'app.
     
     ```powershell
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
     ```
4. Annotare l’ **ApplicationId** dell'applicazione creata. Sarà necessario più avanti.
5. Creare una nuova entità servizio usando il comando seguente, sostituendo **{MyApplicationId}** con il valore di **ApplicationId** del passaggio precedente:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Configurare un'assegnazione di ruolo usando il comando seguente, sostituendo **{MyApplicationId}** con il valore di **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

A questo punto è terminata la creazione dell'applicazione Azure AD che consentirà di eseguire l'autenticazione dall'applicazione C# personalizzata. Più avanti in questa esercitazione saranno necessari i valori seguenti:

* TenantId
* SubscriptionId
* ApplicationId
* Password

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
