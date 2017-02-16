---
title: Gestire i record DNS in DNS di Azure tramite Azure PowerShell | Documentazione Microsoft
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f8dcbdb573fb50d32dfdefbb52c7af71bdc1cb95
ms.openlocfilehash: 2264acb9c78a162adb7b9937568838ab5ec2c720

---

# <a name="manage-dns-records-in-azure-dns-using-azure-powershell"></a>Gestire i record DNS in DNS di Azure tramite Azure PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-operations-recordsets-portal.md)
> * [Interfaccia della riga di comando di Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Questo articolo descrive come gestire i record DNS per la zona DNS usando Azure PowerShell. Per gestire i record DNS è anche possibile usare l'[interfaccia della riga di comando multipiattaforma di Azure](dns-operations-recordsets-cli.md) o il [portale di Azure](dns-operations-recordsets-portal.md).

Gli esempi contenuti in questo articolo presuppongono che l'utente abbia [installato Azure PowerShell, eseguito l'accesso e creato una zona DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Introduzione

Prima di creare record DNS nel servizio DNS di Azure, è necessario comprendere il modo in cui quest'ultimo organizza i record DNS nei set di record DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Per altre informazioni sui record DNS nel servizio DNS di Azure, vedere [Zone e record DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Creare un nuovo record DNS

Se il nuovo record ha lo stesso nome e tipo di un record esistente, è necessario [aggiungerlo al set di record esistente](#add-a-record-to-an-existing-record-set). Se il nuovo record ha un nome e un tipo diversi rispetto a tutti i record esistenti, è necessario creare un nuovo set di record. 

### <a name="create-a-records-in-a-new-record-set"></a>Creare record di tipo A in un nuovo set di record

I set di record vengono creati usando il cmdlet `New-AzureRmDnsRecordSet`. Quando si crea un set di record, è necessario specificare il nome, la zona, la durata (TTL), il tipo di record e i record da creare.

I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record. Quando ad esempio si usa un set di record di tipo A, è necessario specificare l'indirizzo IP usando il parametro `-IPv4Address`. Per altri tipi di record vengono usati altri parametri. Per altre informazioni, vedere la sezione [Altri esempi di tipi di record](#additional-record-type-examples).

L'esempio seguente illustra la creazione di un set di record con il nome relativo "www" nella zona DNS "contoso.com". Il nome completo del set di record sarà "www.contoso.com". Il tipo di record è "A" e la durata (TTL) è 3600 secondi. Il set di record contiene un record singolo con indirizzo IP "1.2.3.4".

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address 1.2.3.4) 
```

Per creare un set di record nel dominio radice di una zona, in questo caso "contoso.com", usare il nome del set di record "@", incluse le virgolette:

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address 1.2.3.4) 
```

Se è necessario creare un nuovo set di record contenente più record, creare prima una matrice locale e aggiungervi i record, quindi passare la matrice a `New-AzureRmDnsRecordSet`, come indicato di seguito:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address 1.2.3.4
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address 2.3.4.5
New-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

È possibile usare i [metadati del set di record](dns-zones-records.md#tags-and-metadata) per associare dati specifici dell'applicazione a ogni set di record, sotto forma di coppie chiave-valore. L'esempio seguente mostra come creare un set di record con due voci di metadati, dept="finance" ed environment="production".

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address 1.2.3.4) -Metadata @{ dept="finance"; environment="production" } 
```

Il servizio DNS di Azure supporta anche set di record vuoti, che possono fungere da segnaposto per riservare un nome DNS prima della creazione di record DNS. I set di record vuoti sono visibili nel piano di controllo del servizio DNS di Azure, ma vengono visualizzati nei server dei nomi del servizio DNS di Azure. L'esempio seguente mostra come creare un set di record vuoto:

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Creare record di altri tipi

Dopo aver visto in dettaglio come creare record di tipo A, gli esempi seguenti illustrano come creare record di altri tipi supportati dal servizio DNS di Azure.

Per ogni caso viene illustrato come creare un set di record contenente un record singolo. Gli esempi precedenti relativi ai record di tipo A possono essere adattati per creare set di record di altri tipi che contengono più record, con metadati, o per creare set di record vuoti.

Non vengono forniti esempi per la creazione di set di record SOA, dato che vengono creati ed eliminati con ogni zona DNS e non è possibile crearli o eliminarli separatamente. Tuttavia, [è possibile modificare i record SOA, come illustrato in uno degli esempi successivi](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Creare un set di record AAAA con un singolo record

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address 2607:f8b0:4009:1803::1005) 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Creare un set di record CNAME con un singolo record

> [!NOTE]
> Gli standard DNS non accettano record CNAME nel dominio radice di una zona (`-Name "@"`) né set di record contenenti più di un record.
> 
> Per altre informazioni, vedere[Record CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name test-cname -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname www.contoso.com) 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Creare un set di record MX con un singolo record

In questo esempio viene usato il nome del set di record "@" per creare un record MX al vertice della zona, in questo caso "contoso.com".


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange mail.contoso.com -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Creare un set di record NS con un singolo record

```powershell
New-AzureRmDnsRecordSet -Name test-ns -RecordType NS -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname ns1.contoso.com) 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Creare un set di record PTR con un singolo record

In questo caso "my-arpa-zone.com" è la zona ARPA che rappresenta l'intervallo IP dell'utente. Ogni record PTR impostato in questa zona corrisponde a un indirizzo IP che rientra nell'intervallo IP. Il nome del record "10" è l'ultimo ottetto dell'indirizzo IP all'interno di questo intervallo di indirizzi rappresentato dal record.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName my-arpa-zone.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname myservice.contoso.com) 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Creare un set di record SRV con un singolo record

Quando si crea un [set di record SRV](dns-zones-records.md#srv-records), specificare il *\_servizio* e il *\_protocollo* nel nome del set di record. Non è necessario includere "@" nel nome del set di record durante la creazione di un set di record SRV nel dominio radice della zona.

```powershell
New-AzureRmDnsRecordSet -Name _sip._tls -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target sip.contoso.com) 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Creare un set di record TXT con un singolo record

L'esempio seguente mostra come creare un record TXT. Per altre informazioni sulla lunghezza massima delle stringhe supportata nei record TXT, vedere [Record TXT](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name test-txt -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Ottenere un set di record

Per recuperare un set di record esistente, usare `Get-AzureRmDnsRecordSet`. Questo cmdlet restituisce un oggetto locale che rappresenta il set di record nel servizio DNS di Azure.

Come per `New-AzureRmDnsRecordSet`, il nome assegnato al set di record deve essere un nome *relativo*, ovvero deve escludere il nome della zona. È anche necessario specificare il tipo di record e la zona contenente il set di record.

L'esempio seguente mostra come recuperare un set di record. In questo esempio la zona viene specificata con i parametri `-ZoneName` e `-ResourceGroupName`.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

In alternativa è possibile specificare la zona usando un oggetto zona, passato usando il parametro "-Zone". 

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Elencare i set di record

È possibile usare `Get-AzureRmDnsZone` per elencare i set di record in una zona, omettendo i parametri `-Name` e/o `-RecordType`.

L'esempio seguente restituisce tutti i set di record nella zona:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

L'esempio seguente illustra come recuperare tutti i set di record di un determinato tipo, specificando il tipo di record e omettendo il nome del set di record:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

Per recuperare tutti i set di record con un nome specifico, indipendentemente dai tipi di record, è necessario recuperare tutti i set di record e quindi filtrare i risultati:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup | where {$_.Name.Equals("www")}
```

In tutti gli esempi precedenti è possibile specificare la zona usando i parametri `-ZoneName` e `-ResourceGroupName`, come illustrato, oppure specificando un oggetto zona:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Aggiungere un record a un set di record esistente

Per aggiungere un record a un set di record esistente, seguire questa procedura in tre passaggi:

1. Ottenere il set di record esistente.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -RecordType A
    ```

2. Aggiungere il nuovo record al set di record locale. Si tratta di un'operazione offline.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address"5.6.7.8
    ```

3. Eseguire il commit della modifica nel servizio DNS di Azure. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

`Set-AzureRmDnsRecordSet` *sostituisce* il set di record esistente nel servizio DNS di Azure, e tutti i record in esso contenuti, con il set di record specificato. I [controlli ETag](dns-zones-records.md#etags) vengono usati per fare in modo che le modifiche simultanee non vengano sovrascritte. È possibile usare l'opzione facoltativa `-Overwrite` per disattivare tali controlli.

Questa sequenza di operazioni può anche essere *inviata tramite pipe*, vale a dire che l'oggetto set di record può essere passato usando la pipe anziché come parametro:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address 5.6.7.8 | Set-AzureRmDnsRecordSet
```

Gli esempi precedenti mostrano come aggiungere un record "A" a un set di record esistente di tipo A. Una sequenza di operazioni simile permette di aggiungere record a set di record di altri tipi, sostituendo il parametro `-Ipv4Address` di `Add-AzureRmDnsRecordConfig` con altri parametri specifici per ogni tipo di record. I parametri per ogni tipo di record sono gli stessi usati per il cmdlet `New-AzureRmDnsRecordConfig`, come illustrato nella sezione [Altri esempi di tipi di record](#additional-record-type-examples).

I set di record di tipo "CNAME" o "SOA" non possono contenere più di un record. Questo vincolo è dato dagli standard DNS, non è una limitazione del servizio DNS di Azure.

## <a name="remove-a-record-from-an-existing-record-set"></a>Rimuovere un record da un set di record esistente

Il processo di rimozione di un record da un set di record è simile a quello di aggiunta di un record a un set di record esistente:

1. Ottenere il set di record esistente.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -RecordType A
    ```

2. Rimuovere il record dall'oggetto set di record locale. Si tratta di un'operazione offline. Il record che viene rimosso deve corrispondere esattamente a un record esistente in tutti i parametri.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 5.6.7.8
    ```

3. Eseguire il commit della modifica nel servizio DNS di Azure. Usare l'opzione facoltativa `-Overwrite` per disattivare i [controlli ETag](dns-zones-records.md#etags) per le modifiche simultanee.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

Se si usa la sequenza di operazioni precedente per rimuovere l'ultimo record da un set di record, il set di record non viene eliminato, ma rimane un set di record vuoto. Per rimuovere del tutto un set di record, vedere [Eliminare un set di record](#delete-a-record-set).

Come per l'aggiunta di record a un set di record, anche la sequenza di operazioni per rimuovere un set di record può essere inviata tramite pipe:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address 5.6.7.8 | Set-AzureRmDnsRecordSet
```

Perché vengano supportati tipi di record diversi, occorre passare i parametri specifici del tipo appropriati a `Remove-AzureRmDnsRecordSet`. I parametri per ogni tipo di record sono gli stessi usati per il cmdlet `New-AzureRmDnsRecordConfig`, come illustrato nella sezione [Altri esempi di tipi di record](#additional-record-type-examples).


## <a name="modify-an-existing-record-set"></a>Modificare un set di record esistente

La procedura per la modifica di un set di record esistente è simile a quella per l'aggiunta o la rimozione di record da un set di record:

1. Recuperare il set di record esistente usando `Get-AzureRmDnsRecordSet`.
2. Modificare l'oggetto set di record locale. A tale scopo:
    * Aggiungere o rimuovere record.
    * Modificare i parametri di record esistenti.
    * Modificare i metadati e la durata (TTL) del set di record.
3. Confermare le modifiche usando il cmdlet `Set-AzureRmDnsRecordSet` . In questo modo si *sostituisce* il set di record esistente nel servizio DNS di Azure con il set di record specificato.

Quando si usa `Set-AzureRmDnsRecordSet`, i [controlli ETag](dns-zones-records.md#etags) vengono usati per fare in modo che le modifiche simultanee non vengano sovrascritte. È possibile usare l'opzione facoltativa `-Overwrite` per disattivare tali controlli.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Per aggiornare un record in un set di record esistente

In questo esempio modificheremo l'indirizzo IP di un record "A" esistente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
$rs.Records[0].Ipv4Address = 9.8.7.6
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Per modificare un record SOA

Non è possibile aggiungere o rimuovere record dal set di record SOA creato automaticamente nel dominio radice della zona, vale a dire `-Name "@"`, incluse le virgolette. È tuttavia possibile modificare i parametri all'interno del record SOA, ad eccezione di "Host", e la durata (TTL) del set di record.

L'esempio seguente mostra come modificare la proprietà *Email* del record SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyResourceGroup
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Per modificare i record NS al vertice della zona

Non è possibile aggiungere, rimuovere o modificare record nel set di record NS creato automaticamente nel dominio radice della zona, vale a dire `-Name "@"`, incluse le virgolette. Le uniche modifiche consentite sono quelle relative alla durata (TTL) e ai metadati del set di record.

L'esempio seguente illustra come modificare la proprietà della durata (TTL) del set di record NS:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyResourceGroup
$rs.Ttl = 300
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Per modificare i metadati del set di record

È possibile usare i [metadati del set di record](dns-zones-records.md#tags-and-metadata) per associare dati specifici dell'applicazione a ogni set di record, sotto forma di coppie chiave-valore.

L'esempio seguente mostra come modificare i metadati di un set di record esistente:

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup

# Add "dept=finance" name-value pair
$rs.Metadata.Add("dept", "finance") 

# Remove metadata item named "environment"
$rs.Metadata.Remove("environment")  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Eliminare un set di record

È possibile eliminare i set di record usando il cmdlet `Remove-AzureRmDnsRecordSet` . Eliminando un set di record vengono eliminati anche tutti i record in esso contenuti.

> [!NOTE]
> Non è possibile eliminare i set di record SOA e NS dal dominio radice della zona (`-Name "@"`).  Tali set di record vengono creati automaticamente durante la creazione della zona e vengono eliminati automaticamente quando la zona viene eliminata.

L'esempio seguente mostra come eliminare un set di record. In questo esempio il nome del set di record, il tipo di set di record, il nome della zona e il gruppo di risorse vengono specificati singolarmente in modo esplicito.

```powershell
Remove-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

In alternativa è possibile specificare il set di record in base al nome e al tipo e specificare la zona con un oggetto:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

Una terza soluzione consiste nello specificare lo stesso set di record usando un oggetto set di record:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

Quando si specifica il set di record da eliminare usando un oggetto set di record, vengono usati i [controlli ETag](dns-zones-records.md#etags) per fare in modo che le modifiche simultanee non vengano eliminate. È possibile usare l'opzione facoltativa `-Overwrite` per disattivare tali controlli.

L'oggetto del set di record può essere anche reindirizzato invece che passato come parametro:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Richieste di conferma

I cmdlet `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet` e `Remove-AzureRmDnsRecordSet` supportano le richieste di conferma.

Ogni cmdlet richiede una conferma se la variabile di preferenza PowerShell `$ConfirmPreference` ha un valore minore o uguale a `Medium`. Dato che il valore predefinito per `$ConfirmPreference` è `High`, non vengono restituite richieste se si usano le impostazioni predefinite di PowerShell.

Per eseguire l'override dell'impostazione `$ConfirmPreference` corrente è possibile usare il parametro `-Confirm`. Se si specifica `-Confirm` o `-Confirm:$True`, il cmdlet chiede conferma prima dell'esecuzione. Se si specifica `-Confirm:$False`, il cmdlet non chiede alcuna conferma. 

Per altre informazioni su `-Confirm` e `$ConfirmPreference`, vedere [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) (Informazioni sulle variabili di preferenza).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [zone e record nel servizio DNS di Azure](dns-zones-records.md).
<br>
Informazioni su come [proteggere zone e record](dns-protect-zones-recordsets.md) quando si usa il servizio DNS di Azure.
<br>
Vedere la [documentazione di riferimento di PowerShell nel servizio DNS di Azure](/powershell/resourcemanager/azurerm.dns/v2.3.0/azurerm.dns).



<!--HONumber=Jan17_HO2-->


