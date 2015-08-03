L'autenticazione è già abilitata nel progetto server scaricato dal portale.

Nel progetto ASP.NET dovrebbero essere visualizzati gli elementi seguenti:

* Il pacchetto NuGet `Microsoft.Azure.Mobile.Server.Authentication` è installato.

* In WebApiConfig.cs il metodo `UseDefaultConfiguration()` viene chiamato sull'oggetto MobileAppConfiguration, che a sua volta chiama il metodo di estensione `AddAppServiceAuthentication()` fornito dal pacchetto NuGet indicato. Registra anche un middleware OWIN necessario per l'autenticazione chiamando `app.UseAppServiceAuthentication()` durante l'avvio di OWIN.

<!---HONumber=July15_HO4-->