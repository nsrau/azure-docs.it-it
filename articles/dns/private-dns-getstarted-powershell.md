---
title: Introduzione alle zone private di Azure DNS con PowerShell | Microsoft Docs
description: Informazioni su come creare una zona DNS privata e un record in Azure DNS. Questa guida dettagliata illustra come creare e gestire la prima zona DNS privata e il primo record usando PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d71e2391b6415b2403447479dea4fd0a3b818ed0
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2017
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Introduzione alle zone private di Azure DNS con PowerShell

Questo articolo illustra i passaggi per creare la prima zona DNS privata e il primo record con Azure PowerShell.

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare una zona DNS privata nella rete virtuale, specificare l'elenco di reti virtuali autorizzate a risolvere i record nella zona.  Tali reti vengono dette "reti di risoluzione".  È anche possibile specificare un set di reti virtuali per cui Azure DNS manterrà i record dei nomi host ogni volta che una macchina virtuale viene creata o eliminata definitivamente oppure ne viene modificato l'indirizzo IP.  Tali reti vengono dette "reti di registrazione".

Poiché questa funzionalità è attualmente disponibile in versione di anteprima gestita, verrà fornito una modulo di PowerShell di anteprima.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima di creare la zona DNS, viene creato un gruppo di risorse per contenere la zona DNS. Di seguito è riportato il comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Viene creata una zona DNS con il cmdlet `New-AzureRmDnsZone` . L'esempio seguente crea una zona DNS denominata *contoso.local* nel gruppo di risorse denominato *MyResourceGroup* e rende la zona DNS disponibile per la rete virtuale denominata *MyAzureVnet*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

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

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create in questo articolo, seguire questa procedura:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle zone DNS private, vedere [Using Azure DNS for private domains](private-dns-overview.md) (Uso di Azure DNS per domini privati).

Per altre informazioni sulla gestione dei record DNS in DNS Azure, vedere [Gestire i record e i set di record DNS in DNS Azure usando PowerShell](dns-operations-recordsets.md).

