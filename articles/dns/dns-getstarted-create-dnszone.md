<properties
   pageTitle="Introduzione a DNS di Azure | Microsoft Azure"
   description="Altre informazioni su come creare zone DNS per DNS di Azure. È una procedura dettagliata che consente di creare la prima zona DNS per iniziare a ospitare il proprio dominio DNS utilizzando PowerShell."
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
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# Introduzione a DNS di Azure utilizzando Powershell


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [Azure Powershell steps](dns-getstarted-create-dnszone.md)

Il dominio "contoso.com" può contenere una serie di record DNS, ad esempio "mail.contoso.com" (per un server di posta elettronica) e "www.contoso.com" (per un sito Web). Una zona DNS viene usata per ospitare i record DNS per un particolare dominio.<BR><BR> Per iniziare a ospitare il dominio è necessario prima di tutto creare una zona DNS. Qualsiasi record DNS creato per un particolare dominio sarà all'interno di una zona DNS per il dominio.<BR><BR> In queste istruzioni viene usato Microsoft Azure PowerShell. Assicurarsi di eseguire l'aggiornamento alla versione più recente di Azure PowerShell per usare i cmdlet di DNS di Azure. Gli stessi passaggi possono essere eseguiti anche usando l'interfaccia della riga di comando, le API REST o l'SDK di Microsoft Azure.<BR><BR>

## Configurare PowerShell in DNS di Azure

I passaggi seguenti devono essere completati prima di poter gestire DNS di Azure con Azure PowerShell.

### Passaggio 1
DNS di Azure usa Gestione risorse di Azure (ARM). Verificare di passare alla modalità di PowerShell per usare i cmdlet ARM. Altre informazioni sono disponibili in [Uso di Windows PowerShell con Gestione risorse](powershell-azure-resource-manager.md).<BR><BR>

		PS C:\> Switch-AzureMode -Name AzureResourceManager

è possibile visualizzare un avviso che indica 'Il cmdlet Switch-AzureMode è obsoleto e verrà rimosso in una versione futura'. Deve essere ignorato.

### Passaggio 2
 Accedere al proprio account Azure.<BR><BR>

		PS C:\> Add-AzureAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### Passaggio 3
Scegliere quali sottoscrizioni Azure usare. <BR>


		PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Per visualizzare un elenco delle sottoscrizioni disponibili, usare il cmdlet "Get-AzureSubscription".<BR>

### Passaggio 4
Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)<BR>

		PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"


Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.<BR>

### Passaggio 5

Il servizio DNS di Azure viene gestito dal provider di risorse Microsoft.Network. La sottoscrizione di Azure deve essere registrata per usare questo provider di risorse prima di poter usare DNS di Azure. Si tratta di un'operazione una tantum per ogni sottoscrizione.

	PS c:> Register-AzureProvider -ProviderNamespace Microsoft.Network



## ETag e tag
### ETag
Si supponga che due persone o due processi provino a modificare un record DNS nello stesso momento. Quale prevale? E il vincitore sa di aver appena sovrascritto solo le modifiche create da qualcun altro?<BR><BR> DNS di Azure usa gli Etag per gestire in modo sicuro le modifiche simultanee alla stessa risorsa. Ogni risorsa DNS (zona o set di record) ah un Etag associato. Quando viene recuperata una risorsa, viene recuperato anche il relativo valore Etag. Quando si aggiorna una risorsa, è possibile passare nuovamente il valore Etag in modo che DNS di Azure possa verificare che corrisponda al valore Etag sul server. Dal momento che ogni aggiornamento a una risorsa comporta la rigenerazione dell'Etag, una mancata corrispondenza del valore Etag indica che si è verificata una modifica simultanea. Gli ETag vengono usati anche quando si crea una nuova risorsa per garantire che questa non esista già.

Per impostazione predefinita, PowerShell in DNS di Azure usa gli Etag per bloccare le modifiche simultanee a zone e a set di record. L'opzione "-Overwrite" facoltativa può essere usata per disattivare i controlli di Etag, nel qual caso eventuali modifiche simultanee verranno sovrascritte.<BR><BR> A livello dell'API REST di DNS di Azure, gli ETag vengono specificati usando le intestazioni HTTP. Il relativo comportamento è illustrato nella tabella seguente:

|Intestazione|Comportamento|
|------|--------|
|Nessuno|PUT riesce sempre (nessun controllo di Etag)|
|If-Match <etag>|PUT riesce solo se la risorsa esiste e l'Etag corrisponde|
|If-match * |PUT riesce solo se la risorsa esiste|
|If-None-Match |* PUT riesce solo se la risorsa non esiste|

### Tag
I tag sono diversi dagli Etag. I tag sono un elenco di coppie nome-valore, usati da Gestione risorse di Azure per etichettare le risorse a scopo di fatturazione o di raggruppamento. Per altre informazioni sui tag, vedere Uso dei tag per organizzare le risorse di Azure. PowerShell di DNS di Azure supporta i tag sia nelle zone che nei set di record specificati usando il parametro "-Tag". L'esempio seguente illustra come creare una zona DNS con due tag: "project = demo" e "env = test":

	PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## Creare una zona DNS
Una zona DNS viene creata usando il cmdlet New-AzureDnsZone. Nell'esempio seguente verrà creata una zona DNS denominata "contoso.com" nel gruppo di risorse denominato "MyResourceGroup":<BR>

		PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

>[AZURE.NOTE]Nel servizio DNS di Azure, i nomi delle aree deve essere specificati senza una terminazione ".", ad esempio "contoso.com" invece di "contoso.com.".<BR>


La zona DNS è stata creata nel servizio DNS di Azure. La creazione di una zona DNS comporta anche la creazione dei record DNS seguenti:<BR>



- Il record "Start of Authority" (SOA), presente nella directory principale di ogni zona DNS.
- I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).<BR>

Per visualizzare questi record, usare Get-AzureDnsRecordSet:

		PS C:\> Get-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

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

>[AZURE.NOTE]I set di record alla radice (o "vertice") di una zona DNS usano "@" come nome del set di record.


Dopo aver creato la prima zona DNS, è possibile eseguirne il test usando gli strumenti DNS, ad esempio nslookup, dig o il [cmdlet di PowerShell Resolve-DnsName](https://technet.microsoft.com/it-IT/library/jj590781.aspx).<BR>

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per l'area sono espressi nei record NS, come elencato dal cmdlet Get-AzureDnsRecordSet sopra riportato; assicurarsi di sostituire i valori corretti per la zona nel comando seguente.<BR>

		C:\> nslookup
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


[Introduzione alla creazione di set di record e di record](dns-getstarted-create-recordset.md)<BR> [Come gestire le zone DNS](dns-operations-dnszones.md)<BR> [Come gestire i record DNS](dns-operations-recordsets.md)<BR> [Automatizzare le operazioni di Azure con .NET SDK](dns-sdk.md)<BR> [Informazioni di riferimento sulle API REST di DNS di Azure](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 

<!---HONumber=Oct15_HO1-->