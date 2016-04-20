<properties 
   pageTitle="Gestire set di record e record DNS in DNS di Azure usando l'interfaccia della riga di comando | Microsoft Azure" 
   description="Gestione dei set di record e dei record DNS in DNS di Azure quando si ospita il dominio in DNS di Azure. Tutti i comandi di CLI per le operazioni sui set di record e i record." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/04/2016"
   ms.author="joaoma"/>

# Come gestire i record DNS utilizzando CLI


> [AZURE.SELECTOR]
- [Portale di Azure](dns-operations-recordsets-portal.md)
- [Interfaccia della riga di comando di Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Questa guida descrive come gestire i set di record e i record per la zona DNS usando l'interfaccia della riga di comando multipiattaforma di Azure.

>[AZURE.NOTE] DNS di Azure è un servizio solo di Gestione risorse di Azure. Non include un’API ASM. È pertanto necessario garantire che l’interfaccia della riga di comando di Azure sia configurata per utilizzare la modalità di gestione risorse, utilizzando il comando 'azure config mode arm'.

>Se viene visualizzato "errore: 'dns' non è un comando di azure" è probabilmente perché si sta utilizzando l’interfaccia della riga di comando di in modalità ASM, non in modalità gestione risorse.

È importante comprendere la differenza tra i set di record DNS e i singoli record DNS. Un set di record è la raccolta di record con lo stesso nome e tipo in una zona. Per altre informazioni, vedere [Informazioni sui set di record e sui record](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records).

## Creare un set di record

I set di record vengono creati utilizzando il comando `azure network dns record-set create`. È necessario specificare il nome, la zona, il Time-to-Live (TTL) e il tipo di record del set di record.

Il nome del set di record deve essere un nome relativo, escluso il nome della zona. Ad esempio, il nome del set di record "www" nella zona "contoso.com" creerà un record impostato con il nome completo "www.contoso.com".

Per un set di record all'apice della zona, usare "@" come nome del set di record, includendo le virgolette. Il nome completo del set di record è quindi uguale al nome della zona, in questo caso "contoso.com".

DNS di Azure supporta i seguenti tipi di record: A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. I set di record di tipo SOA vengono creati automaticamente con ogni zona, non possono essere creati separatamente. Si noti che [il tipo di record SPF è stato deprecato dagli standard DNS a favore della creazione di record SPF utilizzando il tipo di record TXT](http://tools.ietf.org/html/rfc7208#section-3.1).

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT] I set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Ad esempio, è possibile creare contemporaneamente un record CNAME con il nome relativo "www" e un record A con il nome relativo '"www". Dal momento che il vertice della zona (name = ‘@’) contiene sempre i set di record NS e SOA creati quando viene creata la zona, ciò significa che non è possibile creare un set di record CNAME al vertice della zona. Questi vincoli sono causati dagli standard DNS, non sono limitazioni del servizio DNS di Azure.

### Record con caratteri jolly

DNS di Azure supporta [record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Questi dati vengono restituiti per le query con un nome corrispondente (a meno che non esiste una corrispondenza più vicina da un set di record non jolly). Per creare un set di record con caratteri jolly, utilizzare il nome del set di record "\*", o un nome con la prima etichetta è "\*", ad esempio, "\*.foo".

I set di record con caratteri jolly sono supportati per tutti i tipi di record tranne NS e SOA.

## Ottenere un set di record
Per recuperare un set di record esistente, usare `azure network dns record-set show`, specificando gruppo di risorse, nome relativo del set di record e tipo di record:

	azure network dns record-set show myresourcegroup contoso.com www A


## Elencare i set di record

È possibile elencare tutti i record in una zona DNS utilizzando il comando `azure network dns record-set list`:

### Opzione 1 
Elencare tutti i set di record. Verranno restituiti tutti i set di record, indipendentemente dal nome o dal tipo di record:

	azure network dns record-set list myresourcegroup contoso.com

### Opzione 2 

Elencare i set di record di un determinato tipo. Verranno restituiti tutti i set di record corrispondenti al tipo di record specificato (in questo caso, i record A):


	azure network dns record-set list myresourcegroup contoso.com A 

In entrambi i casi si specificherà il nome del gruppo di risorse e il nome di zona.

## Aggiungere un record a un set di record

I record vengono aggiunti al set di record utilizzando il `azure network dns record-set add-record`.

I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record. Ad esempio, quando si usa un set di record di tipo "A" sarà possibile specificare solo i record con il parametro "-a `<IPv4 address>`".

Gli esempi seguenti illustrano come creare un set di record di ogni tipo di record che contiene un singolo record.

### Creare un set di record A con un singolo record

Per creare set di record, utilizzare `azure network dns record-set create`, specificando gruppo di risorse, nome di zona, nome relativo del set di record, tipo di record e durata (TTL):
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE] Se il parametro --ttl non è definito, viene impostato il valore predefinito 4 (in secondi).


Dopo aver creato il set di record A, aggiungere l’indirizzo IPv4 al set di record con `azure network dns record-set add-record`:

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### Creare un set di record AAAA con un singolo record

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### Creare un set di record CNAME con un singolo record

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE] I record CNAME consentono solo un valore stringa singolo.

### Creare un set di record MX con un singolo record

In questo esempio viene usato il nome del set di record "@" per creare il record MX all'apice della zona (ad esempio "contoso.com"). Questo comportamento è comune per i record MX.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### Creare un set di record NS con un singolo record

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### Creare un set di record SRV con un singolo record

Se si crea un record SRV nella radice della zona, specificare solo \_servizio e \_protocollo nel nome del record, non è necessario includere anche ".@" nel nome del record

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### Creare un set di record TXT con un singolo record

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## Modifica dei set di record esistenti


Questo è illustrato nell'esempio seguente:

### Aggiornare un record in un set di record esistente

Per questo esempio si aggiungerà un altro indirizzo IP (1.2.3.4) a un set di record A (www) esistente:

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK


Si utilizzerà `azure network dns record-set delete-record` per rimuovere un valore esistente da un set di record:
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## Rimuovere un record da un set di record esistente

I record possono essere rimossi da un set di record utilizzando `azure network dns record-set delete-record` Si noti che il record che viene rimosso deve corrispondere esattamente a un record esistente, in tutti i parametri.

La rimozione dell'ultimo record da un set di record non elimina il set di record. Per altre informazioni, vedere [Eliminare un set di record](#delete-a-record-set) qui di seguito.


	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### Rimuovere un record AAAA da un set di record esistente

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Rimuovere un record CNAME da un set di record esistente

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### Rimuovere un record MX da un set di record esistente

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Rimuovere un record NS da un set di record esistente
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Rimuovere un record SRV da un set di record esistente

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### Rimuovere un record TXT da un set di record esistente

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## Eliminare un set di record
È possibile eliminare i set di record usando il cmdlet Remove-AzureDnsRecordSet.

>[AZURE.NOTE] Non è possibile eliminare i set di record SOA ed NS al vertice della zona (name = ‘@’) che vengono creati automaticamente quando viene creata la zona. Verranno eliminati automaticamente quando si elimina la zona.

Nell'esempio seguente, il set di record A "test-a" verrà rimosso dalla zona DNS contoso.com:

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

L'opzione ‘-q’ facoltativa può essere usata per eliminare la richiesta di conferma.


## Passaggi successivi

Dopo aver creato la zona e i record DNS, è possibile [delegare il dominio a DNS di Azure](dns-domain-delegation.md).<BR> Informazioni su come [gestire le zone DNS](dns-operations-dnszones-cli.md) con l'interfaccia della riga di comando.<BR> È anche possibile [automatizzare le operazioni usando .NET SDK](dns-sdk.md) per codificare le operazioni DNS di Azure nell'applicazione.

 

<!-----HONumber=AcomDC_0406_2016-->