---
title: Gestire le zone DNS in DNS di Azure - PowerShell | Documentazione Microsoft
description: "È possibile gestire le zone DNS usando PowerShell di Azure. Questo articolo illustra come aggiornare, eliminare e creare le zone DNS in DNS di Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: gwallace
ms.openlocfilehash: 3f28e70bb6ef46f53375d256a520db40fcb71ad0
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Come gestire le zone DNS utilizzando PowerShell

> [!div class="op_single_selector"]
> * [di Microsoft Azure](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-operations-dnszones-cli.md)

Questo articolo spiega come gestire una zona DNS mediante Azure PowerShell. È anche possibile gestire le zone DNS usando l'[interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md) multipiattaforma o il portale di Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Creare una zona DNS

Viene creata una zona DNS con il cmdlet `New-AzureRmDnsZone` .

L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

L'esempio seguente illustra come creare una zona DNS con due [tag di Azure Resource Manager](dns-zones-records.md#tags), *project = demo* e *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

DNS di Azure supporta ora anche le zone DNS private (attualmente una funzionalità di anteprima).  Per un esempio di come creare una zona DNS privata, vedere [Introduzione alle zone private di Azure DNS con PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Ottenere una zona DNS

Per recuperare una zona DNS, usare il cmdlet `Get-AzureRmDnsZone` . Questa operazione restituisce un oggetto di zona DNS corrispondente a una zona esistente nel servizio DNS di Azure. L'oggetto contiene i dati sulla zona (ad esempio il numero di set di record), ma non contiene i set di record stessi (vedere `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Elencare le zone DNS

Omettendo il nome della zona da `Get-AzureRmDnsZone`, è possibile enumerare tutte le zone in un gruppo di risorse. Questa operazione restituisce una matrice di oggetti di zona.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Omettendo il nome della zona e il nome del gruppo di risorse da `Get-AzureRmDnsZone`, è possibile enumerare tutte le zone nella sottoscrizione di Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Aggiornare una zona DNS

È possibile apportare modifiche a una risorsa di zona DNS usando `Set-AzureRmDnsZone`. Il cmdlet non consente di aggiornare alcuni dei set di record DNS compresi nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Le proprietà della zona scrivibile sono attualmente limitate ai ["tag" di Azure Resource Manager relativi alla risorsa di zona](dns-zones-records.md#tags).

Usare una delle due opzioni seguenti per aggiornare una zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Specificare la zona usando il nome della zona e del gruppo di risorse.

Questo approccio sostituisce i tag di zona esistenti con i valori specificati.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Specificare la zona usando un oggetto $zone

Questo approccio recupera l'oggetto zona esistente, modifica i tag e quindi esegue il commit delle modifiche. Ciò consente di conservare i tag esistenti.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Quando si usa `Set-AzureRmDnsZone` con un oggetto $zone, i [controlli ETag](dns-zones-records.md#etags) vengono usati per verificare che le modifiche simultanee non vengano sovrascritte. È possibile usare l'opzione facoltativa `-Overwrite` per disattivare tali controlli.

## <a name="delete-a-dns-zone"></a>Eliminare una zona DNS

Le zone DNS possono essere eliminate usando il cmdlet `Remove-AzureRmDnsZone`.

> [!NOTE]
> L'eliminazione di una zona DNS comporta anche l'eliminazione di tutti i record DNS all'interno della zona. Questa operazione non può essere annullata. Se la zona DNS è in uso, i servizi che la usano rileveranno un errore quando la zona viene eliminata.
>
>Per evitare l'eliminazione accidentale di una zona, vedere [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Come proteggere le zone e i record DNS).


Usare una delle due opzioni seguenti per eliminare la zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Specificare la zona usando il nome della zona e del gruppo di risorse:

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Specificare la zona usando un oggetto $zone

È possibile specificare la zona da eliminare usando un oggetto `$zone` restituito da `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

L'oggetto di zona può essere anche reindirizzato invece che passato come parametro:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Come con `Set-AzureRmDnsZone`, specificare la zona usando un oggetto `$zone` consente di abilitare i controlli ETag per garantire che le modifiche simultanee non vengano eliminate. Usare l'opzione `-Overwrite` per disattivare tali controlli.

## <a name="confirmation-prompts"></a>Richieste di conferma

I cmdlet `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` e `Remove-AzureRmDnsZone` supportano le richieste di conferma.

`New-AzureRmDnsZone` e `Set-AzureRmDnsZone` richiedono la conferma se la variabile di preferenza `$ConfirmPreference` di PowerShell ha un valore uguale o inferiore a `Medium`. A causa dell'impatto potenzialmente significativo dell'eliminazione di una zona DNS, il cmdlet `Remove-AzureRmDnsZone` richiede la conferma se la variabile `$ConfirmPreference` di PowerShell ha un valore diverso da `None`.

Poiché il valore predefinito per `$ConfirmPreference` è `High`, solo `Remove-AzureRmDnsZone` richiede la conferma per impostazione predefinita.

Per eseguire l'override dell'impostazione `$ConfirmPreference` corrente è possibile usare il parametro `-Confirm`. Se si specifica `-Confirm` o `-Confirm:$True`, il cmdlet chiede conferma prima dell'esecuzione. Se si specifica `-Confirm:$False`, il cmdlet non chiede alcuna conferma.

Per altre informazioni su `-Confirm` e `$ConfirmPreference`, vedere [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) (Informazioni sulle variabili di preferenza).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [gestire record e set di record](dns-operations-recordsets.md) nella zona DNS.
<br>
Informazioni su come [delegare il dominio al servizio DNS di Azure](dns-domain-delegation.md).
<br>
Vedere la [documentazione di riferimento di PowerShell nel servizio DNS di Azure](/powershell/module/azurerm.dns).

