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
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388461"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Trasferimento di risorse Network Watcher di Azure tra aree

## <a name="moving-the-network-watcher-resource"></a>Trasferimento della risorsa Network Watcher
La risorsa Network Watcher rappresenta il servizio back-end per Network Watcher ed è completamente gestita da Azure. I clienti non devono gestirlo. L'operazione di spostamento non è supportata in questa risorsa.

## <a name="moving-child-resources-of-network-watcher"></a>Trasferimento di risorse figlio di Network Watcher
Lo stato di trasferimento delle risorse tra aree non è attualmente supportato per nessuna risorsa figlio del `*networkWatcher*` tipo di risorsa.

## <a name="next-steps"></a>Passaggi successivi
* Leggi la [Panoramica di Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)
* Vedere le [domande frequenti sulla Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions)
