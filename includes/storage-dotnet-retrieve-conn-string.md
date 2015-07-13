### Recupero della stringa di connessione
Per rappresentare le informazioni dell'account di archiviazione, è possibile utilizzare il tipo **CloudStorageAccount**. Se si intende usare un modello di progetto di Azure e/o si dispone di un riferimento allo spazio dei nomi Microsoft.WindowsAzure.CloudConfigurationManager, è possibile usare il tipo **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se si intende creare un'applicazione senza riferimenti a Microsoft.WindowsAzure.CloudConfigurationManager e la stringa di connessione si trova nel `web.config` o `app.config` come illustrato in precedenza, per recuperarla è possibile usare **ConfigurationManager**. Sarà necessario aggiungere un riferimento a System.Configuration.dll al progetto e aggiungere un'altra dichiarazione dello spazio dei nomi:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"]);

<!---HONumber=62-->