---
title: Gestire le zone DNS usando l&quot;interfaccia della riga di comando | Documentazione Microsoft
description: "È possibile gestire le zone DNS utilizzando Azure CLI. Come aggiornare, eliminare e creare le zone DNS in DNS di Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 927503bb18a63db1da2c92e034dbccb7707afab4

---

# <a name="how-to-manage-dns-zones-using-cli"></a>Come gestire le zone DNS utilizzando CLI

> [!div class="op_single_selector"]
> * [Interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

Questa guida illustra come gestire le risorse della zona DNS usando l'interfaccia della riga di comando multipiattaforma di Azure.

Per queste istruzioni viene usata l'interfaccia della riga di comando di Microsoft Azure. Assicurarsi di eseguire l'aggiornamento alla versione più recente dell'interfaccia della riga di comando di Azure per usare questi comandi del servizio DNS di Azure. È possibile installare l'interfaccia della riga di comando di Azure per Windows, Linux o MAC. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

DNS di Azure è un servizio solo di Gestione risorse di Azure. Non include un modello di distribuzione "classico". È necessario assicurarsi che l'interfaccia della riga di comando di Azure sia configurata per l'uso della modalità Resource Manager. A tale scopo è possibile usare il comando `azure config mode arm`.

Se viene visualizzato il messaggio di errore con l'avviso che *"dns" non è un comando di Azure*, probabilmente è perché si sta usando l’interfaccia della riga di comando di Azure in modalità Azure Service Management e non in modalità Resource Manager.

Tutti i comandi dell'interfaccia della riga di comando relativi a DNS di Azure iniziano con `azure network dns`. Sono disponibili informazioni per ogni comando tramite l'opzione `--help` (forma breve `-h`).  ad esempio:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Una zona DNS viene creata utilizzando il comando `azure network dns zone create` . Per altre informazioni, vedere `azure network dns zone create -h`.

Gli esempi seguenti mostrano come creare una zona DNS con o senza [tag di Azure Resource Manager](dns-zones-records.md#tags).

### <a name="to-create-a-dns-zone"></a>Per creare una zona DNS

L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare la zona DNS, sostituendo i valori con quelli personalizzati.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Per creare una zona DNS con tag.

L'esempio seguente illustra come creare una zona DNS con due tag, *project = demo* ed *env = test*, usando il parametro `--tags` (forma breve `-t`).

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>Ottenere una zona DNS

Per recuperare una zona DNS, usare `azure network dns zone show`. Per altre informazioni, vedere `azure network dns zone show -h`.

L'esempio seguente restituisce la zona DNS *contoso.com* e i relativi dati associati dal gruppo di risorse *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

Si noti che i record DNS non vengono restituiti da `azure network dns zone show`. Per elencare i record DNS, usare `azure network dns record-set list`.


## <a name="list-dns-zones"></a>Elencare le zone DNS

Per enumerare le zone DNS, usare `azure network dns zone list`. Per altre informazioni, vedere `azure network dns zone list -h`.

Se si specifica il gruppo di risorse, vengono elencate solo le zone all'interno del gruppo di risorse:

```azurecli
azure network dns zone list MyResourceGroup
```

Se invece il gruppo di risorse viene omesso, sono elencate tutte le zone nella sottoscrizione:

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>Aggiornare una zona DNS

È possibile apportare modifiche a una risorsa di zona DNS usando `azure network dns zone set`. Per altre informazioni, vedere `azure network dns zone set -h`.

Questo comando non consente di aggiornare alcun set di record DNS compreso nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Queste proprietà sono attualmente limitate ai ["tag" di Azure Resource Manager](dns-zones-records.md#tags) relativi alla risorsa di zona.

L'esempio seguente illustra come aggiornare i tag in una zona DNS. I tag esistenti vengono sostituiti dal valore specificato.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>Eliminare una zona DNS

Le zone DNS possono essere eliminate usando `azure network dns zone delete`. Per altre informazioni, vedere `azure network dns zone delete -h`.

> [!NOTE]
> L'eliminazione di una zona DNS comporterà anche l'eliminazione di tutti i record DNS all'interno della zona. Questa operazione non può essere annullata. Se la zona DNS è in uso, i servizi che la usano rileveranno un errore quando la zona viene eliminata.
>
>Per evitare l'eliminazione accidentale di una zona, vedere [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Come proteggere le zone e i record DNS).

Questo comando richiede una conferma. L'opzione facoltativa `--quiet` (forma breve `-q`) elimina questa richiesta.

L'esempio seguente mostra come eliminare la zona *contoso.com* dal gruppo di risorse *MyResourceGroup*.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [gestire record e set di record](dns-getstarted-create-recordset-cli.md) nella zona DNS.
<br>
Informazioni su come [delegare il dominio al servizio DNS di Azure](dns-domain-delegation.md).




<!--HONumber=Dec16_HO2-->


