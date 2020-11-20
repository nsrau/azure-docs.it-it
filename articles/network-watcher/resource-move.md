---
title: Spostare le risorse di Azure Network Watcher | Microsoft Docs
description: Spostare le risorse Network Watcher di Azure tra le aree
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 84764e73ec5b4ada8c204147def310326a3c7bdd
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948426"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Trasferimento di risorse Network Watcher di Azure tra aree

## <a name="moving-the-network-watcher-resource"></a>Trasferimento della risorsa Network Watcher
La risorsa Network Watcher rappresenta il servizio back-end per Network Watcher ed è completamente gestita da Azure. I clienti non devono gestirlo. L'operazione di spostamento non è supportata in questa risorsa.

## <a name="moving-child-resources-of-network-watcher"></a>Trasferimento di risorse figlio di Network Watcher
Lo stato di trasferimento delle risorse tra aree non è attualmente supportato per nessuna risorsa figlio del `*networkWatcher*` tipo di risorsa.

## <a name="next-steps"></a>Passaggi successivi
* Leggi la [Panoramica di Network Watcher](./network-watcher-monitoring-overview.md)
* Vedere le [domande frequenti sulla Network Watcher](./frequently-asked-questions.md)