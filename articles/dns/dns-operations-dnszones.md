<properties 
   pageTitle="Operazioni sulle zone DNS | Microsoft Azure" 
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
   ms.date="03/17/2016"
   ms.author="cherylmc"/>

# Come gestire le zone DNS utilizzando PowerShell

> [AZURE.SELECTOR]
- [Interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


Questa guida descrive come gestire per la zona DNS. E consentirà di comprendere la sequenza delle operazioni da eseguire per amministrare la zona DNS.


## Creare una nuova zona DNS

Per creare una nuova zona DNS in cui ospitare il dominio, usare il cmdlet New-AzureRmDnsZone:

		PS C:\> $zone = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

L'operazione crea una nuova zona DNS nel servizio DNS di Azure e restituisce un oggetto locale corrispondente a tale area. È possibile specificare facoltativamente una matrice di tag di Gestione risorse di Azure. Per altre informazioni, vedere [ETag e tag](../dns-getstarted-create-dnszone.md#Etags-and-tags).

Il nome della zona deve essere univoco all'interno del gruppo di risorse e la zona non deve esistere già, altrimenti l'operazione non riuscirà.

Lo stesso nome di zona può essere usato nuovamente in un gruppo di risorse diverso o in un'altra sottoscrizione Azure. Se più zone condividono lo stesso nome, a ogni istanza verranno assegnati diversi indirizzi del server dei nomi e può essere delegata solo un'istanza dal dominio padre. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

## Ottenere una zona DNS

Per recuperare una zona DNS, usare il cmdlet Get-AzureRmDnsZone:

		PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

L'operazione restituisce un oggetto di zona DNS corrispondente a una zona esistente nel servizio DNS di Azure. Questo oggetto contiene i dati sulla zona (ad esempio il numero di set di record), ma non contiene i set di record stessi.

## Elencare le zone DNS

Omettendo il nome della zona da Get-AzureRmDnsZone, è possibile enumerare tutte le zone in un gruppo di risorse:

	PS C:\> $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

Questa operazione restituisce una matrice di oggetti di zona.

## Aggiornare una zona DNS

È possibile apportare modifiche a una risorsa di zona DNS usando Set-AzureRmDnsZone. Ciò non consente di aggiornare alcuni dei set di record DNS compresi nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Attualmente è limitata ai tag di Gestione risorse di Azure relativi alla risorsa di zona. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags).

Usare una delle due opzioni seguenti per aggiornare la zona DNS:

### Opzione 1
 
Specificare la zona usando il nome della zona e del gruppo di risorse.

	PS C:\> Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Opzione 2
Specificare la zona usando un oggetto $zone da Get-AzureRmDnsZone:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> <..modify $zone.Tags here...>
	PS C:\> Set-AzureRmDnsZone -Zone $zone [-Overwrite]

Quando si usa il cmdlet Set-AzureRmDnsZone con un oggetto $zone, i controlli "etag" vengono usati per verificare che le modifiche simultanee non vengano sovrascritte. Puoi usare l'opzione "-Overwrite" facoltativa per disattivare questi controlli. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags).

## Eliminare una zona DNS

È possibile eliminare le zone DNS usando il cmdlet Remove-AzureRmDnsZone.
 
Prima di eliminare una zona DNS di DNS di Azure, sarà necessario eliminare tutti i set di record, ad eccezione dei record NS e SOA alla radice della zona in cui sono stati creati automaticamente quando è stata creata la zona.

Usare una delle due opzioni seguenti per rimuovere la zona DNS:

### Opzione 1

Specificare la zona usando il nome della zona e del gruppo di risorse:

	PS C:\> Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

Questa operazione ha un'opzione "-Force" facoltativa, che elimina la richiesta di conferma della rimozione della zona DNS.

### Opzione 2

Specificare la zona usando un oggetto $zone da Get-AzureRmDnsZone:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]

L'opzione "-Force" è la stessa dell'opzione 1.

Come per "Set-AzureRmDnsZone", specificare la zona usando un oggetto $zone consente di abilitare i controlli "etag" per garantire che le modifiche simultanee non vengano eliminate. <BR> Il flag "-Overwrite" facoltativo disattiva questi controlli. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags).

L'oggetto di zona può essere anche reindirizzato invece che passato come parametro:

	PS C:\> Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## Passaggi successivi


[Gestire i record DNS](dns-operations-recordsets.md)

[Automatizzare le operazioni usando .NET SDK](dns-sdk.md)

<!---HONumber=AcomDC_0427_2016-->