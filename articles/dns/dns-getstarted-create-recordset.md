<properties
   pageTitle="Creare un set di record e i record per una zona DNS | Microsoft Azure"
   description="Come creare i record host per DNS di Azure. Configurare i set di record e i record usando PowerShell"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="Adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma"/>


# Creare record DNS

Dopo aver creato la zona DNS, è necessario aggiungere i record DNS per il dominio. A tale scopo, è necessario comprendere i record e i set di record DNS.


## Informazioni sui set di record e sui record
Ogni record DNS ha un nome e un tipo.

Un nome _completo_ include il nome della zona, mentre un nome _relativo_ non lo include. Ad esempio, il nome del record relativo "www" nella zona "contoso.com" genera il nome di record completo "www.contoso.com".

>[AZURE.NOTE]Nel servizio DNS di Azure, i record vengono specificati usando nomi relativi.

I record possono essere di tipi diversi in base ai dati in essi contenuti. Il tipo più comune è un record "A", che associa un nome a un indirizzo IPv4. Un altro tipo è un record "MX", che associa un nome a un server di posta elettronica.

DNS di Azure supporta tutti i tipi di record DNS comuni: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

In alcuni casi, è necessario creare più record DNS con un determinato nome e tipo. Si supponga, ad esempio, che il sito Web www.contoso.com sia ospitato in due diversi indirizzi IP. Questa operazione richiede due diversi record A, uno per ogni indirizzo IP:

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Questo è un esempio di un set di record. Un set di record è la raccolta di record DNS con lo stesso nome e tipo in una zona. La maggior parte dei set di record contiene un singolo record, ma non sono rari esempi come quello precedente, in cui un set di record contiene più di un record (i set di record di tipo SOA e CNAME sono un'eccezione: gli standard DNS non consentono più record con lo stesso nome per questi tipi).

La durata Time-to-Live o TTL specifica per quanto tempo ogni record viene memorizzato nella cache da parte dei client prima che venga eseguita nuovamente la query. Nell'esempio precedente, il valore TTL è 3600 secondi o 1 ora. Il valore TTL viene specificato per il set di record, non per ogni record, quindi lo stesso valore viene usato per tutti i record all'interno di tale set di record.

>[AZURE.NOTE]DNS di Azure gestisce i record DNS usando il set di record.



## Creare set di record e record

Nell'esempio seguente verrà illustrato come creare un set di record e i record. Verrà usato il tipo di record "A" DNS; per altri tipi di record, vedere [Come gestire i record DNS](dns-operations-recordsets.md)


### Passaggio 1

Creare un set di record e assegnarlo a una variabile $rs:

	PS C:\>$rs = New-AzureDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

Il set di record ha il nome relativo "www" nella zona DNS "contoso.com" in modo che il nome completo dei record sarà "www.contoso.com". Il tipo di record è "A" e il valore TTL è 60 secondi.

>[AZURE.NOTE]Per creare un set di record nell'apice della zona (in questo caso "contoso.com"), usare il nome del record "@", incluse le virgolette. Si tratta di una convenzione comune di DNS.

Il set di record è vuoto ed è necessario aggiungere record per poter usare il set di record "www" appena creato.<BR>

### Passaggio 2

Aggiungere i record IPv4 al set di record "www" usando la variabile $rs assegnata durante la creazione del set di record al passaggio 1:

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

L'aggiunta di record a un set di record usando Add-AzureDnsRecordConfig è un'operazione non in linea. Solo la variabile locale $rs viene aggiornata.

### Passaggio 3
Eseguire il commit delle modifiche al set di record. Usare Set-AzureDnsRecordSet per caricare le modifiche al set di record in DNS di Azure:


	Set-AzureDnsRecordSet -RecordSet $rs

Le modifiche sono state completate. È possibile recuperare il set di record dal servizio DNS di Azure usando Get-AzureDnsRecordSet:


	PS C:\> Get-AzureDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}



È anche possibile usare nslookup o altri strumenti DNS per eseguire una query sul nuovo set di record.

>[AZURE.NOTE]Come durante la creazione della zona, se il dominio non è stato ancora delegato ai server dei nomi DNS di Azure, sarà necessario specificare in maniera esplicita l'indirizzo del server dei nomi per la propria zona.


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221



## Passaggi successivi
[Come gestire le zone DNS](dns-operations-dnszones.md)

[Come gestire i record DNS](dns-operations-recordsets.md)<BR>

[Automatizzare le operazioni di Azure con .NET SDK](dns-sdk.md)
 

<!---HONumber=July15_HO4-->