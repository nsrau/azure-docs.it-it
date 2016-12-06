## <a name="obtain-an-azure-resource-manager-token"></a>Ottenere un token di Azure Resource Manager
Azure Active Directory deve autenticare tutte le attività da eseguire sulle risorse con Gestione risorse di Azure. Nell'esempio illustrato di seguito si usa l'autenticazione della password, per altri approcci, vedere [Authenticating Azure Resource Manager requests][lnk-authenticate-arm] (Autenticazione delle richieste di Azure Resource Manager).

1. Aggiungere il codice seguente al metodo **Main** in Program.cs per recuperare un token da Azure AD tramite l'id dell'applicazione e la password.
   
    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Creare un oggetto **ResourceManagementClient** che usa il token aggiungendo il codice seguente alla fine del metodo **Main**:
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Creare o ottenere un riferimento al gruppo di risorse in uso:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx

<!--HONumber=Nov16_HO3-->


