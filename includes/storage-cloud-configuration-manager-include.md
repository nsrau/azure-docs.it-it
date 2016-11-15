La [libreria di Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) fornisce una classe per l'analisi della stringa di connessione da un file di configurazione. La [classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analizza le impostazioni di configurazione indipendentemente dal fatto che l'applicazione client sia in esecuzione sul PC desktop, su un dispositivo mobile, in una macchina virtuale di Azure o in un servizio cloud di Azure.

Per fare riferimento al pacchetto CloudConfigurationManager, aggiungere l'istruzione `using` seguente alla classe:

    using Microsoft.Azure;    //Namespace for CloudConfigurationManager

Ecco un esempio che illustra come recuperare una stringa di connessione da un file di configurazione:

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

L'uso di Gestione configurazione di Azure è facoltativo. È anche possibile usare un'API, come la [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)di .NET Framework.



<!--HONumber=Nov16_HO2-->


