<properties 
   pageTitle="Operazioni sulle zone DNS utilizzando CLI | Microsoft Azure" 
   description="È possibile gestire le zone DNS utilizzando Azure CLI. Come aggiornare, eliminare e creare le zone DNS in DNS di Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Come gestire le zone DNS utilizzando CLI

> [AZURE.SELECTOR]
- [Interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)

Questa guida illustra come gestire le risorse della zona DNS usando l'interfaccia della riga di comando multipiattaforma di Azure.

>[AZURE.NOTE] DNS di Azure è un servizio solo di Gestione risorse di Azure. Non include un’API ASM. È pertanto necessario garantire che l’interfaccia della riga di comando di Azure sia configurata per utilizzare la modalità di gestione risorse, utilizzando il comando 'azure config mode arm'.

>Se viene visualizzato "errore: 'dns' non è un comando di azure" è probabilmente perché si sta utilizzando l’interfaccia della riga di comando di in modalità ASM, non in modalità gestione risorse.
 
## Creare una nuova zona DNS

Per creare una nuova zona DNS per ospitare il dominio, utilizzare il `azure network dns zone create`:

	azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

L'operazione crea una nuova zona DNS in Azure DNS. È possibile specificare facoltativamente una matrice di tag di Gestione risorse di Azure. Per altre informazioni, vedere [ETag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags).

Il nome della zona deve essere univoco all'interno del gruppo di risorse e la zona non deve esistere già, altrimenti l'operazione non riuscirà.

Lo stesso nome di zona può essere usato nuovamente in un gruppo di risorse diverso o in un'altra sottoscrizione Azure. Se più zone condividono lo stesso nome, a ogni istanza verranno assegnati diversi indirizzi del server dei nomi e può essere delegata solo un'istanza dal dominio padre. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

## Ottenere una zona DNS

Per recuperare una zona DNS, utilizzare il `azure network dns zone show`:

	azure network dns zone show myresourcegroup contoso.com

L'operazione restituisce una zona DNS con il relativo id, numero di set di record e tag.


## Elencare le zone DNS

Per recuperare le zone DNS all'interno di un gruppo di risorse, utilizzare `azure network dns zone list`:

	azure network dns zone list myresourcegroup


## Aggiornare una zona DNS

È possibile apportare modifiche a una risorsa di zona DNS usando `azure network dns zone set`. Ciò non consente di aggiornare alcuni dei set di record DNS compresi nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Attualmente è limitata ai tag di Gestione risorse di Azure relativi alla risorsa di zona. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags).

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Eliminare una zona DNS

Le zone DNS possono essere eliminate utilizzando il `azure network dns zone delete`.
 
Prima di eliminare una zona DNS di DNS di Azure, sarà necessario eliminare tutti i set di record, ad eccezione dei record NS e SOA alla radice della zona in cui sono stati creati automaticamente quando è stata creata la zona.

	azure network dns zone delete myresourcegroup contoso.com 

Questa operazione ha un'opzione "-q" facoltativa, che elimina la richiesta di conferma della rimozione della zona DNS.


## Passaggi successivi


Informazioni su [come gestire i record DNS](dns-operations-recordsets-cli.md) e [automatizzare operazioni con .NET SDK](dns-sdk.md)

<!---HONumber=AcomDC_0309_2016-->