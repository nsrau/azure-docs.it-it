## Informazioni sui record

Ogni record DNS ha un nome e un tipo. I record possono essere di tipi diversi in base ai dati in essi contenuti. Il tipo più comune è un record "A", che esegue il mapping di un nome a un indirizzo IPv4. Un altro tipo è un record "MX", che esegue il mapping di un nome a un server di posta elettronica.

DNS di Azure supporta tutti i tipi di record DNS comuni: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT. I set di record di tipo SOA vengono creati automaticamente con ogni zona, non possono essere creati separatamente. Si noti che i record SPF devono essere creati con il tipo di record TXT. Per altre informazioni, vedere [questa pagina](http://tools.ietf.org/html/rfc7208#section-3.1).

Nel servizio DNS di Azure, i record vengono specificati usando nomi relativi. Un nome di dominio completo include il nome della zona, mentre un nome relativo non lo include. Ad esempio, il nome del record relativo "www" nella zona "contoso.com" genera il nome di record completo "www.contoso.com".

## Informazioni sui set di record

In alcuni casi è necessario creare più record DNS con un determinato nome e tipo. Si supponga, ad esempio, che il sito Web www.contoso.com sia ospitato in due diversi indirizzi IP. Questa operazione richiede due diversi record A, uno per ogni indirizzo IP. Questo è un esempio di un set di record.

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

DNS di Azure gestisce i record DNS usando il set di record. Un set di record è la raccolta di record DNS con lo stesso nome e tipo in una zona. La maggior parte dei set di record contiene un singolo record, ma non sono rari esempi come quello precedente, in cui un set di record contiene più di un record

I set di record di tipo SOA e CNAME sono un'eccezione: gli standard DNS non consentono più record con lo stesso nome per questi tipi.

La durata Time-to-Live o TTL specifica per quanto tempo ogni record viene memorizzato nella cache da parte dei client prima che venga eseguita nuovamente la query. Nell'esempio precedente, il valore TTL è 3600 secondi o 1 ora. Il valore TTL viene specificato per il set di record, non per ogni record, quindi lo stesso valore viene usato per tutti i record all'interno di tale set di record.

#### Set di record con caratteri jolly

DNS di Azure supporta [record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Questi dati vengono restituiti per le query con un nome corrispondente (a meno che non esiste una corrispondenza più vicina da un set di record non jolly). I set di record con caratteri jolly sono supportati per tutti i tipi di record tranne NS e SOA.

Per creare un set di record con caratteri jolly, utilizzare il nome del set di record " * ", o un nome con la prima etichetta è " * ", ad esempio, " *.foo ".

#### Set di record CNAME

I set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Ad esempio, non è possibile creare contemporaneamente un record CNAME con il nome relativo "www" e un record A con il nome relativo '"www". Dal momento che il vertice della zona (name = ‘@’) contiene sempre i set di record NS e SOA creati quando viene creata la zona, ciò significa che non è possibile creare un set di record CNAME al vertice della zona. Questi vincoli sono causati dagli standard DNS, non sono limitazioni di DNS di Azure.


