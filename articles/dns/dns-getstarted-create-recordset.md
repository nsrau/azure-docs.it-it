---
title: Creare record e set di record per una zona DNS con PowerShell | Documentazione Microsoft
description: Come creare record host per il servizio DNS di Azure. Impostare record e set di record DNS con PowerShell
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 67b780d66eac4199b0a2367f575477191542cfa7
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Creare record e set di record DNS con PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-getstarted-create-recordset-cli.md)

Questo articolo illustra come creare record e set di record con Azure PowerShell.

## <a name="introduction"></a>Introduzione

Prima di creare record DNS nel servizio DNS di Azure, è necessario comprendere il modo in cui quest'ultimo organizza i record DNS nei set di record DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Per altre informazioni sui record DNS nel servizio DNS di Azure, vedere [Zone e record DNS](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Creare un set di record e un record

In questa sezione viene descritto come creare record DNS in DNS di Azure. Gli esempi presuppongono che l'utente abbia [installato Azure PowerShell, eseguito l'accesso e creato una zona DNS](dns-getstarted-create-dnszone.md).

Gli esempi in questa pagina usano tutti il tipo di record DNS "A". Per altri tipi di record e maggiori informazioni su come gestire i record DNS e i set di record, vedere [Gestire record e set di record DNS con PowerShell](dns-operations-recordsets.md).

Se il nuovo record ha lo stesso nome e tipo di un record esistente, è necessario [aggiungerlo al set di record esistente](#add-a-record-to-an-existing-record-set). Se il nuovo record ha un nome e un tipo diversi rispetto a tutti i record esistenti, è necessario [creare un nuovo set di record](#create-records-in-a-new-record-set). 

### <a name="create-records-in-a-new-record-set"></a>Creare record in un nuovo set di record

I set di record vengono creati usando il cmdlet `New-AzureRmDnsRecordSet`. Quando si crea un set di record, è necessario specificare il nome, la zona, la durata (TTL), il tipo di record e i record da creare.

Per creare un set di record nell'apice della zona, in questo caso "contoso.com", usare il nome record "@", incluse le virgolette. Si tratta di una convenzione comune di DNS.

L'esempio seguente mostra come creare un nuovo set di record con il nome relativo "www" nella zona DNS "contoso.com". Il nome completo del set di record sarà "www.contoso.com". Il tipo di record è "A" e la durata (TTL) è 3600 secondi. Il set di record contiene un record singolo con indirizzo IP "1.2.3.4".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Se è necessario creare un nuovo set di record contenente più di un record, creare prima una matrice locale contenente i record da aggiungere.  Questa viene passata a `New-AzureRmDnsRecordSet`, come illustrato di seguito:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>Aggiungere un record a un set di record esistente

Per aggiungere un record a un set di record esistente, seguire questa procedura in tre passaggi:

1. Ottenere il set di record esistente.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Aggiungere il nuovo record al set di record locale. Si tratta di un'operazione offline.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Eseguire il commit della modifica nel servizio DNS di Azure. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>Verificare la risoluzione dei nomi

È possibile verificare che i record DNS siano presenti sui server di nomi DNS di Azure usando gli strumenti DNS come nslookup, DIG o il [cmdlet di PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario [indirizzare la query DNS direttamente a uno dei server dei nomi per la zona](dns-getstarted-create-dnszone.md#test-name-servers). Assicurarsi di sostituire i valori corretti per la propria zona di record nell'esempio seguente:

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [delegare il nome di dominio ai server dei nomi DNS di Azure](dns-domain-delegation.md)

Informazioni su come [gestire le zone DNS usando PowerShell](dns-operations-dnszones.md).

Informazioni su come [gestire i record e i set di record DNS usando PowerShell](dns-operations-recordsets.md).



