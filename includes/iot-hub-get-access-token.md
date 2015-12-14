## Ottenere un token di Gestione risorse

Azure Active Directory deve autenticare tutte le attività da eseguire sulle risorse con Gestione risorse di Azure. Nell'esempio illustrato di seguito si usa l'autenticazione della password, per altri approcci, vedere [Autenticazione delle richieste di Gestione risorse di Azure][lnk-authenticate-arm].

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

2. Creare un oggetto **ResourceManagementClient** che utilizza il token aggiungendo il codice seguente alla fine del metodo **Main**:

    ```
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds);
    ```

3. Creare o ottenere un riferimento al gruppo di risorse in uso:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdateAsync(rgName,
        new ResourceGroup("East US")).Result;
    if (rgResponse.StatusCode != HttpStatusCode.Created
        && rgResponse.StatusCode != HttpStatusCode.OK)
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx

<!---HONumber=AcomDC_1203_2015-->