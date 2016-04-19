### Analizzare la stringa di connessione

La libreria di Gestione configurazione di Microsoft Azure alla quale si è fatto riferimento in precedenza fornisce una classe per l'analisi della stringa di connessione da un file di configurazione. La [classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analizza le impostazioni di configurazione indipendentemente dal fatto che l'applicazione client sia in esecuzione sul PC desktop, su un dispositivo mobile, in una macchina virtuale di Azure o in un servizio cloud di Azure.

Aggiungere il codice seguente al metodo `Main()` in `program.cs`:

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		CloudConfigurationManager.GetSetting("StorageConnectionString"));

L'uso di Gestione configurazione di Azure è facoltativo. È anche possibile usare un'API, ad esempio la [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) di .NET Framework.

<!---HONumber=AcomDC_0413_2016-->