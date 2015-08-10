<properties
   pageTitle="Introduzione a DNS di Azure | Microsoft Azure"
   description="Altre informazioni su come creare zone DNS per DNS di Azure. È una procedura dettagliata che consente di creare la prima zona DNS per iniziare a ospitare il proprio dominio DNS."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="joaoma"/>

# Introduzione a DNS di Azure

Il dominio "contoso.com" può contenere una serie di record DNS, ad esempio "mail.contoso.com" (per un server di posta elettronica) e "www.contoso.com" (per un sito Web). Una zona DNS viene usata per ospitare i record DNS per un particolare dominio.

Per iniziare a ospitare il dominio è necessario prima di tutto creare una zona DNS. Qualsiasi record DNS creato per un particolare dominio si troverà all'interno di una zona DNS per il dominio.

Per queste istruzioni viene usata l'interfaccia della riga di comando di Microsoft Azure. Assicurarsi di eseguire l'aggiornamento alla versione più recente dell'interfaccia della riga di comando di Azure per usare i cmdlet di DNS di Azure.

## Configurare l'interfaccia della riga di comando di Azure

### Passaggio 1

Installare l'interfaccia della riga di comando di Azure. È possibile installare l'interfaccia della riga di comando di Azure per Windows, Linux o MAC. È necessario completare i passaggi seguenti prima di poter gestire DNS di Azure tramite l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md). Per trovare tutti i comandi del provider di rete nell'interfaccia della riga di comando di Azure, usare il comando seguente:

	Azure network 

### Passaggio 2

DNS di Azure usa Gestione risorse di Azure (ARM). Assicurarsi di abilitare l'interfaccia della riga di comando di Azure per l'uso dei comandi di Gestione risorse di Azure e DNS.

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

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta della località del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

### Passaggio 6

Il servizio DNS di Azure viene gestito dal provider di risorse Microsoft.Network. La sottoscrizione di Azure deve essere registrata per usare questo provider di risorse prima di poter usare DNS di Azure. Si tratta di un'operazione una tantum per ogni sottoscrizione.

	Azure provider register --namespace Microsoft.Network

## Tag

I tag sono diversi dagli Etag. I tag sono un elenco di coppie nome-valore, usati da Gestione risorse di Azure per etichettare le risorse a scopo di fatturazione o di raggruppamento. Per altre informazioni sui tag, vedere Uso dei tag per organizzare le risorse di Azure. L'interfaccia della riga di comando di DNS di Azure supporta i tag sia nelle zone che nei set di record specificati usando il parametro '-Tag'. L'esempio seguente illustra come creare una zona DNS con due tag: "project = demo" e "env = test":

	Azure network dns-zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## Creare una zona DNS

Per creare una zona DNS, usare il comando "azure network dns-zone create". Nell'esempio seguente verrà creata una zona DNS denominata 'contoso.com' nel gruppo di risorse denominato 'MyResourceGroup':

    Azure network dns-zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE]Nel servizio DNS di Azure, i nomi delle aree deve essere specificati senza una terminazione ".", ad esempio 'contoso.com' invece di 'contoso.com.'.


La zona DNS è stata creata nel servizio DNS di Azure. La creazione di una zona DNS comporta anche la creazione dei record DNS seguenti:

\- Il record 'Start of Authority' (SOA), presente nella directory principale di ogni zona DNS. - I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere Delegare un dominio a DNS di Azure. Per visualizzare i record, usare il comando "azure network dns-record-set show":
   
	azure network dns-record-set show -g myresourcegroup --dns-zone "contoso.com" -n "@" --type SOA
	info:    Executing command network dns-record-set show
	DNS zone name: contoso.com
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
<BR> Per visualizzare i record NS creati, usare il comando seguente:

	azure network dns-record-set show -g myresourcegroup --dns-zone "contoso.com" -n "@" --type NS
	info:    Executing command network dns-record-set show
	DNS zone name: contoso.com
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

>[AZURE.NOTE]I set di record alla radice (o "vertice") di una zona DNS usano "@" come nome del set di record.

Dopo aver creato la prima zona DNS, è possibile testarla usando gli strumenti DNS, ad esempio nslookup, dig o il cmdlet di PowerShell Resolve-DnsName. Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono specificati nei record NS, ottenuti tramite il comando "azure network dns-record-set show" mostrato sopra. Assicurarsi di sostituire i valori corretti per la propria zona nel comando seguente.

L'esempio seguente usa dig per eseguire una query sul dominio contoso.com usando i server dei nomi assegnati per la zona DNS. La query deve fare riferimento a un server dei nomi usato in precedenza `@<name server for the zone>` e al nome della zona tramite DIG.

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

<!---HONumber=July15_HO5-->