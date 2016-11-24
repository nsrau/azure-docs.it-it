---
title: Creare un set di record e i record per una zona DNS usando l&quot;interfaccia della riga di comando | Documentazione Microsoft
description: Come creare i record host per DNS di Azure. Configurare i set di record e i record usando CLI
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: f15333cde5304579ff0d0ba9571a870ddde19163

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Creare set di record e record DNS con l'interfaccia della riga di comando

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Interfaccia della riga di comando di Azure](dns-getstarted-create-recordset-cli.md)

Questo articolo illustra la creazione di record e set di record con l'interfaccia della riga di comando. Dopo aver creato la zona DNS è necessario aggiungere i record DNS per il dominio. A tale scopo, è necessario comprendere i record e i set di record DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>Creare un set di record e un record

Questa sezione illustra come creare record e un set di record. In questo esempio verrà creato un set di record con il nome relativo "www" nella zona DNS "contoso.com". Il nome completo dei record sarà "www.contoso.com". Il tipo di record è "A" e il valore TTL è 60 secondi. Con il completamento di questo passaggio è stato creato un set di record vuoto.

Per creare un set di record nel dominio radice, in questo caso "contoso.com", usare il nome record "@",, incluse le virgolette. Si tratta di una convenzione comune di DNS.

### <a name="1-create-a-record-set"></a>1. Creare un set di record

Per creare un set di record usare `azure network dns record-set create`. Specificare il gruppo di risorse, il nome della zona, il nome relativo del set di record, il tipo di record e il TTL. Se il parametro `--ttl` non è definito, verrà impostato il valore predefinito di quattro secondi. Con il completamento di questo passaggio si avrà un set di record vuoto "www".

*Sintassi: network dns record-set create \<gruppo-risorse\> \<nome-zona-dns\> \<nome\> \<tipo\> \<ttl\>*

```azurecli
azure network dns record-set create myresourcegroup  contoso.com  www A  60
```

### <a name="2-add-records"></a>2. Aggiungere record

Per usare il set di record "www" appena creato è necessario aggiungervi record. Aggiungere record al set di record usando `azure network dns record-set add-record`.

I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record. Quando ad esempio si usa un set di record di tipo "A" sarà possibile specificare record solo con il parametro `-a <IPv4 address>`.

È possibile aggiungere record IPv4 *A* al set di record "www" con il comando seguente:

*Sintassi: network dns record-set add-record \<gruppo-risorse\> \<nome-zona-dns\> \<nome-record-set\> \<tipo\>*

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46
```

## <a name="additional-record-type-examples"></a>Altri esempi di tipi di record

Gli esempi seguenti illustrano come creare un set di record di ogni tipo di record. Ogni set di record contiene un singolo record.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per gestire i set di record e i record, vedere [Gestire record e set di record DNS con l'interfaccia della riga di comando](dns-operations-recordsets-portal.md).

Per altre informazioni sul servizio DNS di Azure, vedere [Panoramica di DNS di Azure](dns-overview.md).




<!--HONumber=Nov16_HO3-->


