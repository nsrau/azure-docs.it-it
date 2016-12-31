---
title: Creare un set di record e i record per una zona DNS usando l&quot;interfaccia della riga di comando | Documentazione Microsoft
description: Come creare i record host per DNS di Azure. Configurare i set di record e i record usando CLI
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: e377f176fe24a8e7e42d409f86d6b0093ce5e7c4

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Creare set di record e record DNS con l'interfaccia della riga di comando

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Interfaccia della riga di comando di Azure](dns-getstarted-create-recordset-cli.md)

Questo articolo illustra la creazione di record e set di record con l'interfaccia della riga di comando. A tale scopo, è necessario comprendere i record e i set di record DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

In questa sezione viene descritto come creare record DNS in DNS di Azure. Negli esempi si suppone che l'utente abbia già [installato l'interfaccia della riga di comando di Azure, effettuato l'accesso e creato una zona DNS](dns-getstarted-create-dnszone-cli.md).

Gli esempi in questa pagina usano tutti il tipo di record DNS "A". Per altri tipi di record e maggiori informazioni su come gestire i record DNS e i set di record, vedere [Gestire record e set di record DNS con l'interfaccia della riga di comando di Azure](dns-operations-recordsets-cli.md).

## <a name="create-a-record-set-and-record"></a>Creare un set di record e un record

Questa sezione illustra come creare record e un set di record. In questo esempio verrà creato un set di record con il nome relativo "www" nella zona DNS "contoso.com". Il nome completo dei record sarà "www.contoso.com". Il tipo di record è "A" e il valore TTL è 60 secondi. Con il completamento di questo passaggio è stato creato un set di record vuoto.

Per creare un set di record nel dominio radice, in questo caso "contoso.com", usare il nome record "@",, incluse le virgolette. Si tratta di una convenzione comune di DNS.

### <a name="1-create-a-record-set"></a>1. Creare un set di record

Se il nuovo record ha lo stesso nome e tipo di un record esistente, è necessario aggiungerlo al set di record esistente. È possibile omettere questo passaggio e passare a [Add records](#add-records) (Aggiungi record) di seguito. Altrimenti, se il nuovo record ha un nome e un tipo diversi rispetto a tutti i record esistenti, è necessario creare un nuovo set di record.

I set di record vengono creati usando il comando `azure network dns record-set create`. Per altre informazioni, vedere `azure network dns record-set create -h`.  

Quando si crea un set di record, è necessario specificare il nome, la zona, il Time-to-Live (TTL) e il tipo di record. 

```azurecli
azure network dns record-set create myresourcegroup contoso.com www A 60
```

Con il completamento di questo passaggio si avrà un set di record vuoto "www". Per usare il set di record "www" appena creato è necessario aggiungervi record.

### <a name="2-add-records"></a>2. Aggiungere record

Aggiungere record al set di record usando `azure network dns record-set add-record`. Per altre informazioni, vedere `azure network dns record-set add-record -h`.

I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record. Ad esempio, quando si usa un set di record di tipo "A" sarà possibile specificare solo i record con il parametro `-a <IPv4 address>`. Vedere `azure network dns record-set add-record -h` per elencare i parametri per altri tipi di record.

È possibile aggiungere record A al set di record "www" creato sopra con il comando seguente:

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 1.2.3.4
```

### <a name="verify-name-resolution"></a>Verificare la risoluzione dei nomi

È possibile verificare che i record DNS siano presenti sui server di nomi DNS di Azure usando gli strumenti DNS come nslookup, DIG o il [cmdlet di PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario [indirizzare la query DNS direttamente a uno dei server dei nomi per la zona](dns-getstarted-create-dnszone.md#test-name-servers). Assicurarsi di sostituire i valori corretti per la propria zona di record nel comando seguente.

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [delegare il nome di dominio ai server dei nomi DNS di Azure](dns-domain-delegation.md)

Informazioni su come [gestire le zone DNS usando l'interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md).

Informazioni su come [gestire i record e i set di record DNS usando l'interfaccia della riga di comando di Azure](dns-operations-recordsets-cli.md).




<!--HONumber=Dec16_HO2-->


