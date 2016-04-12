## Configurazione dell'ambiente di sviluppo

Configurare quindi l'ambiente di sviluppo in Visual Studio per poter provare gli esempi di codice forniti in questa guida.

### Creare un progetto di applicazione console di Windows

In Visual Studio creare una nuova applicazione console di Windows come illustrato di seguito:

![Creare un'applicazione console di Windows](./media/storage-development-environment-include/storage-development-environment-include-1.png)

È possibile usare la libreria client di archiviazione di Azure da qualsiasi tipo di applicazione .NET, ad esempio un servizio cloud di Azure, un'app Web di Azure, un'applicazione desktop o un'applicazione per dispositivi mobili. Per semplicità, in questa guida si userà un'applicazione console.

### Usare NuGet per installare i pacchetti necessari

Per completare questa esercitazione, è necessario installare due pacchetti nel progetto:

- [Libreria client di archiviazione di Microsoft Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): questo pacchetto fornisce l'accesso a livello di codice alle risorse dati nell'account di archiviazione.
- [Libreria Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): questo pacchetto fornisce una classe per l'analisi di una stringa di connessione da un file di configurazione, indipendentemente dalla posizione in cui viene eseguita l'applicazione.

Per ottenere entrambi i pacchetti, è possibile usare NuGet. A tale scopo, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.
2. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto della libreria client di archiviazione e le relative dipendenze.
3. Cercare online "ConfigurationManager" e fare clic su **Installa** per installare Gestione configurazione di Azure.

>[AZURE.NOTE] Il pacchetto della libreria client di archiviazione è incluso anche in [Azure SDK per .NET](https://azure.microsoft.com/downloads/). È tuttavia consigliabile installare anche la libreria client di archiviazione da NuGet per assicurarsi di avere sempre la versione più recente di questa libreria.

### Determinare l'ambiente di destinazione

Sono disponibili due opzioni relative all'ambiente per l'esecuzione degli esempi di questa guida:

- È possibile eseguire il codice con un account di archiviazione di Azure nel cloud. 
- È possibile eseguire il codice nell'emulatore di archiviazione di Azure. L'emulatore di archiviazione è un ambiente locale che emula un account di archiviazione di Azure nel cloud. L'emulatore è un'opzione gratuita per il test e il debug del codice durante lo sviluppo dell'applicazione. L'emulatore usa un account e una chiave noti. Per altre informazioni, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](../articles/storage/storage-use-emulator.md).

Se si imposta come destinazione un account di archiviazione nel cloud, immettere la chiave di accesso primaria per tale account tramite il portale di Azure. Per altre informazioni, vedere [Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [AZURE.NOTE] È possibile impostare come destinazione l'emulatore di archiviazione per evitare di incorrere negli eventuali costi associati al servizio Archiviazione di Azure. Se però si sceglie di impostare come destinazione un account di archiviazione di Azure nel cloud, i costi per eseguire questa esercitazione saranno minimi.

### Configurare la stringa di connessione di archiviazione

Nella libreria del client di archiviazione di Azure per .NET è possibile usare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. Per gestire nel modo migliore la stringa di connessione di archiviazione, usare un file di configurazione.

Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di archiviazione di Azure](../articles/storage/storage-configure-connection-string.md).

> [AZURE.NOTE] La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. È consigliabile proteggere sempre la chiave dell'account di archiviazione. Evitare di distribuirla ad altri utenti, impostarla come hardcoded o salvarla in un file di testo normale accessibile ad altri. Rigenerare la chiave tramite il portale di Azure se si ritiene che possa essere stata compromessa.

Per configurare la stringa di connessione, aprire il file `app.config` da Esplora soluzioni in Visual Studio. Aggiungere il contenuto dell'elemento `<appSettings>` illustrato di seguito. Sostituire `account-name` con il nome dell'account di archiviazione e `account-key` con la chiave di accesso dell'account:

	<configuration>
	    <startup> 
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
	    </startup>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Ad esempio, l'impostazione di configurazione sarà simile a:

	<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />

Per impostare come destinazione l'emulatore di archiviazione, è possibile usare un collegamento con mapping al nome dell'account e alla chiave noti. In questo caso, l'impostazione della stringa di connessione sarà:

	<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

<!---HONumber=AcomDC_0406_2016-->