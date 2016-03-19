<properties 
   pageTitle="Gestire set di record e record DNS in DNS di Azure | Microsoft Azure" 
   description="Gestione dei set di record e dei record DNS in DNS di Azure quando si ospita il dominio in DNS di Azure. Tutti i comandi di PowerShell per le operazioni sui set di record e i record." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/21/2016"
   ms.author="joaoma"/>

# Come gestire i record DNS utilizzando PowerShell


> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Questa guida descrive come gestire i set di record e i record per la zona DNS.

È importante comprendere la differenza tra i set di record DNS e i singoli record DNS. Un set di record è la raccolta di record con lo stesso nome e tipo in una zona. Per altre informazioni, vedere [Informazioni sui set di record e sui record](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Creare un set di record

I set di record vengono creati usando il cmdlet New-AzureRmDnsRecordSet. È necessario specificare il nome, la zona, il Time-to-Live (TTL) e il tipo di record del set di record.

Il nome del set di record deve essere un nome relativo, escluso il nome della zona. Ad esempio, il nome del set di record "www" nella zona "contoso.com" creerà un record impostato con il nome completo "www.contoso.com".

Per un set di record all'apice della zona, usare "@" come nome del set di record, includendo le virgolette. Il nome completo del set di record è quindi uguale al nome della zona, in questo caso "contoso.com".

DNS di Azure supporta i seguenti tipi di record: A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. I set di record di tipo SOA vengono creati automaticamente con ogni zona, non possono essere creati separatamente.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Se esiste già un set di record, il comando non riuscirà a meno che non venga usata l'opzione -Overwrite. L'opzione "-Overwrite" attiverà una richiesta di conferma, che può essere eliminata usando l'opzione -Force.

Nell'esempio precedente la zona viene specificata usando un oggetto di zona, restituito da Get-AzureRmDnsZone o New-AzureRmDnsZone. In alternativa, è anche possibile specificare la zona in base al nome della zona e del gruppo di risorse:

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

New-AzureRmDnsRecordSet restituisce un oggetto locale che rappresenta il set di record creato nel DNS di Azure.

>[AZURE.IMPORTANT] I set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Ad esempio, è possibile creare contemporaneamente un record CNAME con il nome relativo "www" e un record A con il nome relativo '"www". Dal momento che il vertice della zona (name = ‘@’) contiene sempre i set di record NS e SOA creati quando viene creata la zona, ciò significa che non è possibile creare un set di record CNAME al vertice della zona. Questi vincoli sono causati dagli standard DNS, non sono limitazioni del servizio DNS di Azure.

### Record con caratteri jolly

DNS di Azure supporta [record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Questi dati vengono restituiti per le query con un nome corrispondente (a meno che non esiste una corrispondenza più vicina da un set di record non jolly).

Per creare un set di record con caratteri jolly, utilizzare il nome del set di record "*", o un nome con la prima etichetta è "*", ad esempio, "*.foo".

I set di record con caratteri jolly sono supportati per tutti i tipi di record tranne NS e SOA.

## Ottenere un set di record

Per recuperare un set di record esistente, usare "Get-AzureRmDnsRecordSet", specificando il nome relativo del set di record, il tipo di record e la zona:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

Come per New-AzureRmDnsRecordSet, il nome del record deve essere un nome relativo, cioè deve escludere il nome della zona. La zona può essere specificata usando un oggetto di zona (vedere sopra) o nome della zona e nome gruppo di risorse:

	PS C:\> $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Get-AzureRmDnsRecordSet restituisce un oggetto locale che rappresenta il set di record creato nel DNS di Azure.

## Elencare i set di record
Omettendo i parametri –Name e/o –RecordType, è possibile usare Get-AzureRmDnsRecordSet anche per elencare set di record:

### Opzione 1 

Elencare tutti i set di record. Verranno restituiti tutti i set di record, indipendentemente dal nome o dal tipo di record:

	PS C:\> $list = Get-AzureRmDnsRecordSet -Zone $zone

### Opzione 2 

Elencare i set di record di un determinato tipo. Verranno restituiti tutti i set di record corrispondenti al tipo di record specificato (in questo caso, i record A):

	PS C:\> $list = Get-AzureRmDnsRecordSet –RecordType A -Zone $zone 

In entrambi i casi, la zona può essere specificata usando un oggetto di zona (come mostrato) o specificando i parametri –ZoneName e –ResourceGroupName.

## Aggiungere un record a un set di record

I record vengono aggiunti ai set di record usando il cmdlet Add-AzureRmDnsRecordConfig. Si tratta di un'operazione non in linea, in cui viene modificato solo l'oggetto locale che rappresenta il set di record.

I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record. Ad esempio, quando si usa un set di record di tipo "A" sarà possibile specificare solo i record con il parametro "IPv4Address".

Le chiamate aggiuntive al componente Add-AzureRmDnsRecordConfig possono aggiungere altri record a ogni set di record. È possibile aggiungere fino a 100 record a qualsiasi set di record. Tuttavia, i set di record di tipo CNAME possono contenere al massimo 1 record e un set di record non può contenere due record identici. È possibile creare set di record vuoti (con zero record), che non vengono però visualizzati nei server dei nomi DNS di Azure.

Quando il set di record contiene la raccolta di record preferita, è necessario eseguirne il commit usando il cmdlet Set-AzureRmDnsRecordSet, che sostituisce il set di record esistente nel servizio DNS di Azure con il set di record specificato. Gli esempi seguenti illustrano come creare un set di record di ogni tipo di record che contiene un singolo record.

### Creare un set di record A con un singolo record

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

La sequenza delle operazioni per creare un record può anche essere "reindirizzata", passando l'oggetto del set di record usando il pipe anziché come parametro. Ad esempio:

	PS C:\> New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Creare un set di record AAAA con un singolo record

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creare un set di record CNAME con un singolo record

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creare un set di record MX con un singolo record

In questo esempio viene usato il nome del set di record "@" per creare il record MX all'apice della zona (ad esempio "contoso.com"). Questo comportamento è comune per i record MX.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creare un set di record NS con un singolo record

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creare un set di record SRV con un singolo record

Se si crea un record SRV nella radice della zona, specificare solo \_servizio e \_protocollo nel nome del record, non è necessario includere anche ".@" nel nome del record

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creare un set di record TXT con un singolo record

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Modifica dei set di record esistenti

La modifica dei set di record esistenti segue un modello simile alla creazione di record. La sequenza delle operazioni è la seguente:

1.	Recuperare il set di record esistente usando Get-AzureRmDnsRecordSet.
2.	Modificare il set di record con l'aggiunta di record, la rimozione dei record, la modifica dei parametri di record o la modifica della durata (TTL) del set di record. Si tratta di operazioni di modifica non in linea, in cui viene modificato solo l'oggetto locale che rappresenta il set di record.
3.	Eseguire il commit delle modifiche usando il cmdlet Set-AzureRmDnsRecordSet. Sostituisce il set di record esistente nel servizio DNS di Azure con il set di record specificato.

Questo è illustrato nell'esempio seguente:

### Aggiornare un record in un set di record esistente

Per questo esempio si modificherà l'indirizzo IP di un record A esistente:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

Il cmdlet Set-AzureRmDnsRecordSet usa i controlli "etag" per verificare che le modifiche simultanee non vengano sovrascritte. Usare il flag "-Overwrite" per disattivare questi controlli. Per altre informazioni, vedere Etag e tag.

### Modificare il record SOA

>[AZURE.NOTE] Non è possibile aggiungere o rimuovere i record dal set di record SOA creato automaticamente set al vertice della zona (name = ‘@’), ma è possibile modificare i parametri all'interno del record SOA e la durata (TTL) del set di record.

L'esempio seguente mostra come modificare la proprietà "Email" del record SOA:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Modificare i record NS al vertice della zona

>[AZURE.NOTE] Non è possibile aggiungere, rimuovere o modificare i record nel set di record NS creato automaticamente al vertice della zona (name = ‘@’). L'unica modifica consentita consiste nel modificare la durata (TTL) del set di record.

L'esempio seguente illustra come modificare la proprietà della durata (TTL) del set di record NS:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Aggiungere record a un set di record esistente

In questo esempio verranno aggiunti due ulteriori record MX al set di record esistente:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

## Rimuovere un record da un set di record esistente

È possibile rimuovere i record da un set di record usando il cmdlet Remove-AzureRmDnsRecordConfig. Si noti che il record che viene rimosso deve corrispondere esattamente a un record esistente, in tutti i parametri. È necessario eseguire il commit delle modifiche usando il cmdlet Set-AzureRmDnsRecordSet.

La rimozione dell'ultimo record da un set di record non elimina il set di record. Per altre informazioni, vedere [Eliminare un set di record](#delete-a-record-set) qui di seguito.


	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

La sequenza delle operazioni per rimuovere un record da un set di record può anche essere "reindirizzata", passando l'oggetto del set di record usando il pipe anziché come parametro. Ad esempio:

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Rimuovere un record AAAA da un set di record esistente

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record CNAME da un set di record esistente

Dal momento che un set di record CNAME può contenere al massimo un record, la rimozione di tale record lascerà un set di record vuoto.

	PS C:\> $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record MX da un set di record esistente

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record NS da un set di record esistente
	
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record SRV da un set di record esistente

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record TXT da un set di record esistente

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Eliminare un set di record
È possibile eliminare i set di record usando il cmdlet Remove-AzureRmDnsRecordSet.

>[AZURE.NOTE] Non è possibile eliminare i set di record SOA ed NS al vertice della zona (name = ‘@’) che vengono creati automaticamente quando viene creata la zona. Verranno eliminati automaticamente quando si elimina la zona.

Usare uno dei tre metodi seguenti per rimuovere un set di record:

### Opzione 1

Specificare tutti i parametri per nome:

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]

L'opzione "-Force" facoltativa può essere usata per eliminare la richiesta di conferma.

### Opzione 2

Specificare il set di record per nome e tipo, specificare la zona in base all'oggetto:

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Opzione 3

Specificare il set di record in base all'oggetto:

	PS C:\> Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Specificare il set di record usando un oggetto consente di abilitare i controlli "etag" per garantire che le modifiche simultanee non vengano eliminate. Il flag "-Overwrite" facoltativo disattiva questi controlli. Per altre informazioni, vedere [Etag e tag](../dns-getstarted-create-dnszone#Etags-and-tags).

L'oggetto del set di record può essere anche reindirizzato invece che passato come parametro:

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

##Vedere anche

[Creare record DNS](dns-getstarted-create-recordset.md)<BR> [Come gestire le zone DNS utilizzando PowerShell](dns-operations-dnszones.md)<BR> [Creazione di zone e set di record DNS con .NET SDK](dns-sdk.md)
 

<!---HONumber=AcomDC_0128_2016-->