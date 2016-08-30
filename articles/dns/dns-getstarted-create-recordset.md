<properties
   pageTitle="Creare record e set di record per una zona DNS con PowerShell | Microsoft Azure"
   description="Come creare i record host per DNS di Azure. Configurare i set di record e i record usando PowerShell"
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



# Creare record e set di record DNS con PowerShell


> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interfaccia della riga di comando di Azure](dns-getstarted-create-recordset-cli.md)

Questo articolo illustra la creazione di record e set di record con Windows PowerShell. Dopo aver creato la zona DNS, è necessario aggiungere i record DNS per il dominio. A tale scopo, è necessario comprendere i record e i set di record DNS.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Verificare che sia disponibile l'ultima versione di PowerShell

Verificare di aver installato la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Creare un set di record e un record

Questa sezione mostra come creare un record e un set di record.


### 1\. Eseguire la connessione alla sottoscrizione

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

	Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

	Get-AzureRmSubscription

Specificare la sottoscrizione da usare.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Per altre informazioni sull'uso di PowerShell, vedere [Uso di Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).


### 2\. Creare un set di record

I set di record vengono creati usando il cmdlet `New-AzureRmDnsRecordSet`. Quando si crea un set di record, è necessario specificare il nome, la zona, il Time-to-Live (TTL) e il tipo di record.

Per creare un set di record nell'apice della zona, in questo caso "contoso.com", usare il nome del record "@", incluse le virgolette. Si tratta di una convenzione comune di DNS.

L'esempio seguente illustra la creazione di un set di record con il nome relativo "www" nella zona DNS "contoso.com". Il nome completo del set di record sarà "www.contoso.com". Il tipo di record è "A" e il valore TTL è 60 secondi. Con il completamento di questo passaggio si avrà un set di record "www" vuoto assegnato alla variabile *$rs*.

	$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### Se un set di record esiste già

Se un set di record esiste già, il comando non riuscirà a meno che non venga usata l'opzione *-Overwrite*. L'opzione *-Overwrite* attiverà una richiesta di conferma, che può essere eliminata usando l'opzione *-Force*.


	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


In questo esempio specificare la zona usando il nome della zona e del gruppo di risorse. In alternativa, è possibile specificare un oggetto di zona, come restituito da `Get-AzureRmDnsZone` o `New-AzureRmDnsZone`.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet` restituisce un oggetto locale che rappresenta il set di record creato nel servizio DNS di Azure.

### 3\. Aggiungere un record

Per usare il set di record "www" appena creato è necessario aggiungervi record. È possibile aggiungere record IPv4 *A* al set di record "www" usando l'esempio seguente. Questo esempio si basa sulla variabile *$rs* impostata nel passaggio precedente.

L'aggiunta di record a un set di record usando `Add-AzureRmDnsRecordConfig` è un'operazione offline. Solo la variabile locale *$rs* viene aggiornata.


	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### 4\. Eseguire il commit delle modifiche

Eseguire il commit delle modifiche al set di record. Usare `Set-AzureRmDnsRecordSet` per caricare le modifiche al set di record in DNS di Azure.

	Set-AzureRmDnsRecordSet -RecordSet $rs

### 5\. Recuperare il set di record

È possibile recuperare il set di record dal servizio DNS di Azure usando `Get-AzureRmDnsRecordSet`, come illustrato nell'esempio seguente.


	Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}


È anche possibile usare nslookup o altri strumenti DNS per eseguire una query sul nuovo set di record.

Se il dominio non è stato ancora delegato ai server dei nomi DNS di Azure, sarà necessario specificare in maniera esplicita il nome, il server e l'indirizzo per la propria zona.


	nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221

## Per creare un set di record di ogni tipo con un singolo record


Gli esempi seguenti illustrano come creare un set di record di ogni tipo di record. Ogni set di record contiene un singolo record.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## Passaggi successivi

[Come gestire le zone DNS usando PowerShell](dns-operations-dnszones.md)

[Gestire record e set di record DNS con PowerShell](dns-operations-recordsets.md)

[Automatizzare le operazioni di Azure con .NET SDK](dns-sdk.md)

<!---HONumber=AcomDC_0817_2016-->