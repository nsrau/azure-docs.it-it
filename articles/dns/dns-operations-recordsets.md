---
title: Gestire set di record e record DNS con il portale di Azure | Microsoft Docs
description: Gestione dei set di record e dei record DNS in DNS di Azure quando si ospita il dominio in DNS di Azure. Tutti i comandi di PowerShell per le operazioni sui set di record e i record.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: e3f7967843b3d9e38c79b45d90e333192fd3a900

---

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Gestire record e set di record DNS con PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-operations-recordsets-portal.md)
> * [Interfaccia della riga di comando di Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Questo articolo descrive come gestire i set di record e i record per la zona DNS usando Windows PowerShell.

È importante comprendere la differenza tra i set di record DNS e i singoli record DNS. Un set di record è la raccolta di record con lo stesso nome e lo stesso tipo in una zona. Per altre informazioni vedere [Creare set di record e record DNS mediante il portale di Azure](dns-getstarted-create-recordset-portal.md).

Per gestire i record e i set di record, è necessario installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Per altre informazioni sull'uso di PowerShell, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

## <a name="create-a-new-record-set-and-a-record"></a>Creare un nuovo set di record e un record

Per creare un set di record con PowerShell, vedere [Creare record e set di record DNS con PowerShell](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Ottenere un set di record

Per recuperare un set di record esistente, usare `Get-AzureRmDnsRecordSet`. Specificare il nome relativo del set di record, il tipo di record e la zona.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

Come per `New-AzureRmDnsRecordSet`, il nome del record deve essere un nome relativo, ovvero deve escludere il nome della zona.

È possibile specificare la zona usando il nome della zona e il nome del gruppo di risorse oppure un oggetto di zona:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

`Get-AzureRmDnsRecordSet` restituisce un oggetto locale che rappresenta il set di record creato nel servizio DNS di Azure.

## <a name="list-record-sets"></a>Elencare i set di record

Per elencare i set di record, è anche possibile usare `Get-AzureRmDnsRecordSet`, omettendo i parametri *-Name* e/o *-RecordType*.

### <a name="to-list-all-record-sets"></a>Per elencare tutti i set di record

Questo esempio restituisce tutti i set di record, indipendentemente dal nome o dal tipo di record:

```powershell
$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="to-list-record-sets-of-a-given-record-type"></a>Per elencare i set di record di un determinato tipo

Questo esempio restituisce tutti i set di record corrispondenti al tipo di record specificato. In questo caso, il set di record restituito è record "A":

```powershell
$list = Get-AzureRmDnsRecordSet -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

È possibile specificare la zona usando i parametri `-ZoneName` e `-ResourceGroupName`, come illustrato, oppure specificando un oggetto zona:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$list = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-a-record-set"></a>Aggiungere un record a un set di record

Aggiungere record al set di record usando il cmdlet `Add-AzureRmDnsRecordConfig` . Si tratta di un'operazione offline. Solo l'oggetto locale che rappresenta il set di record viene modificato.

I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record. Ad esempio, quando si usa un set di record di tipo "A" sarà possibile specificare solo i record con il parametro *-IPv4Address*.

Le chiamate aggiuntive a `Add-AzureRmDnsRecordConfig`possono aggiungere altri record a ogni set di record. È possibile aggiungere fino a 20 record a qualsiasi set di record. Tuttavia, i set di record di tipo "CNAME" possono contenere al massimo 1 record e un set di record non può contenere due record identici. È possibile creare set di record vuoti, con zero record, che non vengono però visualizzati nei server dei nomi DNS di Azure.

Una volta che il set di record contiene la raccolta di record desiderata, è necessario eseguirne il commit usando il cmdlet `Set-AzureRmDnsRecordSet` . Dopo che è stato eseguito il commit di un set di record, questo sostituisce il set di record esistente nel servizio DNS di Azure.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Per creare un set di record A con un singolo record

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

La sequenza delle operazioni per creare un record può anche essere *reindirizzata*, ovvero l'oggetto del set di record viene passato usando il pipe anziché come parametro. Ad esempio:

```powershell
New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="additional-record-type-examples"></a>Altri esempi di tipi di record

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modificare i set di record esistenti

I passaggi per la modifica di un set di record esistente sono simili ai passaggi per la creazione di record. La sequenza delle operazioni è la seguente:

1. Recuperare il set di record esistente usando `Get-AzureRmDnsRecordSet`.
2. Modificare il set di record con l'aggiunta di record, la rimozione dei record, la modifica dei parametri di record o la modifica della durata (TTL) del set di record. Si tratta di un'operazione offline. Solo l'oggetto locale che rappresenta il set di record viene modificato.
3. Confermare le modifiche usando il cmdlet `Set-AzureRmDnsRecordSet` . Sostituisce il set di record esistente nel servizio DNS di Azure.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Per aggiornare un record in un set di record esistente

In questo esempio modificheremo l'indirizzo IP di un record "A" esistente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Ipv4Address = "134.170.185.46"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

Il cmdlet `Set-AzureRmDnsRecordSet` usa i controlli etag per verificare che le modifiche simultanee non vengano sovrascritte. Usare il flag *-Overwrite* per disattivare questi controlli. Per altre informazioni, vedere [Etag e Tag](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Per modificare un record SOA

Non è possibile aggiungere o rimuovere i record dal set di record SOA creato automaticamente al vertice della zona (name = "@").), ma è possibile modificare i parametri all'interno del record SOA, ad eccezione di "Host", e la durata (TTL) del record.

L'esempio seguente mostra come modificare la proprietà *Email* del record SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Per modificare i record NS al vertice della zona

Non è possibile aggiungere, rimuovere o modificare i record nel set di record NS creato automaticamente al vertice della zona (name = "@").). L'unica modifica consentita consiste nel modificare la durata (TTL) del set di record.

L'esempio seguente illustra come modificare la proprietà della durata (TTL) del set di record NS:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Ttl = 300
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-add-records-to-an-existing-record-set"></a>Per aggiungere record a un set di record esistente

In questo esempio verranno aggiunti due ulteriori record MX al set di record esistente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="remove-a-record-from-an-existing-record-set"></a>Rimuovere un record da un set di record esistente

È possibile rimuovere i record da un set di record usando `Remove-AzureRmDnsRecordConfig`. Il record che viene rimosso deve corrispondere esattamente a un record esistente in tutti i parametri. È necessario eseguire il commit delle modifiche usando `Set-AzureRmDnsRecordSet`.

La rimozione dell'ultimo record da un set di record non elimina il set di record. Per altre informazioni, vedere [Eliminare un set di record](#delete-a-record-set) di seguito.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

La sequenza delle operazioni per rimuovere un record da un set di record può anche essere reindirizzata , ovvero l'oggetto del set di record viene passato usando il pipe anziché come parametro. Ad esempio:

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Rimuovere un record AAAA da un set di record esistente

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-cname-record-from-a-record-set"></a>Rimuovere un record CNAME da un set di record esistente

Dal momento che un set di record CNAME può contenere al massimo un record, la rimozione di tale record lascerà un set di record vuoto.

```powershell
$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-mx-record-from-a-record-set"></a>Rimuovere un record MX da un set di record esistente

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-ns-record-from-record-set"></a>Rimuovere un record NS da un set di record esistente

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-srv-record-from-a-record-set"></a>Rimuovere un record SRV da un set di record esistente

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-txt-record-from-a-record-set"></a>Rimuovere un record TXT da un set di record esistente

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="delete-a-record-set"></a>Eliminare un set di record

È possibile eliminare i set di record usando il cmdlet `Remove-AzureRmDnsRecordSet` . Non è possibile eliminare i set di record SOA ed NS al vertice della zona (name = "@")) che sono stati creati automaticamente quando è stata creata la zona. Verranno eliminati automaticamente quando si elimina la zona.

Usare uno dei tre metodi seguenti per rimuovere un set di record:

### <a name="specify-all-the-parameters-by-name"></a>Specificare tutti i parametri per nome

L'opzione *-Force* facoltativa può essere usata per eliminare la richiesta di conferma.

```powershell
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Specificare il set di record per nome e tipo e specificare la zona in base all'oggetto

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]
```

### <a name="specify-the-record-set-by-object"></a>Specificare il set di record in base all'oggetto

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -RecordSet $rs [-Overwrite] [-Force]
```

Quando si specifica il set di record usando un oggetto, si abilitano i controlli etag per garantire che le modifiche simultanee non vengano eliminate. Il flag facoltativo *-Overwrite* disattiva questi controlli. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#tagetag) .

L'oggetto del set di record può essere anche reindirizzato invece che passato come parametro:

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio DNS di Azure, vedere [Panoramica di DNS di Azure](dns-overview.md). Per informazioni sull'automazione di DNS, vedere [Creare zone e set di record DNS con .NET SDK](dns-sdk.md).

Per altre informazioni sui record DNS inversi, vedere [Come gestire i record DNS inversi per i servizi tramite PowerShell](dns-reverse-dns-record-operations-ps.md).



<!--HONumber=Nov16_HO3-->


