### (Facoltativo) Configurare il servizio mobile .NET per l'accesso ad AAD

>[AZURE.NOTE] Questa procedura è facoltativa perché si applica solo al provider di accesso di Azure Active Directory.

1. Installare il **WindowsAzure.MobileServices.Backend.Security** pacchetto NuGet.

2. In Visual Studio espandere App_Start e aprire il file WebApiConfig.cs. Aggiungere il codice seguente al metodo  `Register`, immediatamente dopo la creazione di un'istanza di  `options`:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


<!--HONumber=42-->
