#### <a name="create-an-a-record-set-with-single-record"></a>Creare un set di record A con un singolo record
Per creare un set di record, usare `azure network dns record-set create`. Specificare il gruppo di risorse, il nome della zona, il nome relativo del set di record, il tipo di record e la durata (TTL).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Dopo aver creato il set di record A, aggiungere l'indirizzo IPv4 al set di record con `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Creare un set di record AAAA con un singolo record
    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Creare un set di record CNAME con un singolo record
I record CNAME consentono solo un valore stringa singolo.

    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>Creare un set di record MX con un singolo record
In questo esempio viene usato il nome del set di record "@" per creare il record MX al vertice della zona, in questo caso "contoso.com". Questo comportamento è comune per i record MX.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>Creare un set di record NS con un singolo record
    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Creare un set di record PTR con un singolo record
In questo caso 'my-arpa-zone.com' è la zona ARPA che rappresenta l'intervallo IP dell'utente.  Ogni record PTR impostato in questa zona corrisponde a un indirizzo IP che rientra nell'intervallo IP.    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Creare un set di record SRV con un singolo record
Se si crea un record SRV nella radice della zona, è possibile specificare "_service" e "_protocol" nel nome del record. Non è necessario includere "@" nel nome del record.

    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>Creare un set di record TXT con un singolo record
    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"


<!--HONumber=Oct16_HO2-->


