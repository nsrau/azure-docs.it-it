---
title: Introduzione a DNS Azure con PowerShell | Microsoft Docs
description: Informazioni su come creare una zona e un record DNS in DNS Azure. Si tratta di una guida dettagliata per creare e gestire la prima zona e il primo record DNS usando PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: 3f67d4f537a832d1d3e13ff2a67645ae17ed8669
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="get-started-with-azure-dns-using-powershell"></a>Introduzione a DNS Azure con PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-getstarted-cli.md)

Questo articolo illustra i passaggi per creare la prima zona e il primo record DNS con Azure PowerShell. È possibile eseguire questi passaggi usando il portale di Azure o nell'interfaccia della riga di comando di Azure multipiattaforma. DNS di Azure supporta anche la creazione di domini privati. Per istruzioni dettagliate sulla creazione della prima zona e del primo record DNS privati, vedere [Introduzione alle zone private di DNS di Azure con PowerShell](private-dns-getstarted-powershell.md).

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare infine la zona DNS su Internet, è necessario configurare i server dei nomi per il dominio. Ogni passaggio è illustrato di seguito.

Queste istruzioni presuppongono che Azure PowerShell sia già stato installato e che sia già stato eseguito l'accesso. Per informazioni, vedere [Come gestire le zone DNS usando PowerShell](dns-operations-dnszones.md).

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima di creare la zona DNS, viene creato un gruppo di risorse per contenere la zona DNS. Di seguito è riportato il comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Viene creata una zona DNS con il cmdlet `New-AzureRmDnsZone` . L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Creare un record DNS

I set di record vengono creati usando il cmdlet `New-AzureRmDnsRecordSet`. L'esempio seguente crea un record con il nome relativo "www" nella zona DNS "contoso.com" nel gruppo di risorse "MyResourceGroup". Il nome completo del set di record sarà "www.contoso.com". Il tipo di record è "A", con indirizzo IP "1.2.3.4", e la durata (TTL) è 3600 secondi.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Per altri tipi di record, per set di record con più di un record e per modificare i record esistenti, vedere [Gestire record e set di record DNS con Azure PowerShell](dns-operations-recordsets.md). 


## <a name="view-records"></a>Visualizzare i record

Per elencare i record DNS nella zona, usare:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>Aggiornare i server dei nomi

Dopo essersi assicurati che la zona e i record DNS siano stati configurati correttamente, è necessario configurare il nome di dominio in modo che usi i server dei nomi di DNS Azure. Ciò consente agli altri utenti su Internet di trovare i record DNS.

I server dei nomi per la zona vengono specificati tramite il cmdlet `Get-AzureRmDnsZone`:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

I server dei nomi devono essere configurati con il registrar dei nomi di dominio, in cui è stato acquistato il nome di dominio. Il registrar offrirà l'opzione per la configurazione dei server dei nomi per il dominio. Per altre informazioni, vedere [Delegare un dominio al servizio DNS Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create in questo articolo, seguire questa procedura:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio DNS Azure, vedere [Panoramica di DNS Azure](dns-overview.md).

Per altre informazioni sulla gestione delle zone DNS in DNS Azure, vedere [Gestire le zone DNS in DNS Azure usando PowerShell](dns-operations-dnszones.md).

Per altre informazioni sulla gestione dei record DNS in DNS Azure, vedere [Gestire i record e i set di record DNS in DNS Azure usando PowerShell](dns-operations-recordsets.md).

