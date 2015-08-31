Le notifiche push sono già abilitate nel progetto server scaricato dal portale.

Nel progetto ASP.NET è possibile visualizzare gli elementi seguenti:

* Il pacchetto NuGet `Microsoft.Azure.Mobile.Server.Notifications` è installato.

* In WebApiConfig.cs il metodo `UseDefaultConfiguration()` viene chiamato sull'oggetto MobileAppConfiguration, che a sua volta chiama il metodo di estensione `AddPushNotifications()` fornito dal pacchetto NuGet indicato.

<!---HONumber=August15_HO8-->