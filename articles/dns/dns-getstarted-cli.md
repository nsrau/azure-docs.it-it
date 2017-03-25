---
title: Introduzione a DNS Azure con l&quot;interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come creare una zona e un record DNS in DNS Azure. Si tratta di una guida dettagliata per creare e gestire la prima zona e il primo record DNS usando l&quot;interfaccia della riga di comando di Azure 2.0.
services: dns
documentationcenter: na
author: jtuliani
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
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 981275da75adb11e8fff16f77e31d4ff86affe1f
ms.lasthandoff: 03/11/2017

---

# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Introduzione a DNS Azure con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-getstarted-cli.md)

Questo articolo illustra i passaggi per creare la prima zona e il primo record DNS usando l'interfaccia della riga di comando di Azure 2.0 tra piattaforme, disponibile per Windows, Mac e Linux. È anche possibile eseguire questi passaggi tramite il portale di Azure o Azure PowerShell.

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare infine la zona DNS su Internet, è necessario configurare i server dei nomi per il dominio. Ogni passaggio è illustrato di seguito.

Queste istruzioni presuppongono che l'interfaccia della riga di comando di Azure 1.0 sia già stata installata e che sia già stato eseguito l'accesso. Per informazioni, vedere [Come gestire le zone DNS usando l'interfaccia della riga di comando di Azure 2.0](dns-operations-dnszones-cli.md).


## <a name="create-a-dns-zone"></a>Creare una zona DNS

Una zona DNS viene creata utilizzando il comando `az network dns zone create` . Per visualizzare la guida per questo comando, digitare `az network dns zone create -h`.

L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```


## <a name="create-a-dns-record"></a>Creare un record DNS

Per creare un record DNS, usare il comando `az network dns record-set [record type] add-record`. Per informazioni, ad esempio per i record A, vedere `azure network dns record-set A add-record -h`.

L'esempio seguente crea un record con il nome relativo "www" nella zona DNS "contoso.com" nel gruppo di risorse "MyResourceGroup". Il nome completo del set di record sarà "www.contoso.com". Il tipo di record è "A" con indirizzo IP "1.2.3.4" e viene usato un valore TTL predefinito di 3600 secondi (1 ora).

```azurecli
az network dns record-set A add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Per altri tipi di record, per set di record con più di un record, per valori TTL alternativi e per modificare i record esistenti, vedere [Gestire record e set di record DNS con l'interfaccia della riga di comando di Azure 2.0](dns-operations-recordsets-cli.md).


## <a name="view-records"></a>Visualizzare i record

Per elencare i record DNS nella zona, usare:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```


## <a name="update-name-servers"></a>Aggiornare i server dei nomi

Dopo essersi assicurati che la zona e i record DNS siano stati configurati correttamente, è necessario configurare il nome di dominio in modo che usi i server dei nomi di DNS Azure. Ciò consente agli altri utenti su Internet di trovare i record DNS.

I server dei nomi per la zona vengono specificati tramite il comando `az network dns zone show`. Per visualizzare i nomi dei server dei nomi, usare l'output JSON, come mostrato nell'esempio seguente.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

I server dei nomi devono essere configurati con il registrar dei nomi di dominio, in cui è stato acquistato il nome di dominio. Il registrar offrirà l'opzione per la configurazione dei server dei nomi per il dominio. Per altre informazioni, vedere [Delegare un dominio al servizio DNS Azure](dns-domain-delegation.md).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio DNS Azure, vedere [Panoramica di DNS Azure](dns-overview.md).

Per altre informazioni sulla gestione delle zone DNS in DNS Azure, vedere [Gestire le zone DNS in DNS Azure usando l'interfaccia della riga di comando di Azure 2.0](dns-operations-dnszones-cli.md).

Per altre informazioni sulla gestione dei record DNS in DNS Azure, vedere [Gestire i record e i set di record DNS in DNS Azure usando l'interfaccia della riga di comando di Azure 2.0](dns-operations-recordsets-cli.md).

