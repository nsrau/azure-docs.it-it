---
title: Risolvere gli errori di distribuzione comuni
titleSuffix: Azure Load Balancer
description: Viene descritto come risolvere gli errori comuni quando si distribuiscono i bilanciamento del carico di Azure
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 26b4232832589827a09774eb0106c25e5c446eb1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791086"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Risolvere gli errori comuni di distribuzione di Azure con Azure Load Balancer

Questo articolo descrive alcuni errori di distribuzione di Azure Load Balancer comuni e fornisce informazioni per risolvere gli errori. Se si stanno cercando informazioni su un codice di errore e tali informazioni non sono disponibili in questo articolo, è possibile segnalarle. Nella parte inferiore della pagina è possibile lasciare il feedback. Il feedback viene registrato con i problemi di GitHub.

## <a name="error-codes"></a>Codici di errore

| Codice errore | Dettagli e mitigazione |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Lo SKU IP pubblico e lo SKU Load Balancer devono corrispondere. Verificare che Azure Load Balancer e gli SKU IP pubblici corrispondano. Lo SKU standard è consigliato per i carichi di lavoro di produzione. Altre informazioni sulle [differenze negli SKU](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Per impostazione predefinita, i set di scalabilità di macchine virtuali vengono impostati sui bilanciamenti del carico Basic quando lo SKU non è specificato o viene distribuito senza IP Ridistribuire il set di scalabilità di macchine virtuali con indirizzi IP pubblici standard nelle singole istanze per assicurarsi che Load Balancer Standard sia selezionato o semplicemente selezionare una LB standard quando si distribuisce un set di scalabilità di macchine virtuali dal portale di Azure. |
|MaxAvailabilitySetsInLoadBalancerReached | Il pool back-end di un Load Balancer può contenere un massimo di 150 set di disponibilità. Se non sono stati definiti in modo esplicito set di disponibilità per le macchine virtuali nel pool back-end, ogni singola macchina virtuale entra nel proprio set di disponibilità. Quindi, la distribuzione di macchine virtuali autonome 150 comporterebbe la disponibilità di 150 set di disponibilità, raggiungendo così il limite. È possibile distribuire un set di disponibilità e aggiungervi altre macchine virtuali come soluzione alternativa. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Non è possibile avere più di una regola per un determinato tipo di servizio di bilanciamento del carico (interno, pubblico) con la stessa porta back-end e il protocollo a cui fa riferimento lo stesso set di scalabilità di macchine virtuali. Aggiornare la regola per modificare la creazione della regola duplicata. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Non è possibile avere più di una regola per un determinato tipo di servizio di bilanciamento del carico (interno, pubblico) con la stessa porta back-end e il protocollo a cui fa riferimento lo stesso set di scalabilità di macchine virtuali. Aggiornare i parametri della regola per modificare la creazione della regola duplicata. |
|AnotherInternalLoadBalancerExists| Nel back-end della Load Balancer è possibile avere solo un Load Balancer di tipo Internal Reference lo stesso set di macchine virtuali o interfacce di rete. Aggiornare la distribuzione per assicurarsi di creare solo un Load Balancer dello stesso tipo. |
|CannotUseInactiveHealthProbe| Non è possibile avere un probe non usato da nessuna regola configurata per l'integrità del set di scalabilità di macchine virtuali. Verificare che il probe configurato venga usato attivamente. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Non è possibile avere più bilanciamenti del carico dello stesso tipo (interno, pubblico). È possibile avere un massimo di un Load Balancer interno e uno pubblico. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | La Load Balancer di base non è supportata per i set di scalabilità di macchine virtuali con più host o per i set di scalabilità di macchine virtuali. In alternativa, usare Load Balancer Standard. |
|ResourceDeploymentFailure| Se il servizio di bilanciamento del carico si trova in uno stato di errore, attenersi alla procedura seguente per ripristinare lo stato di errore:<ol><li>Passare a https://resources.azure.come accedere con le credenziali portale di Azure.</li><li>Selezionare **lettura/scrittura**.</li><li>A sinistra espandere **sottoscrizioni**, quindi espandere la sottoscrizione con il Load Balancer da aggiornare.</li><li>Espandere **ResourceGroups**, quindi espandere il gruppo di risorse con il Load Balancer da aggiornare.</li><li>Selezionare **Microsoft. Network** > **LoadBalancers**, quindi selezionare la Load Balancer da aggiornare, **LoadBalancer_1**.</li><li>Nella pagina di visualizzazione per **LoadBalancer_1**selezionare **Get** > **Edit (modifica**).</li><li>Aggiornare il valore di **ProvisioningState** da **failed** a **succeeded**.</li><li>Selezionare **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Passaggi successivi

* Esaminare la tabella di [confronto SKU](./skus.md) Azure Load Balancer
* Informazioni sui [limiti di Azure Load Balancer](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
