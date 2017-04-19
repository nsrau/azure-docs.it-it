---
title: Come gestire i record DNS inversi per i servizi tramite l&quot;interfaccia della riga di comando di Azure | Documentazione Microsoft
description: "Come gestire i record DNS inversi o i record PTR per i servizi di Azure tramite l’interfaccia della riga di comando di Azure in Gestione risorse"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 1ac7bc232082100cd0f0fe3e337930a6c5e9ede9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Come gestire i record DNS inversi per i servizi tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

* [Interfaccia della riga di comando di Azure 1.0](dns-reverse-dns-record-operations-cli-nodejs.md): l'interfaccia della riga di comando per il modello di distribuzione classico e di gestione delle risorse.
* [Interfaccia della riga di comando di Azure 2.0](dns-reverse-dns-record-operations-cli.md): interfaccia avanzata per il modello di distribuzione di gestione delle risorse.

## <a name="introduction"></a>Introduzione

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Per altre informazioni sul modello di distribuzione classica, vedere [Come gestire i record DNS inversi per i servizi di Azure (classico) con Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).


## <a name="validation-of-reverse-dns-records"></a>Convalida dei record DNS inversi
Per impedire che qualcun altro possa creare record DNS inversi mappati ai propri domini DNS, Azure consente la creazione di un record DNS inverso solo se una delle condizioni seguenti è vera:

* Il valore "ReverseFqdn" corrisponde al valore "Fqdn" per la risorsa indirizzo IP pubblico per cui è stato specificato o al valore "Fqdn" per qualsiasi indirizzo IP pubblico nella stessa sottoscrizione, ad esempio "ReverseFqdn" è "contosoapp1.northus.cloudapp.azure.com.".
* Il valore "ReverseFqdn" si risolve in avanti nel nome o IP dell'indirizzo IP pubblico per cui è stato specificato o nel valore "Fqdn" o IP di qualsiasi indirizzo IP pubblico nella stessa sottoscrizione, ad esempio "ReverseFqdn" è "app1.contoso.com.", un alias CName per "contosoapp1.northus.cloudapp.azure.com."

Controlli di convalida vengono eseguiti solo quando la proprietà DNS inverso per un indirizzo IP pubblico viene impostata o modificata. La convalida non viene ripetuta periodicamente.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Aggiungere il DNS inverso agli indirizzi IP pubblici esistenti
È possibile aggiungere il DNS inverso a un indirizzo IP pubblico usando azure network public-ip set:

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Se si desidera aggiungere il DNS inverso a un indirizzo IP pubblico esistente che non dispone già di un nome DNS è necessario specificare anche un nome DNS. A tale scopo è possibile usare azure network public-ip set:

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Creare un indirizzo IP pubblico con il DNS inverso
È possibile aggiungere un nuovo indirizzo IP pubblico con la proprietà DNS inverso specificata usando azure network public-ip create.

```azurecli
az network public-ip create --name PublicIp --resource-group NRP-DemoRG-PS --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Visualizzare il DNS inverso per gli indirizzi IP pubblici esistenti
È possibile visualizzare il valore configurato per un indirizzo IP pubblico esistente usando azure network public-ip show.

```azurecli
 az network public-ip show --name PublicIp --resource-group NRP-DemoRG-PS
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Rimuovere il DNS inverso da indirizzi IP pubblici esistenti
È possibile rimuovere la proprietà DNS inverso da un indirizzo IP pubblico usando azure network public-ip set. Questa operazione viene eseguita impostando il valore della proprietà ReverseFqdn su un valore vuoto:

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


