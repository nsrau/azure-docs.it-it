Il push è già abilitato nel progetto server scaricato dal portale.

Nel progetto ASP.NET dovrebbero essere visualizzati gli elementi seguenti:

* Il pacchetto NuGet `Microsoft.Azure.Mobile.Server.Notifications` è installato.

* In WebApiConfig.cs il metodo `UseDefaultConfiguration()` viene chiamato sull'oggetto MobileAppConfiguration, che a sua volta chiama il metodo di estensione `AddPushNotifications()` fornito dal pacchetto NuGet indicato.

<!---HONumber=August15_HO6-->