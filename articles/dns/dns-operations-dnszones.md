---
title: Gestire le zone DNS mediante PowerShell | Documentazione Microsoft
description: "È possibile gestire le zone DNS usando PowerShell di Azure. Come aggiornare, eliminare e creare le zone DNS in DNS di Azure"
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
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: f6403718f106b5021ea5b41e0e91a11bd92d4830

---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Come gestire le zone DNS utilizzando PowerShell

> [!div class="op_single_selector"]
> * [Interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

Questo articolo spiega come gestire una zona DNS mediante PowerShell. Per eseguire questi passaggi è necessario installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager (versione 1.0 o successiva). Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

## <a name="create-a-new-dns-zone"></a>Creare una nuova zona DNS

Per creare una zona DNS, vedere le informazioni su come [creare una zona DNS mediante PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Ottenere una zona DNS

Per recuperare una zona DNS, usare il cmdlet `Get-AzureRmDnsZone` . Questa operazione restituisce un oggetto di zona DNS corrispondente a una zona esistente nel servizio DNS di Azure. L'oggetto contiene i dati sulla zona (ad esempio il numero di set di record), ma non contiene i set di record stessi.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
```

## <a name="list-dns-zones"></a>Elencare le zone DNS

Omettendo il nome della zona da `Get-AzureRmDnsZone`, è possibile enumerare tutte le zone in un gruppo di risorse. Questa operazione restituisce una matrice di oggetti di zona.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

## <a name="update-a-dns-zone"></a>Aggiornare una zona DNS

È possibile apportare modifiche a una risorsa di zona DNS usando `Set-AzureRmDnsZone`. Ciò non consente di aggiornare alcuni dei set di record DNS compresi nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Attualmente è limitata ai tag di Gestione risorse di Azure relativi alla risorsa di zona. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#tagetag) .

Usare una delle due opzioni seguenti per aggiornare la zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Specificare la zona usando il nome della zona e del gruppo di risorse.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]
```

### <a name="specify-the-zone-using-a-zone-object"></a>Specificare la zona usando un oggetto $zone

Specificare la zona usando un oggetto $zone da `Get-AzureRmDnsZone`. Quando si usa `Set-AzureRmDnsZone` con un oggetto $zone, i controlli ETag vengono usati per verificare che le modifiche simultanee non vengano sovrascritte. È possibile usare l'opzione facoltativa *-Overwrite* per disattivare questi controlli. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#tagetag) .

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
<..modify $zone.Tags here...>
Set-AzureRmDnsZone -Zone $zone [-Overwrite]
```

## <a name="delete-a-dns-zone"></a>Eliminare una zona DNS

È possibile eliminare le zone DNS usando il cmdlet Remove-AzureRmDnsZone.

Prima di eliminare una zona DNS di DNS di Azure, sarà necessario eliminare tutti i set di record, ad eccezione dei record NS e SOA alla radice della zona in cui sono stati creati automaticamente quando è stata creata la zona.

Usare una delle due opzioni seguenti per rimuovere la zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Specificare la zona usando il nome della zona e del gruppo di risorse:

Questa operazione ha un'opzione facoltativa `-Force` che elimina la richiesta di conferma della rimozione della zona DNS.

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-zone-using-a-zone-object"></a>Specificare la zona usando un oggetto $zone

Specificare la zona usando un oggetto $zone da `Get-AzureRmDnsZone`. Questa operazione ha un'opzione facoltativa `-Force` che elimina la richiesta di conferma della rimozione della zona DNS. Come con `Set-AzureRmDnsZone`, specificare la zona usando un oggetto consente di abilitare i controlli ETag per garantire che le modifiche simultanee non vengano eliminate.

Il flag facoltativo `-Overwrite` disattiva questi controlli. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#tagetag) .

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]
```

L'oggetto di zona può essere anche reindirizzato invece che passato come parametro:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset.md) per avviare la risoluzione dei nomi per il dominio Internet.




<!--HONumber=Nov16_HO3-->


