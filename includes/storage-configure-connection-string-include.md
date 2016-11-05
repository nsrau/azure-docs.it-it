## Configurare una stringa di connessione di archiviazione
Nella libreria del client di archiviazione di Azure per .NET è possibile usare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. Per gestire nel modo migliore la stringa di connessione di archiviazione, usare un file di configurazione.

Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di archiviazione di Azure](../articles/storage/storage-configure-connection-string.md).

> [!NOTE]
> La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. È consigliabile proteggere sempre la chiave dell'account di archiviazione. Evitare di distribuirla ad altri utenti, impostarla come hardcoded o salvarla in un file di testo normale accessibile ad altri. Rigenerare la chiave tramite il portale di Azure se si ritiene che possa essere stata compromessa.
> 
> 

### Determinare l'ambiente di destinazione
Sono disponibili due opzioni relative all'ambiente per l'esecuzione degli esempi di questa guida:

* È possibile eseguire il codice nell'emulatore di archiviazione di Azure. L'emulatore di archiviazione è un ambiente locale che emula un account di archiviazione di Azure nel cloud. L'emulatore è un'opzione gratuita per il test e il debug del codice durante lo sviluppo dell'applicazione. L'emulatore usa un account e una chiave noti. Per altre informazioni, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](../articles/storage/storage-use-emulator.md).
* È possibile eseguire il codice con un account di archiviazione di Azure nel cloud. 

Se si imposta come destinazione un account di archiviazione nel cloud, immettere la chiave di accesso primaria per tale account tramite il portale di Azure. Per altre informazioni, vedere [Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> È possibile impostare come destinazione l'emulatore di archiviazione per evitare di incorrere negli eventuali costi associati al servizio Archiviazione di Azure. Se però si sceglie di impostare come destinazione un account di archiviazione di Azure nel cloud, i costi per eseguire questa esercitazione saranno minimi.
> 
> 

### Configurare la stringa di connessione con la configurazione .NET
Se l'applicazione viene eseguita in un dispositivo desktop o portatile, una macchina virtuale di Azure o un'app Web di Azure, salvare la stringa di connessione con la configurazione .NET. *Ad esempio*, nel file `web.config` o `app.config` dell'applicazione. Archiviare la stringa di connessione usando l'elemento `<appSettings>` come segue. Sostituire `account-name` con il nome dell'account di archiviazione e `account-key` con la chiave di accesso dell'account:

    <configuration>
          <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
          </appSettings>
    </configuration>

Ad esempio, l'impostazione di configurazione sarà simile a:

    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=account-key" />

Per impostare come destinazione l'emulatore di archiviazione, è possibile usare un collegamento con mapping al nome dell'account e alla chiave noti. In questo caso, l'impostazione della stringa di connessione sarà:

    <add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

### Configurare la stringa di connessione per un servizio cloud di Azure
Se l'applicazione viene eseguita in un servizio cloud di Azure, salvare la stringa di connessione tramite i file di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`). Per informazioni dettagliate sulla configurazione del servizio cloud di Azure, vedere [Come creare e distribuire un servizio Cloud](../articles/cloud-services/cloud-services-how-to-create-deploy.md).

<!---HONumber=AcomDC_0406_2016-->