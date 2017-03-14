---
title: Introduzione alla topologia di Azure Network Watcher | Documentazione Microsoft
description: "Questa pagina fornisce una panoramica delle funzionalità di topologia di Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 3e7595baa26ba9eebfcb8a2fd5c2744d9c0fbfcb
ms.openlocfilehash: 42443f614b76b8180ac163b9889163021adbf048
ms.lasthandoff: 02/23/2017

---

# <a name="introduction-to-topology-in-azure-network-watcher"></a>Introduzione alla topologia di Azure Network Watcher

La topologia restituisce un grafico delle risorse di rete in una rete virtuale. Il grafico illustra l'interconnessione tra le risorse per rappresentare in modo completo la connettività di rete.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

![Panoramica della topologia][1]

Nel portale la topologia restituisce gli oggetti risorsa per ogni singola rete virtuale. Le relazioni sono rappresentate dalle linee tra le risorse all'esterno dell'area di Network Watcher, anche se non vengono visualizzate nel gruppo di risorse. Le risorse restituite nella vista del portale sono un sottoinsieme di componenti di rete rappresentati in un grafico. Per visualizzare l'elenco completo delle risorse di rete è possibile usare [PowerShell](network-watcher-topology-powershell.md) o [REST](network-watcher-topology-rest.md).

> [!NOTE]
> È necessaria un'istanza di Network Watcher in ogni area in cui si desidera eseguire la topologia.

Quando le risorse vengono restituite, la connessione tra di esse viene modellati in due relazioni.

- **Contenimento** - Esempio: la rete virtuale contiene una subnet che contiene una scheda di interfaccia di rete.
- **Associazione** - Esempio: una scheda NIC è associata a una macchina virtuale

### <a name="next-steps"></a>Passaggi successivi

Le informazioni su come usare PowerShell per recuperare la vista della topologia sono disponibili in [Network Watcher topology with PowerShell](network-watcher-topology-powershell.md) (Topologia di Network Watcher con PowerShell)

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png

