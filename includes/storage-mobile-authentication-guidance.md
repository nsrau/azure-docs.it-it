## Configurare l'applicazione per l'accesso all'archiviazione di Azure

Per autenticare l'applicazione per accedere ai servizi di archiviazione, è possibile procedere in due modi:

- Chiave condivisa: usare la Chiave condivisa solo a scopo di test
- Firma di accesso condiviso: usare la firma di accesso condiviso per le applicazioni per la produzione

### Chiave condivisa
L'autenticazione con la chiave condivisa implica che l'applicazione userà il nome account e la chiave dell'account per accedere ai servizi di archiviazione. Per mostrare rapidamente come usare questa libreria, in questa guida introduttiva verrà usata l'autenticazione con la chiave condivisa.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ] Il nome account e la chiave dell'account, che concedono l'accesso completo in lettura/scrittura all'account di archiviazione associato, verranno distribuiti a ogni persona che scarica l'app. Questa procedura **non** è consigliabile perché si rischia che la chiave venga compromessa da client non attendibili.

Quando si usa l'autenticazione con la chiave condivisa, si creerà una [stringa di connessione](../articles/storage/storage-configure-connection-string.md). La stringa di connessione è costituita da:

- **DefaultEndpointsProtocol**: è possibile scegliere HTTP o HTTPS. Tuttavia, è vivamente consigliato l'uso di HTTPS.
- **Nome account**: nome dell'account di archiviazione
- **Chiave account**: se si usa il [portale di Azure](https://portal.azure.com), passare all'account di archiviazione e fare clic sull'icona delle **chiavi** per trovare queste informazioni.
- (Facoltativo) **EndpointSuffix**: usata per i servizi di archiviazione in aree con suffissi dell'endpoint diversi, ad esempio per Azure China o Azure Governance.

Ecco un esempio di stringa di connessione che usa l'autenticazione con la chiave condivisa:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### Firme di accesso condiviso
Per un'applicazione mobile, il metodo consigliato per autenticare una richiesta da parte di un client in base al servizio di archiviazione di Azure è di usare una firma di accesso condiviso. La firma di accesso condiviso consente di concedere a un client l'accesso a una risorsa per un periodo di tempo specificato, con un set di autorizzazioni specificato. Il proprietario dell'account di archiviazione dovrà generare una firma di accesso condiviso che verrà usata dai client mobili. Per generare la firma di accesso condiviso da distribuire ai client, è possibile che si voglia scrivere un apposito servizio separato. A scopo di test, è possibile usare [Esplora archivi di Microsoft Azure](http://storageexplorer.com) o il [portale di Azure](https://portal.azure.com) per generare una firma di accesso condiviso. Quando si crea la firma di accesso condiviso, è possibile specificare l'intervallo di tempo in cui la firma di accesso condiviso è valida e le autorizzazioni che la firma di accesso condiviso concede al client.

L'esempio seguente mostra come usare Esplora archivi di Microsoft Azure per generare una firma di accesso condiviso.

1. Se non è già stato fatto, [installare Esplora archivi di Microsoft Azure](http://storageexplorer.com)

2. connessione alla sottoscrizione.

3. Fare clic sull'account di archiviazione e quindi sulla scheda "Azioni" in basso a sinistra. Fare clic su "Get Shared Access Signature" (Ottieni firma di accesso condiviso) per generare una "stringa di connessione" per la firma di accesso condiviso.

4. Di seguito è riportato un esempio di una stringa di connessione della firma di accesso condiviso che concede le autorizzazioni di lettura e scrittura a livello di servizio, contenitore e oggetto per il servizio BLOB dell'account di archiviazione.

  `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Come si può osservare, quando si usa una firma di accesso condiviso, non si espone la chiave dell'account nell'applicazione. Per altre informazioni sulla firma di accesso condiviso e sulle procedure consigliate per il relativo uso, vedere [Firme di accesso condiviso, parte 1: conoscere il modello di firma di accesso condiviso](../articles/storage/storage-dotnet-shared-access-signature-part-1.md).

<!---HONumber=AcomDC_0907_2016-->