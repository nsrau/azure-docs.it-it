---
title: Gestire le zone DNS in DNS Azure - Portale di Azure | Microsoft Docs
description: "È possibile gestire le zone DNS usando il portale di Azure. Questo articolo illustra come aggiornare, eliminare e creare le zone DNS in DNS di Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 69a509612e6204fc93dd42bf2fe69cb165b5777c
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Come gestire le zone DNS nel portale di Azure

> [!div class="op_single_selector"]
> * [Portale](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-operations-dnszones-cli.md)

Questo articolo spiega come gestire le zone DNS usando il portale di Azure. È anche possibile gestire le zone DNS usando l'[interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md) multipiattaforma o Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Creare una zona DNS

1. Accedere al portale di Azure
2. Nel menu Hub fare clic su **Nuovo > Rete >** e quindi fare clic su **Zona DNS** per aprire il pannello per creare una zona DNS.

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. Nel pannello **Crea zona DNS** immettere i valori seguenti, quindi fare clic su **Crea**:


   | **Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Nome**|contoso.com|Nome della zona DNS|
   |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione in cui creare la zona DNS.|
   |**Gruppo di risorse**|**Crea nuovo:** contosoDNSRG|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. Per altre informazioni sui gruppi di risorse, vedere l'articolo [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Posizione**|Stati Uniti occidentali||

> [!NOTE]
> Il gruppo di risorse indica la località del gruppo di risorse e non ha alcun impatto sulla zona DNS. La posizione della zona DNS è sempre "globale" e non viene visualizzata.

## <a name="list-dns-zones"></a>Elencare le zone DNS

Nel portale di Azure passare a **Altri servizi** > **Rete** > **Zone DNS**. Ogni zona DNS è una risorsa a sé e in questa vista sono disponibili informazioni quali il numero di set di record e i server dei nomi. La colonna **SERVER DEI NOMI** non è presente nella visualizzazione predefinita. Per aggiungerla, fare clic su **Colonne**, selezionare **Server dei nomi** e quindi fare clic su **Fine**.

![elenco delle zone DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Eliminare una zona DNS

Passare a una zona DNS nel portale. Nel pannello **Zona DNS** fare clic su **Elimina zona**. Verrà chiesto di confermare l'intenzione di eliminare la zona DNS. L'eliminazione di una zona DNS comporta anche l'eliminazione di tutti i record contenuti nella zona.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare la zona e i record DNS, vedere [Introduzione a DNS Azure con il portale di Azure](dns-getstarted-portal.md).
