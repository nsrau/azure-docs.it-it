<properties 
   pageTitle="Automazione delle operazioni relative a DNS e ai set di record con .NET SDK | Microsoft Azure" 
   description="Uso di .NET SDK per automatizzare tutte le operazioni DNS per DNS di Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/20/2015"
   ms.author="joaoma"/>
# Creazione di zone e set di record DNS con .NET SDK
È possibile automatizzare le operazioni per creare, eliminare o aggiornare zone, set di record e record DNS usando l'SDK per DNS con la libreria di gestione DNS per .NET. Un progetto completo di Visual Studio è disponibile [qui](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip).

## Pacchetti NuGet e dichiarazioni degli spazi dei nomi
Per usare il client DNS, è necessario installare il pacchetto NuGet "Azure DNS Management Library" e aggiungere al progetto gli spazi dei nomi di gestione DNS. Andare a Visual Studio, aprire un progetto o un nuovo progetto e andare a Strumenti e alla console Gestione pacchetti NuGet. Scaricare Azure DNS Management Library:

	using Microsoft.Azure;
	using Microsoft.Azure.Management.Dns;
	using Microsoft.Azure.Management.Dns.Models;

## Inizializzazione del client di gestione DNS

DnsManagementClient contiene i metodi e le proprietà necessari per gestire le zone e i set di record DNS. Perché il client possa accedere alla sottoscrizione, è necessario configurare le autorizzazioni corrette e generare un token AWT. Per altri dettagli, vedere "Autenticazione delle richieste di Gestione risorse di Azure".

	// get a token for the AAD application (see linked article for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Creazione o aggiornamento di una zona DNS

Per creare una zona DNS, un oggetto Zone viene creato e passato a dnsClient.Zones.CreateOrUpdate. Poiché le zone DNS non sono collegate a un'area specifica, la posizione viene impostata su "global".<BR>

Creare una zona DNS:

	// create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);


DNS di Azure supporta la concorrenza ottimistica denominata [Etags](dns-getstarted-create-dnszone.md#Etags-and-tags). Etag è una proprietà di Zone e IfNoneMatch è una proprietà in ZoneCreateOrUpdateParameters.

## Creazione o aggiornamento di record DNS
I record DNS vengono gestiti come set di record. Un set di record è il set di record con lo stesso nome e tipo di record in una zona. Per creare o aggiornare un set di record, l'oggetto RecordSet viene creato e passato a dnsClient.RecordSets.CreateOrUpdate. Si noti che il nome del set di record è relativo al nome della zona invece di essere il nome DNS completo. Anche in questo caso la posizione viene impostata su "global".
    
Creare alcuni set di record

	// make some records sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
DNS di Azure supporta la concorrenza ottimistica [Etags](dns-getstarted-create-dnszone.md#Etags-and-tags). Etag è una proprietà di RecordSet e IfNoneMatch è una proprietà in RecordSetCreateOrUpdateParameters.

## Acquisizione di zone e set di record
Le raccolte Zones e RecordSets consentono di ottenere rispettivamente zone e set di record. I set di record vengono identificati dal tipo, dal nome e dalla zona (e dal gruppo di risorse) in cui si trovano. Le zone vengono identificate dal nome e dal gruppo di risorse in cui si trovano.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

##Elenchi di zone e set di record

Per elencare le zone, usare il metodo List nella raccolta Zones. Per elencare i set di record, usare i metodi List o ListAll nella raccolta RecordSets. Il metodo List, a differenza del metodo ListAll, restituisce solo i set di record del tipo specificato.

Il seguente esempio mostra come ottenere un elenco di zone e set di record DNS:


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}
## Passaggi successivi

[Gestione traffico di Azure](traffic-manager-overview.md)

[Che cos'è DNS di Azure?](dns-overview.md)

[Progetto di esempio di Visual Studio SDK](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=Oct15_HO3-->