Le funzionalità relative ai dati sono già abilitate nel progetto server scaricato dal portale.

Nel progetto ASP.NET dovrebbero essere visualizzati gli elementi seguenti:

* I pacchetti NuGet `Microsoft.Azure.Mobile.Server.Tables` e `Microsoft.Azure.Mobile.Server.Entity` sono installati.

* In WebApiConfig.cs il metodo `UseDefaultConfiguration()` viene chiamato sull'oggetto MobileAppConfiguration, che a sua volta chiama il metodo di estensione `AddTablesWithEntityFramework()` fornito dal pacchetto NuGet indicato.

<!---HONumber=August15_HO6-->