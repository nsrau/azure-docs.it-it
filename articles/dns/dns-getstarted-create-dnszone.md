<properties
   pageTitle="Introduzione a DNS di Azure | Microsoft Azure"
   description="Altre informazioni su come creare zone DNS per DNS di Azure. È una procedura dettagliata che consente di creare la prima zona DNS per iniziare a ospitare il proprio dominio DNS utilizzando PowerShell."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>  

# Creare una zona DNS mediante PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Interfaccia della riga di comando di Azure](dns-getstarted-create-dnszone-cli.md)

Questo articolo illustra i passaggi per creare una zona DNS con l'interfaccia della riga di comando. È anche possibile creare una zona DNS con PowerShell o il portale di Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Informazioni sugli Etag e sui tag

### <a name="etags"></a>ETag

Si supponga che due persone o due processi provino a modificare un record DNS nello stesso momento. Quale prevale? E il vincitore sa di aver appena sovrascritto solo le modifiche create da qualcun altro?

DNS di Azure usa gli Etag per gestire in modo sicuro le modifiche simultanee alla stessa risorsa. Ogni risorsa DNS (zona o set di record) ah un Etag associato. Quando viene recuperata una risorsa, viene recuperato anche il relativo valore Etag. Quando si aggiorna una risorsa, è possibile passare nuovamente il valore Etag in modo che DNS di Azure possa verificare che corrisponda al valore Etag sul server. Dal momento che ogni aggiornamento di una risorsa comporta la rigenerazione dell'Etag, una mancata corrispondenza del valore Etag indica che si è verificata una modifica simultanea. Gli ETag vengono usati anche quando si crea una nuova risorsa per garantire che questa non esista già.

Per impostazione predefinita, PowerShell in DNS di Azure usa gli Etag per bloccare le modifiche simultanee a zone e a set di record. L'opzione facoltativa *-Overwrite* può essere usata per disattivare i controlli di Etag e in questo caso le eventuali modifiche simultanee verranno sovrascritte.

A livello dell'API REST di DNS di Azure, gli ETag vengono specificati usando le intestazioni HTTP. Il relativo comportamento è illustrato nella tabella seguente:

|Intestazione|Comportamento|
|------|--------|
|None|PUT riesce sempre (nessun controllo di Etag)|
|If-match <etag>|PUT riesce solo se la risorsa esiste e l'Etag corrisponde|
|If-match * | PUT riesce solo se la risorsa esiste|
|If-none-match * |	PUT riesce solo se la risorsa non esiste|

### <a name="tags"></a>Tag

I tag sono diversi dagli Etag. I tag sono un elenco di coppie nome-valore usate da Azure Resource Manager per etichettare le risorse a scopo di fatturazione o di raggruppamento. Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

PowerShell del servizio DNS di Azure supporta i tag sia nelle zone che nei set di record specificati con il parametro `-Tag`.


## Prima di iniziare

Prima di iniziare la configurazione, verificare di essere in possesso degli elementi seguenti.
	
- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
	
- È necessario installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager (versione 1.0 o successiva). Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Passaggio 1: Eseguire l'accesso

Aprire la console di PowerShell e connettersi al proprio account. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Per connettersi, usare l'esempio seguente:

	Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

	Get-AzureRmSubscription 

Specificare la sottoscrizione da usare.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Passaggio 2: Creare un gruppo di risorse

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

	New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## Passaggio 3: Registrazione

Il servizio DNS di Azure viene gestito dal provider di risorse Microsoft.Network. Per poter usare il servizio DNS di Azure, la sottoscrizione di Azure deve essere registrata per l'uso di questo provider di risorse. Questa operazione viene eseguita una sola volta per ogni sottoscrizione.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## Passaggio 4: Creare una zona DNS

Viene creata una zona DNS con il cmdlet `New-AzureRmDnsZone`. Di seguito sono riportati alcuni esempi per la creazione di una zona DNS con o senza tag. Per altre informazioni sui tag, vedere la sezione relativa ai [tag](#tags) in questo articolo.

>[AZURE.NOTE] Nel servizio DNS di Azure i nomi delle zone devono essere specificati senza la terminazione **"."**, ad esempio "**contoso.com**" invece di "**contoso.com.**".

### Per creare una zona DNS

L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### Per creare una zona DNS con tag

L'esempio seguente illustra come creare una zona DNS con due tag, *project = demo* ed *env = test*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## Visualizzare i record

La creazione di una zona DNS comporta anche la creazione dei record DNS seguenti:

- Il record "*Start of Authority*" (SOA), presente nella directory principale di ogni zona DNS.

- I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio al servizio DNS di Azure](dns-domain-delegation.md).

Per visualizzare questi record, usare `Get-AzureRmDnsRecordSet`:

	Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}


I set di record alla radice, o *vertice*, di una zona DNS usano **@** come nome del set di record.


## Test

È possibile testare la zona DNS usando strumenti DNS come nslookup, DIG o il [cmdlet di PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono specificati nei record NS, ottenuti tramite il comando `Get-AzureRmDnsRecordSet` precedente. Assicurarsi di sostituire i valori corretti per la propria zona nel comando seguente.

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)


## Passaggi successivi

Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset.md) per avviare la risoluzione dei nomi per il dominio Internet.

<!---HONumber=AcomDC_0817_2016-->