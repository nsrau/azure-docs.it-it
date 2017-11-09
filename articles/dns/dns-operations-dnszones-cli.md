---
title: Gestire le zone DNS in DNS di Azure - Interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: "È possibile gestire le zone DNS usando l'interfaccia della riga di comando Azure 2.0. Questo articolo illustra come aggiornare, eliminare e creare le zone DNS in DNS di Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
ms.openlocfilehash: 1414baf9e51d648cc3a46c4f8635040b4d276910
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-20"></a>Come gestire le zone DNS in DNS di Azure DNS usando l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [Portale](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-operations-dnszones-cli.md)


Questa guida illustra come gestire le zone DNS usando l'interfaccia della riga di comando di Azure multipiattaforma, disponibile per Windows, Mac e Linux. È anche possibile gestire le zone DNS usando [Azure PowerShell](dns-operations-dnszones.md) o il portale di Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

* [Interfaccia della riga di comando di Azure 1.0](dns-operations-dnszones-cli-nodejs.md): l'interfaccia della riga di comando per il modello di distribuzione classico e di gestione delle risorse.
* [Interfaccia della riga di comando di Azure 2.0](dns-operations-dnszones-cli.md): interfaccia avanzata per il modello di distribuzione di gestione delle risorse.

## <a name="introduction"></a>Introduzione

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Configurare l'interfaccia della riga di comando di Azure 2.0 per DNS di Azure

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di essere in possesso degli elementi seguenti.

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

* Installare la versione più recente dell'interfaccia della riga di comando di Azure 2.0, disponibile per Windows, Linux o Mac. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure

Aprire una finestra della console ed eseguire l'autenticazione con le credenziali. Per altre informazioni, vedere Accedere ad Azure dall'interfaccia della riga di comando di Azure

```
az login
```

### <a name="select-the-subscription"></a>Selezionare la sottoscrizione

Controllare le sottoscrizioni per l'account.

```
az account list
```

Scegliere le sottoscrizioni ad Azure da usare.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Risorse della Guida

Tutti i comandi dell'interfaccia della riga di comando 2.0 relativi a DNS di Azure iniziano con `az network dns`. Sono disponibili informazioni per ogni comando tramite l'opzione `--help` (forma breve `-h`).  ad esempio:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Una zona DNS viene creata utilizzando il comando `az network dns zone create` . Per altre informazioni, vedere `az network dns zone create -h`.

L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Per creare una zona DNS con tag

L'esempio seguente illustra come creare una zona DNS con due [tag di Azure Resource Manager](dns-zones-records.md#tags), *project = demo* ed *env = test*, usando il parametro `--tags` (forma breve `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Ottenere una zona DNS

Per recuperare una zona DNS, usare `az network dns zone show`. Per altre informazioni, vedere `az network dns zone show --help`.

L'esempio seguente restituisce la zona DNS *contoso.com* e i relativi dati associati dal gruppo di risorse *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

L'esempio seguente corrisponde alla risposta.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Si noti che i record DNS non vengono restituiti da `az network dns zone show`. Per elencare i record DNS, usare `az network dns record-set list`.


## <a name="list-dns-zones"></a>Elencare le zone DNS

Per enumerare le zone DNS, usare `az network dns zone list`. Per altre informazioni, vedere `az network dns zone list --help`.

Se si specifica il gruppo di risorse, vengono elencate solo le zone all'interno del gruppo di risorse:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Se invece il gruppo di risorse viene omesso, sono elencate tutte le zone nella sottoscrizione:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Aggiornare una zona DNS

È possibile apportare modifiche a una risorsa di zona DNS usando `az network dns zone update`. Per altre informazioni, vedere `az network dns zone update --help`.

Questo comando non consente di aggiornare alcun set di record DNS compreso nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets-cli.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Queste proprietà sono attualmente limitate ai ["tag" di Azure Resource Manager](dns-zones-records.md#tags) relativi alla risorsa di zona.

L'esempio seguente illustra come aggiornare i tag in una zona DNS. I tag esistenti vengono sostituiti dal valore specificato.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Eliminare una zona DNS

Le zone DNS possono essere eliminate usando `az network dns zone delete`. Per altre informazioni, vedere `az network dns zone delete --help`.

> [!NOTE]
> L'eliminazione di una zona DNS comporta anche l'eliminazione di tutti i record DNS all'interno della zona. Questa operazione non può essere annullata. Se la zona DNS è in uso, i servizi che la usano rileveranno un errore quando la zona viene eliminata.
>
>Per evitare l'eliminazione accidentale di una zona, vedere [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Come proteggere le zone e i record DNS).

Questo comando richiede una conferma. Lo switch facoltativo `--yes` elimina questa richiesta.

L'esempio seguente mostra come eliminare la zona *contoso.com* dal gruppo di risorse *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [gestire record e set di record](dns-getstarted-create-recordset-cli.md) nella zona DNS.

Informazioni su come [delegare il dominio al servizio DNS di Azure](dns-domain-delegation.md).

