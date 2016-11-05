### Creare l'applicazione console e ottenere l'assembly
Per creare una nuova applicazione console in Visual Studio e installare il pacchetto NuGet contenente la libreria client di archiviazione di Azure:

1. In Visual Studio scegliere **File > Nuovo progetto** e quindi **Windows > Applicazione console** dall'elenco di modelli di Visual C#.
2. Specificare un nome per l'applicazione console e quindi fare clic su **OK**.
3. Dopo aver creato il progetto, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto della libreria client di archiviazione di Azure per .NET e le dipendenze.

Anche gli esempi di codice in questo articolo usano la [libreria di Gestione configurazione di Microsoft Azure](https://msdn.microsoft.com/library/azure/mt634646.aspx) per recuperare la stringa di connessione di archiviazione da un file app.config nell'applicazione console. Gestione configurazione di Azure permette di recuperare la stringa di connessione in fase di esecuzione indipendentemente dal fatto che l'applicazione sia in esecuzione in Microsoft Azure o in un'applicazione Web, desktop o per dispositivi mobili.

Per installare il pacchetto di Gestione configurazione di Azure, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Cercare online "ConfigurationManager" e fare clic su **Installa** per installare il pacchetto.

L'uso di Gestione configurazione di Azure è facoltativo. È anche possibile usare un'API, come la [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) di .NET Framework.

<!---HONumber=AcomDC_0309_2016-->