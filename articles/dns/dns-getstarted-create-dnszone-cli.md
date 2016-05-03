<properties
   pageTitle="Introduzione a DNS di Azure utilizzando CLI| Microsoft Azure"
   description="Informazioni su come creare zone DNS per il DNS di Azure dettagliato per avviare l'hosting del dominio DNS utilizzando CLI"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="cherylmc"/>

# Introduzione a DNS di Azure utilizzando CLI


> [AZURE.SELECTOR]
- [Interfaccia della riga di comando di Azure](dns-getstarted-create-dnszone-cli.md)
- [PowerShell](dns-getstarted-create-dnszone.md)


Il dominio "contoso.com" può contenere una serie di record DNS, ad esempio "mail.contoso.com" (per un server di posta elettronica) e "www.contoso.com" (per un sito Web). Una zona DNS viene usata per ospitare i record DNS per un particolare dominio.

Per iniziare a ospitare il dominio è necessario prima di tutto creare una zona DNS. Qualsiasi record DNS creato per un particolare dominio si troverà all'interno di una zona DNS per il dominio.

Per queste istruzioni viene usata l'interfaccia della riga di comando di Microsoft Azure. Assicurarsi di eseguire l'aggiornamento alla versione più recente dell'interfaccia della riga di comando di Azure per usare i cmdlet di DNS di Azure.

## Configurare l'interfaccia della riga di comando di Azure

### Passaggio 1

Installare l'interfaccia della riga di comando di Azure. È possibile installare l'interfaccia della riga di comando di Azure per Windows, Linux o MAC. È necessario completare i passaggi seguenti prima di poter gestire DNS di Azure tramite l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). Per trovare tutti i comandi del provider di rete nell'interfaccia della riga di comando di Azure, usare il comando seguente:

	Azure network


>[AZURE.IMPORTANT] I comandi DNS richiedono la versione 0.9.8 di Azure CLI o versione successiva. Digitare `azure -v`per controllare quale versione di Azure CLI è attualmente installata nel computer.

### Passaggio 2

DNS di Azure usa Gestione risorse di Azure. Assicurarsi di passare alla modalità interfaccia della riga di comando per usare i comandi di Azure Resource Manager.

	Azure config mode arm

### Passaggio 3

Accedere all'account Azure.

    Azure login -u "username"

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali. Tenere presente che è possibile usare solo account aziendali (ORGID).

### Passaggio 4
Scegliere le sottoscrizioni ad Azure da utilizzare.

    Azure account set "subscription name"

Per visualizzare l'elenco delle sottoscrizioni disponibili, usare il comando "azure account list".

### Passaggio 5

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)

    Azure group create -n myresourcegroup --location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

### Passaggio 6

Il servizio DNS di Azure viene gestito dal provider di risorse Microsoft.Network. La sottoscrizione di Azure deve essere registrata per usare questo provider di risorse prima di poter usare DNS di Azure. Si tratta di un'operazione una tantum per ogni sottoscrizione.

	Azure provider register --namespace Microsoft.Network

## Tag

I tag sono un elenco di coppie nome-valore, usati da Gestione risorse di Azure per etichettare le risorse a scopo di fatturazione o di raggruppamento. Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md). L'interfaccia della riga di comando DNS di Azure supporta i tag nelle zone DNS specificate con il parametro facoltativo '-Tag'. L'esempio seguente illustra come creare una zona DNS con due tag: "project = demo" e "env = test":

	Azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## Creare una zona DNS

Una zona DNS viene creata utilizzando il comando `azure network dns zone create`. Nell'esempio seguente verrà creata una zona DNS denominata "contoso.com" nel gruppo di risorse denominato "MyResourceGroup":

    Azure network dns zone create myresourcegroup contoso.com


>[AZURE.NOTE] In Azure DNS, è necessario specificare i nomi di zona senza una terminazione ‘.’, ad esempio, ‘contoso.com’ anziché ‘contoso.com’.


La zona DNS è stata creata nel servizio DNS di Azure. La creazione di una zona DNS comporta anche la creazione dei record DNS seguenti:

- Il record "Start of Authority" (SOA), presente nella directory principale di ogni zona DNS.
- I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

Per visualizzare i record, usare il comando "azure network dns-record-set show":

	Usage: network dns record-set show <resource-group> <dns-zone-name> <name> <type>


Nell'esempio seguente, l’esecuzione del comando con il gruppo di risorse "myresourcegroup", nome del set di record "@" (per un record radice) e tipo "SOA" produrrà il seguente output:


	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> Per visualizzare i record NS creati con la zona, usare il comando seguente:

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE] I set di record alla radice (o "vertice") di una zona DNS usano "@" come nome del set di record.

Dopo aver creato la prima zona DNS, è possibile eseguirne il test usando gli strumenti DNS, ad esempio nslookup, DIG o il **cmdlet di PowerShell Resolve-DnsName**. Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono specificati nei record NS, ottenuti tramite il comando "azure network dns-record-set show" precedente. Assicurarsi di sostituire i valori corretti per la propria zona nel comando seguente.

L'esempio seguente usa DIG per eseguire una query sul dominio contoso.com usando i server dei nomi assegnati per la zona DNS. La query deve fare riferimento a un server dei nomi usato in precedenza `@<name server for the zone>` e al nome della zona tramite DIG.

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## Passaggi successivi

Dopo aver creato una zona DNS, è necessario creare [set di record e record](dns-getstarted-create-recordset-cli.md) per avviare la risoluzione dei nomi per il dominio Internet.<BR> È anche possibile apprendere [come gestire le zone DNS](dns-operations-dnszones-cli.md) e le relative operazioni per zone DNS.<BR> Informazioni su [come gestire i record DNS](dns-operations-recordsets-cli.md) e [automatizzare le operazioni di Azure con .NET SDK](dns-sdk.md).<BR> [Riferimento all'API REST di Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx).

<!---HONumber=AcomDC_0427_2016-->