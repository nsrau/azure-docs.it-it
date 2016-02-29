## Prepararsi all’autenticazione delle richieste di Gestione risorse

Si devono autenticare tutte le attività da eseguire sulle risorse mediante [Gestione risorse di Azure][lnk-authenticate-arm] con Azure Active Directory (AD). Il modo più semplice per configurare questa impostazione è usare PowerShell o l’interfaccia della riga di comando di Azure.

È necessario installare [Azure PowerShell 1.0][lnk-powershell-install] o versione successiva prima di continuare.

La procedura seguente illustra come configurare l'autenticazione della password per un'applicazione di AD mediante PowerShell. È possibile eseguire questi comandi in una sessione standard di PowerShell.

1. Accedere alla sottoscrizione di Azure usando il comando seguente:

    ```
    Login-AzureRmAccount
    ```

2. Annotare il **TenantId** e il **SubscriptionId**. saranno necessarie più avanti.

3. Creare una nuova applicazione Azure Active Directory mediante il comando seguente, sostituendo i segnaposto:

    - **{Display name}:** un nome visualizzato per l'applicazione, come ad esempio **MySampleApp**
    - **{Home page URL}:** l'URL della home page dell'app, come ad esempio ****http://mysampleapp/home**. Non è necessario che questo URL punti a un'applicazione reale.
- **{Application identifier}::** un identificatore univoco, come ad esempio ****http://mysampleapp**. Non è necessario che questo URL punti a un'applicazione reale.
- **{Password}:** una password da utilizzare per l'autenticazione con l'app.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Annotare l’**ApplicationId** dell'applicazione creata. che sarà necessario più avanti.

5. Creare una nuova entità servizio usando il comando seguente, sostituendo **{MyApplicationId}** con l’**ApplicationId** dal passaggio precedente:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Installare un'assegnazione di ruolo mediante il comando seguente, sostituendo **{MyApplicationId}** con l’**ApplicationId**.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
A questo punto è terminata la creazione dell'applicazione Azure AD che consentirà di eseguire l'autenticazione dall’applicazione C# personalizzata. Più avanti, in questa esercitazione, saranno necessari i valori seguenti:

- TenantId
- SubscriptionId
- ApplicationId
- Password

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0218_2016-->