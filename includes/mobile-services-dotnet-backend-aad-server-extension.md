### (Facoltativo) Configurare il servizio mobile .NET per l'accesso ad AAD

> [WACOM.NOTE] Questa procedura è facoltativa perché si applica solo al provider di accesso di Azure Active Directory.

1.  Installare il pacchetto NuGet **WindowsAzure.MobileServices.Backend.Security**.

2.  In Visual Studio espandere App\_Start e aprire il file WebApiConfig.cs. Aggiungere il codice seguente al metodo `Register`, immediatamente dopo la creazione di un'istanza di `options`:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


