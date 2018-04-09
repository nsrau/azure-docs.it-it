---
title: Introduzione alle Zone private di DNS di Azure con l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come creare una zona DNS privata e un record in DNS Azure. Si tratta di una guida dettagliata per creare e gestire la prima zona privata e il primo record DNS usando l'interfaccia della riga di comando di Azure 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Introduzione alle Zone private di DNS di Azure con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Interfaccia della riga di comando di Azure 2.0](private-dns-getstarted-cli.md)

Questo articolo illustra i passaggi per creare la prima zona privata e il primo record DNS usando l'interfaccia della riga di comando di Azure 2.0 tra piattaforme, disponibile per Windows, Mac e Linux. È anche possibile eseguire questi passaggi tramite Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare una zona DNS privata nella rete virtuale, specificare l'elenco di reti virtuali autorizzate a risolvere i record nella zona.  Tali reti vengono dette "reti virtuali di risoluzione".  È anche possibile specificare una rete virtuale per cui Azure DNS manterrà i record dei nomi host ogni volta che una macchina virtuale viene creata o eliminata definitivamente oppure ne viene modificato l'indirizzo IP.  Tale rete viene definita "rete virtuale di registrazione".

Queste istruzioni presuppongono che l'installazione e l'accesso all'interfaccia della riga di comando di Azure 2.0 siano già stati completati e che sia stata installata anche l'estensione necessaria dell'interfaccia della riga di comando che supporta le zone private. Per informazioni, vedere [Come gestire le zone DNS usando l'interfaccia della riga di comando di Azure 2.0](dns-operations-dnszones-cli.md).

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Per installare/usare la funzionalità Zone private di DNS di Azure (anteprima pubblica)
La funzionalità Zone private di DNS di Azure viene rilasciata in anteprima pubblica tramite un'estensione dell'interfaccia della riga di comando di Azure. Installare l'estensione "dns" dell'interfaccia della riga di comando di Azure 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima di creare la zona DNS, viene creato un gruppo di risorse per contenere la zona DNS. Di seguito è riportato il comando.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Creare una zona DNS privata

Una zona DNS privata viene creata usando il comando `az network dns zone create`. Per visualizzare la guida per questo comando, digitare `az network dns zone create --help`.

L'esempio seguente crea una zona DNS privata denominata *contoso.local* nel gruppo di risorse *MyResourceGroup* e la rende disponibile, ovvero la collega, alla rete virtuale *MyAzureVnet* tramite il parametro resolution-vnets. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

Nota: nell'esempio precedente la rete virtuale "MyAzureVnet" appartiene allo stesso gruppo di risorse e alla stessa sottoscrizione della zona privata. Se è necessario collegare una rete virtuale appartenente a un gruppo di risorse o a una sottoscrizione diversa, occorre specificare l'ID completo di Azure Resource Manager invece del semplice nome di rete virtuale per il parametro --resolution-vnets. 

Se è necessario che Azure crei automaticamente i record dei nomi host nella zona, usare il parametro *registration-vnets* invece di *resolution-vnets*.  Le reti virtuali di registrazione sono abilitate automaticamente per la risoluzione.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>Creare un record DNS

Per creare un record DNS, usare il comando `az network dns record-set [record type] add-record`. Per informazioni, ad esempio per i record A, vedere `azure network dns record-set A add-record --help`.

L'esempio seguente crea un record con il nome relativo "ip1" nella zona DNS "contoso.local" nel gruppo di risorse "MyResourceGroup". Il nome completo del set di record è "ip1.contoso.local". Il tipo di record è "A", con indirizzo IP "10.0.0.1".

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Per altri tipi di record, per set di record con più di un record, per valori TTL alternativi e per modificare i record esistenti, vedere [Gestire record e set di record DNS con l'interfaccia della riga di comando di Azure 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Visualizzare i record

Per elencare i record DNS nella zona, usare:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Ottenere una zona DNS privata

Per recuperare una zona DNS privata, usare `az network dns zone show`. Per altre informazioni, vedere `az network dns zone show --help`.

L'esempio seguente restituisce la zona DNS *contoso.local* e i relativi dati associati dal gruppo di risorse *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

L'esempio seguente corrisponde alla risposta.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
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

Questo comando non consente di aggiornare alcun set di record DNS compreso nella zona (vedere [Come gestire i record DNS](dns-operations-recordsets-cli.md)). Questa operazione permette solo di aggiornare le proprietà della risorsa di zona stessa. Per le zone private è possibile aggiornare le reti virtuali di registrazione o di risoluzione collegate a una zona. 

L'esempio seguente mostra come aggiornare la rete virtuale di risoluzione collegata a una zona DNS privata. La rete virtuale di risoluzione collegata esistente viene sostituita dalla nuova rete virtuale specificata.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>Eliminare una zona DNS

Le zone DNS possono essere eliminate usando `az network dns zone delete`. Per altre informazioni, vedere `az network dns zone delete --help`.

> [!NOTE]
> L'eliminazione di una zona DNS comporta anche l'eliminazione di tutti i record DNS all'interno della zona. Questa operazione non può essere annullata. Se la zona DNS è in uso, i servizi che la usano rileveranno un errore quando la zona viene eliminata.
>
>Per evitare l'eliminazione accidentale di una zona, vedere [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Come proteggere le zone e i record DNS).

Questo comando richiede una conferma. Lo switch facoltativo `--yes` elimina questa richiesta.

L'esempio seguente mostra come eliminare la zona *contoso.local* dal gruppo di risorse *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Eliminare tutte le risorse
 
Per eliminare tutte le risorse create in questo articolo, seguire questa procedura:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio DNS Azure, vedere [Panoramica di DNS Azure](dns-overview.md).

Per altre informazioni sulla gestione delle zone DNS in DNS Azure, vedere [Gestire le zone DNS in DNS Azure usando l'interfaccia della riga di comando di Azure 2.0](dns-operations-dnszones-cli.md).

Per altre informazioni sulla gestione dei record DNS in DNS Azure, vedere [Gestire i record e i set di record DNS in DNS Azure usando l'interfaccia della riga di comando di Azure 2.0](dns-operations-recordsets-cli.md).
