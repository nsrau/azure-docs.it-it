---
title: Creare record e set di record per una zona DNS con PowerShell | Documentazione Microsoft
description: Come creare i record host per DNS di Azure. Configurare i set di record e i record usando PowerShell
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
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fe82db80f0efc02809bbd898e990860c7bcce8da

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Creare record e set di record DNS con PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Interfaccia della riga di comando di Azure](dns-getstarted-create-recordset-cli.md)

Questo articolo illustra la creazione di record e set di record con Windows PowerShell. Dopo aver creato la zona DNS, è necessario aggiungere i record DNS per il dominio. A tale scopo, è necessario comprendere i record e i set di record DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>Verificare che sia disponibile l'ultima versione di PowerShell

Verificare di aver installato la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) .

## <a name="create-a-record-set-and-record"></a>Creare un set di record e un record

Questa sezione mostra come creare un record e un set di record.

### <a name="1-connect-to-your-subscription"></a>1. Eseguire la connessione alla sottoscrizione

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

```powershell
Login-AzureRmAccount
```

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Specificare la sottoscrizione da usare.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

Per altre informazioni sull'uso di PowerShell, vedere [Uso di Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

### <a name="2-create-a-record-set"></a>2. Creare un set di record

I set di record vengono creati usando il cmdlet `New-AzureRmDnsRecordSet`. Quando si crea un set di record, è necessario specificare il nome, la zona, il Time-to-Live (TTL) e il tipo di record.

Per creare un set di record nel dominio radice, in questo caso "contoso.com", usare il nome record "@",, incluse le virgolette. Si tratta di una convenzione comune di DNS.

L'esempio seguente illustra la creazione di un set di record con il nome relativo "www" nella zona DNS "contoso.com". Il nome completo del set di record sarà "www.contoso.com". Il tipo di record è "A" e il valore TTL è 60 secondi. Con il completamento di questo passaggio si avrà un set di record "www" vuoto assegnato alla variabile *$rs*.

```powershell
$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60
```

#### <a name="if-a-record-set-already-exists"></a>Se un set di record esiste già

Se un set di record esiste già, il comando non riuscirà a meno che non venga usata l'opzione *-Overwrite* . L'opzione *-Overwrite* attiverà una richiesta di conferma, che può essere eliminata usando l'opzione *-Force*.

```powershell
$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]
```

In questo esempio specificare la zona usando il nome della zona e del gruppo di risorse. In alternativa, è possibile specificare un oggetto di zona, come restituito da `Get-AzureRmDnsZone` o `New-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -Zone $zone [-Tag $tags] [-Overwrite] [-Force]
```

`New-AzureRmDnsRecordSet` restituisce un oggetto locale che rappresenta il set di record creato nel servizio DNS di Azure.

### <a name="3-add-a-record"></a>3. Aggiungere un record

Per usare il set di record "www" appena creato è necessario aggiungervi record. È possibile aggiungere record IPv4 *A* al set di record "www" usando l'esempio seguente. Questo esempio si basa sulla variabile *$rs* impostata nel passaggio precedente.

L'aggiunta di record a un set di record usando `Add-AzureRmDnsRecordConfig` è un'operazione offline. Solo la variabile locale *$rs* viene aggiornata.

```powershell
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221
```

### <a name="4-commit-the-changes"></a>4. Eseguire il commit delle modifiche

Eseguire il commit delle modifiche al set di record. Usare `Set-AzureRmDnsRecordSet` per caricare le modifiche al set di record in DNS di Azure.

```powershell
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="5-retrieve-the-record-set"></a>5. Recuperare il set di record

È possibile recuperare il set di record dal servizio DNS di Azure usando `Get-AzureRmDnsRecordSet`, come illustrato nell'esempio seguente.

    Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


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

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>Per creare un set di record di ogni tipo con un singolo record

Gli esempi seguenti illustrano come creare un set di record di ogni tipo di record. Ogni set di record contiene un singolo record.

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="next-steps"></a>Passaggi successivi

[Come gestire le zone DNS usando PowerShell](dns-operations-dnszones.md)

[Gestire record e set di record DNS con PowerShell](dns-operations-recordsets.md)

[Automatizzare le operazioni di Azure con .NET SDK](dns-sdk.md)



<!--HONumber=Dec16_HO2-->


