<properties 
   pageTitle="Gestire le zone DNS mediante l'interfaccia della riga di comando | Microsoft Azure" 
   description="È possibile gestire le zone DNS utilizzando Azure CLI. Come aggiornare, eliminare e creare le zone DNS in DNS di Azure" 
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

# Come gestire le zone DNS utilizzando CLI

> [AZURE.SELECTOR]
- [Interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


Questa guida illustra come gestire le risorse della zona DNS usando l'interfaccia della riga di comando multipiattaforma di Azure.

Per queste istruzioni viene usata l'interfaccia della riga di comando di Microsoft Azure. Assicurarsi di eseguire l'aggiornamento alla versione più recente (0.9.8 o successiva) dell'interfaccia della riga di comando di Azure per usare i comandi del servizio DNS di Azure. Digitare `azure -v` per verificare quale versione dell'interfaccia della riga di comando di Azure è attualmente installata nel computer. È possibile installare l'interfaccia della riga di comando di Azure per Windows, Linux o MAC. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

DNS di Azure è un servizio solo di Gestione risorse di Azure. Non include un’API ASM. È necessario assicurarsi che l'interfaccia della riga di comando di Azure sia configurata per l'uso della modalità Resource Manager. A tale scopo è possibile usare il comando `azure config mode arm`.<BR> Se viene visualizzato il messaggio di errore con l'avviso che *"dns" non è un comando di Azure*, probabilmente è perché si sta usando l’interfaccia della riga di comando di Azure in modalità ASM e non in modalità Gestione risorse.

## Creare una nuova zona DNS

Per creare una nuova zona DNS in cui ospitare il dominio, vedere [Creare una zona DNS di Azure con l'interfaccia della riga di comando](dns-getstarted-create-dnszone-cli.md).

## Ottenere una zona DNS

Per recuperare una zona DNS, usare `azure network dns zone show`:

	azure network dns zone show myresourcegroup contoso.com

L'operazione restituisce una zona DNS con il relativo id, numero di set di record e tag.


## Elencare le zone DNS

Per recuperare le zone DNS all'interno di un gruppo di risorse, usare `azure network dns zone list`.

	azure network dns zone list myresourcegroup

## Aggiornare una zona DNS

È possibile apportare modifiche a una risorsa di zona DNS usando `azure network dns zone set`. Ciò non consente di aggiornare alcuni dei set di record DNS compresi nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Attualmente è limitata ai tag di Gestione risorse di Azure relativi alla risorsa di zona. Per altre informazioni, vedere [Etag e tag](dns-getstarted-create-dnszone.md#tagetag).

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Eliminare una zona DNS

Le zone DNS possono essere eliminate usando `azure network dns zone delete`. Questa operazione ha un'opzione facoltativa *-q* che elimina la richiesta di conferma della rimozione della zona DNS.
 
Prima di eliminare una zona DNS di DNS di Azure, sarà necessario eliminare tutti i set di record, ad eccezione dei record NS e SOA alla radice della zona in cui sono stati creati automaticamente quando è stata creata la zona.

	azure network dns zone delete myresourcegroup contoso.com 



## Passaggi successivi
Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset-cli.md) per avviare la risoluzione dei nomi per il dominio Internet.

<!---HONumber=AcomDC_0518_2016-->