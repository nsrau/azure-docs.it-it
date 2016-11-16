## <a name="about-records"></a>Informazioni sui record

Ogni record DNS ha un nome e un tipo. I record sono organizzati in tipi diversi in base ai dati che contengono. Il tipo più comune è un record "A", che esegue il mapping di un nome a un indirizzo IPv4. Un altro tipo è un record "MX", che esegue il mapping di un nome a un server di posta elettronica.

DNS di Azure supporta tutti i tipi di record DNS comuni, inclusi A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT. Si noti che:

* I set di record SOA vengono creati automaticamente con ogni zona e non possono essere creati separatamente.
* I record SPF devono essere creati con il tipo di record TXT. Per altre informazioni, vedere [questa pagina](http://tools.ietf.org/html/rfc7208#section-3.1).

Nel servizio DNS di Azure i record vengono specificati usando nomi relativi. Un nome di dominio completo include il nome della zona, mentre un nome relativo non lo include. Ad esempio, il nome del record relativo "www" nella zona "contoso.com" genera il nome di record completo "www.contoso.com".

## <a name="about-record-sets"></a>Informazioni sui set di record

In alcuni casi è necessario creare più record DNS con un determinato nome e tipo. Si supponga, ad esempio, che il sito Web "www.contoso.com" sia ospitato in due diversi indirizzi IP. Questo sito Web richiede due diversi record A, uno per ogni indirizzo IP. Ecco un esempio di un set di record:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

DNS di Azure gestisce i record DNS usando set di record. Un set di record è la raccolta di record DNS con lo stesso nome e lo stesso tipo in una zona. La maggior parte dei set di record contiene un singolo record, ma non sono rari esempi come questo in cui un set di record contiene più di un record

I set di record SOA e CNAME sono eccezioni. Gli standard DNS non consentono più record con lo stesso nome per questi tipi.

La durata Time-to-Live o TTL specifica per quanto tempo ogni record viene memorizzato nella cache da parte dei client, prima che venga eseguita nuovamente la query. In questo esempio il valore TTL è pari a 3600 secondi o 1 ora. Il valore TTL viene specificato per il set di record, non per ogni record, quindi lo stesso valore viene usato per tutti i record all'interno di tale set di record.

#### <a name="wildcard-record-sets"></a>Set di record con caratteri jolly

DNS di Azure supporta [record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Questi dati vengono restituiti per le query con un nome corrispondente (a meno che non esiste una corrispondenza più vicina da un set di record non jolly). I set di record con caratteri jolly sono supportati per tutti i tipi di record tranne NS e SOA.

Per creare un set di record con caratteri jolly, usare il nome del set di record "\*". In alternativa, usare un nome con l'etichetta "\*", ad esempio, "\*.foo".

#### <a name="cname-record-sets"></a>Set di record CNAME

I set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Ad esempio, non è possibile creare contemporaneamente un record CNAME con il nome relativo "www" e un record A con il nome relativo '"www". Dal momento che il vertice della zona (name = ‘@’) contiene sempre i set di record NS e SOA creati quando è stata creata la zona, non è possibile creare un set di record CNAME al vertice della zona. Questi vincoli sono causati dagli standard DNS e non sono limitazioni di DNS di Azure.


<!--HONumber=Nov16_HO2-->


