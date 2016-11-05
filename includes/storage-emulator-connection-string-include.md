L'emulatore di archiviazione supporta un singolo account fisso e una chiave di autenticazione nota per l’autenticazione con chiave condivisa. Questo account e questa chiave sono le uniche credenziali con chiave condivisa consentite per l'utilizzo con l'emulatore di archiviazione. Sono:

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

> [!NOTE]
> La chiave di autenticazione supportata dall'emulatore di archiviazione è destinata solo al test della funzionalità del codice di autenticazione client. Non viene utilizzata per eventuali scopi di sicurezza. Non è possibile usare l'account di archiviazione di produzione e la chiave con l'emulatore di archiviazione. Si noti inoltre che non è consigliabile usare l'account di sviluppo con dati di produzione.
> 
> Si noti che l'emulatore di archiviazione supporta solo la connessione tramite HTTP. Tuttavia, HTTPS è il protocollo consigliato per accedere alle risorse in un account di archiviazione di produzione di Azure.
> 
> 

#### Connettersi all'account dell'emulatore utilizzando un collegamento
Il modo più semplice per eseguire la connessione all'emulatore di archiviazione dall’applicazione consiste nel configurare una stringa di connessione nel file di configurazione dell’applicazione che fa riferimento al collegamento `UseDevelopmentStorage=true`. Di seguito viene riportato un esempio di stringa di connessione all'emulatore di archiviazione in un file app.config:

    <appSettings>
      <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
    </appSettings>

#### Connettersi all'account dell'emulatore utilizzando il nome account ben noto e la chiave
Per creare una stringa di connessione che fa riferimento al nome e alla chiave dell'account emulatore, è necessario specificare gli endpoint per ciascuno dei servizi che si desidera utilizzare dall'emulatore nella stringa di connessione. Ciò è necessario per fare in modo che la stringa di connessione faccia riferimento agli endpoint dell’emulatore, che variano rispetto a quelli per un account di archiviazione di produzione. Ad esempio, il valore della stringa di connessione risulterà simile al seguente:

    DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
    AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
    BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
    TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
    QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1; 

Questo valore è identico al collegamento mostrato in precedenza, `UseDevelopmentStorage=true`.

#### Specificare un proxy HTTP
È inoltre possibile specificare un proxy HTTP da usare quando si esegue il test del servizio nell'emulatore di archiviazione. Ciò può essere utile per osservare le richieste HTTP e le risposte in fase di debug delle operazioni nei servizi di archiviazione. Per specificare un proxy, aggiungere `DevelopmentStorageProxyUri` l'opzione alla stringa di connessione, e impostarne il valore sull'URI del proxy. Ad esempio, di seguito viene presentata una stringa di connessione che punta all'emulatore di archiviazione e configura un proxy HTTP:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

<!---HONumber=Oct15_HO3-->