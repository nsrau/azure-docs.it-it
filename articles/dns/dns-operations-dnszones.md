<properties 
   pageTitle="Gestire le zone DNS mediante PowerShell | Microsoft Azure" 
   description="È possibile gestire le zone DNS usando PowerShell di Azure. Come aggiornare, eliminare e creare le zone DNS in DNS di Azure" 
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
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Come gestire le zone DNS utilizzando PowerShell

> [AZURE.SELECTOR]
- [Interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



Questo articolo spiega come gestire una zona DNS mediante PowerShell. Per eseguire questi passaggi è necessario installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager (versione 1.0 o successiva). Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## Creare una nuova zona DNS

Per creare una zona DNS, vedere le informazioni su come [creare una zona DNS mediante PowerShell](dns-getstarted-create-dnszone.md).

## Ottenere una zona DNS

Per recuperare una zona DNS, usare il cmdlet `Get-AzureRmDnsZone`. Questa operazione restituisce un oggetto di zona DNS corrispondente a una zona esistente nel servizio DNS di Azure. L'oggetto contiene i dati sulla zona (ad esempio il numero di set di record), ma non contiene i set di record stessi.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## Elencare le zone DNS

Omettendo il nome della zona da `Get-AzureRmDnsZone`, è possibile enumerare tutte le zone in un gruppo di risorse. Questa operazione restituisce una matrice di oggetti di zona.

	$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## Aggiornare una zona DNS

È possibile apportare modifiche a una risorsa di zona DNS usando `Set-AzureRmDnsZone`. Ciò non consente di aggiornare alcuni dei set di record DNS compresi nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Attualmente è limitata ai tag di Gestione risorse di Azure relativi alla risorsa di zona. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags).

Usare una delle due opzioni seguenti per aggiornare la zona DNS:

### Specificare la zona usando il nome della zona e del gruppo di risorse.

	Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Specificare la zona usando un oggetto $zone

Specificare la zona usando un oggetto $zone da `Get-AzureRmDnsZone`. Quando si usa `Set-AzureRmDnsZone` con un oggetto $zone, i controlli ETag vengono usati per verificare che le modifiche simultanee non vengano sovrascritte. È possibile usare l'opzione facoltativa *-Overwrite* per disattivare questi controlli. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags).


	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	<..modify $zone.Tags here...>
	Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## Eliminare una zona DNS

È possibile eliminare le zone DNS usando il cmdlet Remove-AzureRmDnsZone.
 
Prima di eliminare una zona DNS di DNS di Azure, sarà necessario eliminare tutti i set di record, ad eccezione dei record NS e SOA alla radice della zona in cui sono stati creati automaticamente quando è stata creata la zona.

Usare una delle due opzioni seguenti per rimuovere la zona DNS:

### Specificare la zona usando il nome della zona e del gruppo di risorse:

Questa operazione ha un'opzione *-Force* facoltativa che elimina la richiesta di conferma della rimozione della zona DNS.

	Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

### Specificare la zona usando un oggetto $zone 

Specificare la zona usando un oggetto $zone da `Get-AzureRmDnsZone`. Questa operazione ha un'opzione *-Force* facoltativa che elimina la richiesta di conferma della rimozione della zona DNS. Come con `Set-AzureRmDnsZone`, specificare la zona usando un oggetto consente di abilitare i controlli ETag per garantire che le modifiche simultanee non vengano eliminate. <BR> Il flag facoltativo *-Overwrite* disattiva questi controlli. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags).

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



L'oggetto di zona può essere anche reindirizzato invece che passato come parametro:

	Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## Passaggi successivi

Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset.md) per avviare la risoluzione dei nomi per il dominio Internet.

<!---HONumber=AcomDC_0518_2016-->