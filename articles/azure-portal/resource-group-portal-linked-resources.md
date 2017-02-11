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
ms.sourcegitcommit: ebc5dbf790ca6012cfe9a7ea9ccee9fdacb46ffd
ms.openlocfilehash: b4c8ad69674c553f8b521a85765edbad0ce1faf2


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Risorse correlate e collegate nella raccolta dei riquadri
La raccolta dei riquadri consente di trovare riquadri per una particolare risorsa e trascinarli nel pannello corrente. Utilizzando la raccolta dei riquadri è possibile creare viste di gestione estese alle varie risorse. Per qualsiasi risorsa specificata, le risorse correlate includono tutte le risorse che condividono lo stesso gruppo di risorse come risorsa e tutte le risorse che si collegano a o da tale risorsa.

## <a name="linked-resources-in-azure-resource-manager"></a>Risorse collegate in Gestione risorse di Azure
Il collegamento è una funzionalità di Gestione risorse di Azure.  Consente di dichiarare le relazioni tra le risorse anche se non si trovano nello stesso gruppo di risorse. Il collegamento non ha alcun impatto sul runtime delle risorse, sulla fatturazione e sull'accesso in base al ruolo.  È semplicemente un meccanismo che è possibile usare per rappresentare le relazioni in modo che gli strumenti come la raccolta dei riquadri possano offrire un'esperienza di gestione completa.  Gli strumenti possono controllare i collegamenti mediante l'API per i collegamenti e fornire allo stesso tempo esperienze personalizzate per la gestione delle relazioni. 

## <a name="how-do-i-link-my-resources"></a>Come si collegano le risorse?
Quando si creano risorse tramite il portale o tramite la distribuzione di un modello tramite Azure PowerShell o l'interfaccia della riga di comando di Azure, vengono creati automaticamente collegamenti per alcune risorse dipendenti. È inoltre possibile collegare le risorse a livello di programmazione tramite l' [API REST Risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx) o dichiarando le relazioni nel modello. Per informazioni dettagliate sull'uso delle risorse collegate, vedere [Collegamento di risorse in Gestione risorse di Azure](../azure-resource-manager/resource-group-link-resources.md).

## <a name="next-steps"></a>Passaggi successivi
* Se è necessaria un'introduzione alla scrittura di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](../azure-resource-manager/resource-group-authoring-templates.md).
* Per ulteriori dettagli sulla creazione di collegamenti tra le risorse, vedere [Collegamento di risorse in Gestione risorse di Azure](../azure-resource-manager/resource-group-link-resources.md).
* Per ulteriori informazioni sull'uso dei gruppi di risorse tramite il portale di anteprima, vedere [Uso del portale di anteprima di Azure per gestire le risorse di Azure](../azure-resource-manager/resource-group-portal.md).




<!--HONumber=Feb17_HO2-->


