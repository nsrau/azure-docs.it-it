<properties
   pageTitle="Creare un set di record e i record per una zona DNS utilizzando CLI | Microsoft Azure"
   description="Come creare i record host per DNS di Azure. Configurare i set di record e i record usando CLI"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Creare set di record e record DNS con l'interfaccia della riga di comando

> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interfaccia della riga di comando di Azure](dns-getstarted-create-recordset-cli.md)


Questo articolo illustra la creazione di record e set di record con l'interfaccia della riga di comando. Dopo aver creato la zona DNS, è necessario aggiungere i record DNS per il dominio. A questo scopo, occorre comprendere prima di tutto i record e i set di record DNS.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Creare un nuovo set di record e un record

Questa sezione mostra come creare record e un set di record. In questo esempio si creerà un set di record con il nome relativo *www* nella zona DNS *contoso.com*. Il nome completo dei record sarà *www.contoso.com*. Il tipo di record è *A* e il valore TTL è 60 secondi. Con il completamento di questo passaggio è stato creato un set di record vuoto.

Per creare un set di record nell'apice della zona (in questo caso "contoso.com"), usare il nome del record "@", incluse le virgolette. Si tratta di una convenzione comune di DNS.

### 1\. Creare un set di record

Per creare un set di record, usare `azure network dns record-set create`. Specificare il gruppo di risorse, il nome della zona, il nome relativo del set di record, il tipo di record e la durata (TTL). Se il parametro --ttl non è definito, viene impostato il valore predefinito 4 (in secondi). Con il completamento di questo passaggio si avrà un set di record vuoto "www".
	
*Utilizzo: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\. Aggiungere record

Per poter usare il set di record *www* appena creato, è necessario aggiungervi record. I record vengono aggiunti al set di record utilizzando il `azure network dns record-set add-record`.

I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record. Ad esempio, quando si usa un set di record di tipo *A*, si possono specificare record solo con il parametro `-a <IPv4 address>`.

È possibile aggiungere record IPv4 *A* al set di record *www* con il comando seguente:

*Utilizzo: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## Altri esempi di tipi di record

Gli esempi seguenti illustrano come creare un set di record per ogni tipo di record, ognuno contenente un singolo record.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## Passaggi successivi

Per gestire i set di record e i record, vedere [Come creare e gestire record e set di record DNS con il portale di Azure](dns-operations-recordsets-portal.md).

Per altre informazioni sul servizio DNS di Azure, vedere [Panoramica di DNS di Azure](dns-overview.md).

<!---HONumber=AcomDC_0511_2016-->