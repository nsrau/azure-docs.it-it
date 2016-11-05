---
title: Risorse correlate e collegate nella raccolta dei riquadri
description: Informazioni sulle risorse correlate e collegate visualizzate nella raccolta dei riquadri del portale di anteprima di Azure.
services: azure-portal
documentationcenter: ''
author: adamabdelhamed
manager: wpickett
editor: ''

ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab

---
# Risorse correlate e collegate nella raccolta dei riquadri
La raccolta dei riquadri consente di trovare riquadri per una particolare risorsa e trascinarli nel pannello corrente. Utilizzando la raccolta dei riquadri è possibile creare viste di gestione estese alle varie risorse. Per qualsiasi risorsa specificata, le risorse correlate includono tutte le risorse che condividono lo stesso gruppo di risorse come risorsa e tutte le risorse che si collegano a o da tale risorsa.

## Risorse collegate in Gestione risorse di Azure
Il collegamento è una funzionalità di Gestione risorse di Azure. Consente di dichiarare le relazioni tra le risorse anche se non si trovano nello stesso gruppo di risorse. Il collegamento non ha alcun impatto sul runtime delle risorse, sulla fatturazione e sull'accesso in base al ruolo. È semplicemente un meccanismo che è possibile usare per rappresentare le relazioni in modo che gli strumenti come la raccolta dei riquadri possano offrire un'esperienza di gestione completa. Gli strumenti possono controllare i collegamenti mediante l'API per i collegamenti e fornire allo stesso tempo esperienze personalizzate per la gestione delle relazioni.

## Come si collegano le risorse?
Quando si creano risorse tramite il portale o tramite la distribuzione di un modello tramite Azure PowerShell o l'interfaccia della riga di comando di Azure, vengono creati automaticamente collegamenti per alcune risorse dipendenti. È inoltre possibile collegare le risorse a livello di programmazione tramite l'[API REST Risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx) o dichiarando le relazioni nel modello. Per informazioni dettagliate sull'uso delle risorse collegate, vedere [Collegamento di risorse in Gestione risorse di Azure](../resource-group-link-resources.md).

## Passaggi successivi
* Se è necessaria un'introduzione alla scrittura di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](../resource-group-authoring-templates.md).
* Per ulteriori dettagli sulla creazione di collegamenti tra le risorse, vedere [Collegamento di risorse in Gestione risorse di Azure](../resource-group-link-resources.md).
* Per ulteriori informazioni sull'uso dei gruppi di risorse tramite il portale di anteprima, vedere [Uso del portale di anteprima di Azure per gestire le risorse di Azure](resource-group-portal.md).

<!---HONumber=Oct15_HO3-->