---
title: Gestire le zone DNS in DNS Azure - Portale di Azure | Microsoft Docs
description: È possibile gestire le zone DNS usando il portale di Azure. Questo articolo illustra come aggiornare, eliminare e creare le zone DNS in DNS di Azure
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 51e17812e1116f1e625685d5a818c18e25cdb2da
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965800"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Come gestire le zone DNS nel portale di Azure

> [!div class="op_single_selector"]
> * [Portale](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [INTERFACCIA della riga di comando di Azure classico](./dns-operations-dnszones-cli.md)
> * [Interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md)

Questo articolo spiega come gestire le zone DNS usando il portale di Azure. È anche possibile gestire le zone DNS usando l'[interfaccia della riga di comando di Azure](dns-operations-dnszones-cli.md) multipiattaforma o Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Creare una zona DNS

1. Accedere al portale di Azure
2. Nel menu Hub passare a **Crea una risorsa > Rete > Zona DNS** per aprire il pannello **Crea zona DNS**.

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. Nel pannello **Crea zona DNS** immettere i valori seguenti, quindi fare clic su **Crea**:


   | **Impostazione** | **Valore** | **Informazioni dettagliate** |
   |---|---|---|
   |**Nome**|contoso.com|Nome della zona DNS|
   |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione in cui creare la zona DNS.|
   |**Gruppo di risorse**|**Crea nuovo:** contosoDNSRG|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. Per altre informazioni sui gruppi di risorse, vedere l'articolo Panoramica di [Gestione risorse](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) .|
   |**Posizione**|Stati Uniti occidentali||

> [!NOTE]
> Il gruppo di risorse indica la località del gruppo di risorse e non ha alcun impatto sulla zona DNS. La posizione della zona DNS è sempre "globale" e non viene visualizzata.

## <a name="list-dns-zones"></a>Elencare le zone DNS

Nella portale di Azure passare ad **altri servizi**  >  **rete**  >  **DNS zone**. Ogni zona DNS è una risorsa a sé e in questa vista sono disponibili informazioni quali il numero di set di record e i server dei nomi. La colonna **SERVER DEI NOMI** non è inclusa nella vista predefinita. Per aggiungerla, fare clic su **Colonne**, selezionare **Server dei nomi** e fare clic su **Fine**.

![elenco delle zone DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Eliminare una zona DNS

Passare a una zona DNS nel portale. Nel pannello **Zona DNS** fare clic su **Elimina zona**. Verrà chiesto di confermare l'intenzione di eliminare la zona DNS. L'eliminazione di una zona DNS comporta anche l'eliminazione di tutti i record contenuti nella zona.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare la zona e i record DNS, vedere [Introduzione a DNS Azure con il portale di Azure](dns-getstarted-portal.md).