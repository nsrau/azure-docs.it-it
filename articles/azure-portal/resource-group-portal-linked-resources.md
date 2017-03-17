---
title: Risorse correlate e collegate nella raccolta dei riquadri
description: Informazioni sulle risorse correlate e collegate visualizzate nella raccolta dei riquadri del portale di anteprima di Azure.
services: azure-portal
documentationcenter: 
author: adamabdelhamed
manager: wpickett
editor: 
ms.assetid: dba96d29-f518-49c8-bfd2-f14cecb44cbf
ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: efa7bce26c2c4c153b083e0e34d689a11d27dd16
ms.lasthandoff: 03/06/2017


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Risorse correlate e collegate nella raccolta dei riquadri
La raccolta dei riquadri consente di trovare riquadri per una particolare risorsa e trascinarli nel pannello corrente. Utilizzando la raccolta dei riquadri è possibile creare viste di gestione estese alle varie risorse. Per qualsiasi risorsa specificata, le risorse correlate includono tutte le risorse nel relativo gruppo di risorse e tutte le risorse che si collegano a o da tale risorsa.

## <a name="linked-resources-in-resource-manager"></a>Risorse collegate in Resource Manager
Il collegamento è una funzionalità di Resource Manager.  Consente di dichiarare le relazioni tra le risorse anche se non si trovano nello stesso gruppo di risorse. Il collegamento non ha alcun impatto sul runtime delle risorse, sulla fatturazione e sull'accesso in base al ruolo.  È semplicemente un meccanismo che è possibile usare per rappresentare le relazioni in modo che gli strumenti come la raccolta dei riquadri possano offrire un'esperienza di gestione completa.  Gli strumenti possono controllare i collegamenti mediante l'API per i collegamenti e fornire allo stesso tempo esperienze personalizzate per la gestione delle relazioni. 

## <a name="how-do-i-link-my-resources"></a>Come si collegano le risorse?
Quando si creano risorse tramite il portale o tramite la distribuzione di un modello tramite Azure PowerShell o l'interfaccia della riga di comando di Azure, vengono creati automaticamente collegamenti per alcune risorse dipendenti. È inoltre possibile collegare le risorse a livello di programmazione tramite l' [API REST Risorse collegate](/rest/api/resources/resourcelinks).

## <a name="next-steps"></a>Passaggi successivi
* Se è necessaria un'introduzione alla scrittura dei modelli di Resource Manager, vedere [Creazione di modelli](../azure-resource-manager/resource-group-authoring-templates.md).
* Per ulteriori informazioni sull'uso dei gruppi di risorse tramite il portale, vedere [Uso del Portale di Azure per gestire le risorse di Azure](../azure-resource-manager/resource-group-portal.md).


