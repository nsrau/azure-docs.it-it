---
title: Introduzione alle zone private di Azure DNS con PowerShell | Microsoft Docs
description: Informazioni su come creare una zona DNS privata e un record in Azure DNS. Questa guida dettagliata illustra come creare e gestire la prima zona DNS privata e il primo record usando PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Introduzione alle zone private di Azure DNS con PowerShell

Questo articolo illustra i passaggi per creare la prima zona DNS privata e il primo record con Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare una zona DNS privata nella rete virtuale, specificare l'elenco di reti virtuali autorizzate a risolvere i record nella zona.  Tali reti vengono dette "reti virtuali di risoluzione".  È anche possibile specificare una rete virtuale per cui Azure DNS manterrà i record dei nomi host ogni volta che una macchina virtuale viene creata o eliminata definitivamente oppure ne viene modificato l'indirizzo IP.  Tale rete viene definita "rete virtuale di registrazione".

# <a name="get-the-preview-powershell-modules"></a>Ottenere i moduli di PowerShell di anteprima
Queste istruzioni presuppongono che l'installazione e l'accesso di Azure PowerShell siano già stati completati e che sia stata verificata la disponibilità dei moduli necessari per la funzionalità della zona privata. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima di creare la zona DNS, viene creato un gruppo di risorse per contenere la zona DNS. L'esempio seguente mostra il comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Creare una zona DNS privata

Una zona DNS viene creata tramite l'uso del cmdlet `New-AzureRmDnsZone` insieme a un valore "Private" per il parametro ZoneType. L'esempio seguente crea una zona DNS denominata *contoso.local* nel gruppo di risorse denominato *MyResourceGroup* e rende la zona DNS disponibile per la rete virtuale denominata *MyAzureVnet*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

Si noti che se il parametro ZoneType viene omesso la zona verrà creata come zona pubblica, quindi questo parametro è necessario se occorre creare una zona privata. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Se è necessario che Azure crei automaticamente i record dei nomi host nella zona, usare il parametro *RegistrationVirtualNetworkId* invece di *ResolutionVirtualNetworkId*.  Le reti virtuali di registrazione sono abilitate automaticamente per la risoluzione.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>Creare un record DNS

I set di record vengono creati usando il cmdlet `New-AzureRmDnsRecordSet`. L'esempio seguente crea un record con il nome relativo "db" nella zona DNS "contoso.local" nel gruppo di risorse "MyResourceGroup". Il nome completo del set di record è "db.contoso.local". Il tipo di record è "A", con indirizzo IP "10.0.0.4", e la durata (TTL) è 3600 secondi.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Per altri tipi di record, per set di record con più di un record e per modificare i record esistenti, vedere [Gestire record e set di record DNS con Azure PowerShell](dns-operations-recordsets.md). 

## <a name="view-records"></a>Visualizzare i record

Per elencare i record DNS nella zona, usare:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>Elencare le zone DNS private

Omettendo il nome della zona da `Get-AzureRmDnsZone`, è possibile enumerare tutte le zone in un gruppo di risorse. Questa operazione restituisce una matrice di oggetti di zona.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Omettendo il nome della zona e il nome del gruppo di risorse da `Get-AzureRmDnsZone`, è possibile enumerare tutte le zone nella sottoscrizione di Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Aggiornare una zona DNS privata

È possibile apportare modifiche a una risorsa di zona DNS usando `Set-AzureRmDnsZone`. Il cmdlet non consente di aggiornare alcuni dei set di record DNS compresi nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Le proprietà scrivibili delle zone sono attualmente limitate ai ["tag" di Azure Resource Manager relativi alla risorsa di zona](dns-zones-records.md#tags) oltre ai parametri "RegistrationVirtualNetworkId" e "ResolutionVirtualNetworkId" per le zone private.

L'esempio seguente sostituisce la rete virtuale di registrazione collegata a una zona con la nuova rete virtuale MyNewAzureVnet.

Si noti che non è necessario specificare il parametro ZoneType per l'aggiornamento, a differenza della creazione. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

L'esempio seguente sostituisce la rete virtuale di risoluzione collegata a una zona con una nuova rete virtuale denominata "MyNewAzureVnet".

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Eliminare una zona DNS privata

Le zone DNS private possono essere eliminate usando il cmdlet `Remove-AzureRmDnsZone`, analogamente alle zone pubbliche.

> [!NOTE]
> L'eliminazione di una zona DNS comporta anche l'eliminazione di tutti i record DNS all'interno della zona. Questa operazione non può essere annullata. Se la zona DNS è in uso, i servizi che la usano rileveranno un errore quando la zona viene eliminata.
>
>Per evitare l'eliminazione accidentale di una zona, vedere [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Come proteggere le zone e i record DNS).

Usare una delle due opzioni seguenti per eliminare la zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Specificare la zona usando il nome della zona e del gruppo di risorse:

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Specificare la zona usando un oggetto $zone

È possibile specificare la zona da eliminare usando un oggetto `$zone` restituito da `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

L'oggetto di zona può essere anche reindirizzato invece che passato come parametro:

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Richieste di conferma

I cmdlet `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` e `Remove-AzureRmDnsZone` supportano le richieste di conferma.

`New-AzureRmDnsZone` e `Set-AzureRmDnsZone` richiedono la conferma se la variabile di preferenza `$ConfirmPreference` di PowerShell ha un valore uguale o inferiore a `Medium`. A causa dell'impatto potenzialmente significativo dell'eliminazione di una zona DNS, il cmdlet `Remove-AzureRmDnsZone` richiede la conferma se la variabile `$ConfirmPreference` di PowerShell ha un valore diverso da `None`.

Poiché il valore predefinito per `$ConfirmPreference` è `High`, solo `Remove-AzureRmDnsZone` richiede la conferma per impostazione predefinita.

Per eseguire l'override dell'impostazione `$ConfirmPreference` corrente è possibile usare il parametro `-Confirm`. Se si specifica `-Confirm` o `-Confirm:$True`, il cmdlet chiede conferma prima dell'esecuzione. Se si specifica `-Confirm:$False`, il cmdlet non chiede alcuna conferma.

Per altre informazioni su `-Confirm` e `$ConfirmPreference`, vedere [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) (Informazioni sulle variabili di preferenza).


## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create in questo articolo, seguire questa procedura:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle zone DNS private, vedere [Using Azure DNS for private domains](private-dns-overview.md) (Uso di Azure DNS per domini privati).

Per informazioni su alcuni scenari comuni che possono essere realizzati con le zone private in DNS Azure, vedere [Private Zone scenarios](./private-dns-scenarios.md) (Scenari relativi alle zone private).

Per altre informazioni sulla gestione dei record DNS in DNS Azure, vedere [Gestire i record e i set di record DNS in DNS Azure usando PowerShell](dns-operations-recordsets.md).

