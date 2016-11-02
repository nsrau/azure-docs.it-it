<properties
   pageTitle="Gestire set di record e record DNS con il portale di Azure | Microsoft Azure"
   description="Gestione dei set di record e dei record DNS in DNS di Azure quando si ospita il dominio in DNS di Azure. Tutti i comandi di PowerShell per le operazioni sui set di record e i record."
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

# Gestire record e set di record DNS con PowerShell



> [AZURE.SELECTOR]
- [Portale di Azure](dns-operations-recordsets-portal.md)
- [Interfaccia della riga di comando di Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



Questo articolo descrive come gestire i set di record e i record per la zona DNS usando Windows PowerShell.

È importante comprendere la differenza tra i set di record DNS e i singoli record DNS. Un set di record è la raccolta di record con lo stesso nome e lo stesso tipo in una zona. Per altre informazioni vedere [Creare set di record e record DNS mediante il portale di Azure](dns-getstarted-create-recordset-portal.md).

Per gestire i record e i set di record, è necessario installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Per altre informazioni sull'uso di PowerShell, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).



## Creare un nuovo set di record e un record

Per creare un set di record con PowerShell, vedere [Creare record e set di record DNS con PowerShell](dns-getstarted-create-recordset.md).

## Ottenere un set di record

Per recuperare un set di record esistente, usare `Get-AzureRmDnsRecordSet`. Specificare il nome relativo del set di record, il tipo di record e la zona.

	$rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Come per `New-AzureRmDnsRecordSet`, il nome del record deve essere un nome relativo, ovvero deve escludere il nome della zona.

È possibile specificare la zona usando il nome della zona e il nome del gruppo di risorse oppure un oggetto di zona:

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet` restituisce un oggetto locale che rappresenta il set di record creato nel servizio DNS di Azure.

## Elencare i set di record

È anche possibile usare`Get-AzureRmDnsRecordSet` per elencare i set di record omettendo i parametri *–Name* e/o *–RecordType*.

### Per elencare tutti i set di record

Questo esempio restituisce tutti i set di record, indipendentemente dal nome o dal tipo di record:

	$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### Per elencare i set di record di un determinato tipo

Questo esempio restituisce tutti i set di record corrispondenti al tipo di record specificato. In questo caso, il set di record restituito è record "A":

	$list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

È possibile specificare la zona usando i parametri *–ZoneName* e *–ResourceGroupName*, come illustrato, oppure specificando un oggetto di zona:

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$list = Get-AzureRmDnsRecordSet -Zone $zone

## Aggiungere un record a un set di record

Aggiungere record al set di record usando il cmdlet `Add-AzureRmDnsRecordConfig`. Si tratta di un'operazione offline. Solo l'oggetto locale che rappresenta il set di record viene modificato.

I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record. Ad esempio, quando si usa un set di record di tipo "A" sarà possibile specificare solo i record con il parametro *-IPv4Address*.

Le chiamate aggiuntive a `Add-AzureRmDnsRecordConfig` possono aggiungere altri record a ogni set di record. È possibile aggiungere fino a 20 record a qualsiasi set di record. Tuttavia, i set di record di tipo "CNAME" possono contenere al massimo 1 record e un set di record non può contenere due record identici. È possibile creare set di record vuoti, con zero record, che non vengono però visualizzati nei server dei nomi DNS di Azure.

Una volta che il set di record contiene la raccolta di record desiderata, è necessario eseguirne il commit usando il cmdlet `Set-AzureRmDnsRecordSet`. Dopo che è stato eseguito il commit di un set di record, questo sostituisce il set di record esistente nel servizio DNS di Azure.

### Per creare un set di record A con un singolo record

	$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

La sequenza delle operazioni per creare un record può anche essere *reindirizzata* , ovvero l'oggetto del set di record viene passato usando il pipe anziché come parametro. Ad esempio:

	New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Altri esempi di tipi di record

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## Modificare i set di record esistenti

I passaggi per la modifica di un set di record esistente sono simili ai passaggi per la creazione di record. La sequenza delle operazioni è la seguente:

1.	Recuperare il set di record esistente usando `Get-AzureRmDnsRecordSet`.

2.	Modificare il set di record con l'aggiunta di record, la rimozione dei record, la modifica dei parametri di record o la modifica della durata (TTL) del set di record. Si tratta di un'operazione offline. Solo l'oggetto locale che rappresenta il set di record viene modificato.

3.	Confermare le modifiche usando il cmdlet `Set-AzureRmDnsRecordSet`. Sostituisce il set di record esistente nel servizio DNS di Azure.


### Per aggiornare un record in un set di record esistente

In questo esempio modificheremo l'indirizzo IP di un record "A" esistente:

	$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Ipv4Address = "134.170.185.46"
	Set-AzureRmDnsRecordSet -RecordSet $rs

Il cmdlet `Set-AzureRmDnsRecordSet` usa i controlli etag per verificare che le modifiche simultanee non vengano sovrascritte. Usare il flag *-Overwrite* per disattivare questi controlli. Per altre informazioni, vedere [Etag e Tag](dns-getstarted-create-dnszone.md#tagetag).

### Per modificare un record SOA

Non è possibile aggiungere o rimuovere record dal set di record SOA creato automaticamente al vertice della zona (name = "@"). È tuttavia possibile modificare i parametri all'interno del record SOA, ad eccezione di "Host", e la durata (TTL) del set di record.

L'esempio seguente mostra come modificare la proprietà *Email* del record SOA:

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Email = "admin.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Per modificare i record NS al vertice della zona

Non è possibile aggiungere, rimuovere o modificare i record nel set di record NS creato automaticamente al vertice della zona (name = "@"). L'unica modifica consentita consiste nel modificare la durata (TTL) del set di record.

L'esempio seguente illustra come modificare la proprietà della durata (TTL) del set di record NS:

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Ttl = 300
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Per aggiungere record a un set di record esistente

In questo esempio verranno aggiunti due ulteriori record MX al set di record esistente:

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	Set-AzureRmDnsRecordSet -RecordSet $rs

## Rimuovere un record da un set di record esistente

È possibile rimuovere i record da un set di record usando `Remove-AzureRmDnsRecordConfig`. Il record che viene rimosso deve corrispondere esattamente a un record esistente in tutti i parametri. È necessario eseguire il commit delle modifiche usando `Set-AzureRmDnsRecordSet`.

La rimozione dell'ultimo record da un set di record non elimina il set di record. Per altre informazioni, vedere [Eliminare un set di record](#delete-a-record-set) di seguito.


	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

La sequenza delle operazioni per rimuovere un record da un set di record può anche essere reindirizzata , ovvero l'oggetto del set di record viene passato usando il pipe anziché come parametro. Ad esempio:

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Rimuovere un record AAAA da un set di record esistente

	$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record CNAME da un set di record esistente

Dal momento che un set di record CNAME può contenere al massimo un record, la rimozione di tale record lascerà un set di record vuoto.

	$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record MX da un set di record esistente

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record NS da un set di record esistente

	$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record SRV da un set di record esistente

	$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Rimuovere un record TXT da un set di record esistente

	$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs

## Eliminare un set di record

È possibile eliminare i set di record usando il cmdlet `Remove-AzureRmDnsRecordSet`. Non è possibile eliminare i set di record SOA ed NS al vertice della zona (name = "@") che sono stati creati automaticamente quando è stata creata la zona. Verranno eliminati automaticamente quando si elimina la zona.

Usare uno dei tre metodi seguenti per rimuovere un set di record:

### Specificare tutti i parametri per nome

L'opzione *-Force* facoltativa può essere usata per eliminare la richiesta di conferma.

	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### Specificare il set di record per nome e tipo e specificare la zona in base all'oggetto

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Specificare il set di record in base all'oggetto

	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Quando si specifica il set di record usando un oggetto, si abilitano i controlli etag per garantire che le modifiche simultanee non vengano eliminate. Il flag facoltativo *-Overwrite* disattiva questi controlli. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#tagetag).

L'oggetto del set di record può essere anche reindirizzato invece che passato come parametro:

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## Passaggi successivi

Per altre informazioni sul servizio DNS di Azure, vedere [Panoramica di DNS di Azure](dns-overview.md). Per informazioni sull'automazione di DNS, vedere [Creare zone e set di record DNS con .NET SDK](dns-sdk.md).

Per altre informazioni sui record DNS inversi, vedere [Come gestire i record DNS inversi per i servizi tramite PowerShell](dns-reverse-dns-record-operations-ps.md).

<!---HONumber=AcomDC_0824_2016-->