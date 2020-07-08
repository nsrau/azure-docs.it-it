---
title: Risolvere gli errori di distribuzione comuni
titleSuffix: Azure Load Balancer
description: Viene descritto come risolvere gli errori che si verificano più frequentemente quando si distribuiscono i servizi di bilanciamento del carico di Azure
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 527f71b1980b5a62d3db94fe89a1bce98142e31a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221004"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Risolvere errori comuni durante la distribuzione di Azure con Azure Load Balancer

Questo argomento descrive alcuni errori comuni che possono verificarsi durante la distribuzione di Azure Load Balancer e fornisce indicazioni su come risolverli. Se si cercano informazioni su un codice di errore non riportato in questo articolo, è possibile segnalarlo lasciando un feedback nella parte inferiore della pagina. Il feedback viene registrato insieme ai problemi di GitHub.

## <a name="error-codes"></a>Codici di errore

| Codice di errore | Dettagli e mitigazione |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Lo SKU dell'indirizzo IP pubblico e lo SKU di Load Balancer devono corrispondere. Verificare che lo SKU di Load Balancer e quello dell'indirizzo IP pubblico corrispondano. Il tipo di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Altre informazioni sulle [differenze negli SKU](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Per impostazione predefinita, i set di scalabilità di macchine virtuali vengono impostati sui servizi di bilanciamento del carico Basic quando lo SKU non è specificato o viene distribuito senza IP pubblici standard. Ridistribuire il set di scalabilità di macchine virtuali con indirizzi IP pubblici standard nelle singole istanze per assicurarsi che venga selezionato Load Balancer Standard o selezionare semplicemente Load Balancer Standard quando si distribuisce un set di scalabilità di macchine virtuali dal portale di Azure. |
|MaxAvailabilitySetsInLoadBalancerReached | Il pool back-end di un servizio di bilanciamento del carico può contenere un massimo di 150 set di disponibilità. Se nel pool back-end non sono definiti set di disponibilità in modo esplicito per le VM, ogni singola VM viene inserita nel proprio set di disponibilità. La distribuzione di 150 macchine virtuali autonome comporterebbe quindi 150 set di disponibilità, raggiungendo così il limite. Come soluzione alternativa, è possibile distribuire un set di disponibilità e aggiungervi altre macchine virtuali. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | Per il servizio di bilanciamento del carico SKU Basic, l'interfaccia di rete e il servizio di bilanciamento del carico devono trovarsi nello stesso set di disponibilità. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Non può esistere più di una regola per un determinato tipo di bilanciamento del carico (interno, pubblico) con la stessa porta back-end e lo stesso protocollo a cui fa riferimento lo stesso set di scalabilità di macchine virtuali. Aggiornare la regola per modificare la creazione della regola duplicata. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Non può esistere più di una regola per un determinato tipo di bilanciamento del carico (interno, pubblico) con la stessa porta back-end e lo stesso protocollo a cui fa riferimento lo stesso set di scalabilità di macchine virtuali. Aggiornare i parametri della regola per modificare la creazione della regola duplicata. |
|AnotherInternalLoadBalancerExists| Può esistere un solo servizio di bilanciamento del carico di tipo interno che fa riferimento allo stesso set di VM/interfacce di rete nel back-end del servizio di bilanciamento del carico. Aggiornare la distribuzione per assicurarsi di creare un solo bilanciamento del carico dello stesso tipo. |
|CannotUseInactiveHealthProbe| Non può esistere un probe che non venga usato da nessuna regola configurata per l'integrità del set di scalabilità di macchine virtuali. Assicurarsi che il probe configurato venga usato attivamente. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Non possono esistere più servizi di bilanciamento del carico dello stesso tipo (interno, pubblico). Si può avere un massimo di un bilanciamento del carico interno e uno pubblico. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Load Balancer Basic non è supportato per i set di scalabilità di macchine virtuali con più gruppi di selezione host o i set di scalabilità di macchine virtuali in diverse zone di disponibilità. Usare Load Balancer Standard. |
|MarketplacePurchaseEligibilityFailed | Passare all'account amministrativo corretto per abilitare gli acquisti in una sottoscrizione con contratto Enterprise. Per altre informazioni, leggere [qui](https://docs.microsoft.com/azure/marketplace/marketplace-faq-publisher-guide#what-could-block-a-customer-from-completing-a-purchase). |
|ResourceDeploymentFailure| Se il servizio di bilanciamento del carico è in stato di errore, seguire questa procedura per ripristinarlo:<ol><li>Passare a https://resources.azure.com e accedere con le credenziali del portale di Azure.</li><li>Selezionare **Lettura/Scrittura**.</li><li>A sinistra espandere **Sottoscrizioni** e quindi espandere la sottoscrizione con il servizio di bilanciamento del carico da aggiornare.</li><li>Espandere **Gruppi di risorse** e quindi espandere il gruppo di risorse con il servizio di bilanciamento del carico da aggiornare.</li><li>Selezionare **Microsoft.Network** > **LoadBalancers** e quindi selezionare il servizio di bilanciamento del carico da aggiornare, **LoadBalancer_1**.</li><li>Nella pagina di **LoadBalancer_1** selezionare **GET** > **Modifica**.</li><li>Aggiornare il valore di **ProvisioningState** da **Non riuscito** a **Riuscito**.</li><li>Selezionare **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Passaggi successivi

* Vedere la [tabella di confronto tra gli SKU](./skus.md) di Azure Load Balancer
* Informazioni sui [limiti di Azure Load Balancer](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
