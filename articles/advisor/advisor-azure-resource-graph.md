---
title: Dati dell'advisor in Azure Resource GraphAdvisor data in Azure Resource Graph
description: Eseguire query per i dati di Advisor in Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502451"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Query for Advisor data in Resource Graph Explorer (Azure Resource Graph)

Le risorse dell'advisor sono ora incartone in [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Questo pone le basi per molti scenari di clienti su larga scala per consigli di Advisor. Pochi scenari che non erano possibili prima da eseguire su larga scala e ora possono essere raggiunti utilizzando Resource Graph sono:Few scenarios that were not possible before to do at scale and now can be achieved using Resource Graph are:
* Offre la possibilità di eseguire query complesse per tutte le sottoscrizioni nel portale di AzureGives capability to perform complex query for all your subscriptions in Azure portal
* Raccomandazioni riepilogate per tipi di categoria (ad esempio disponibilità elevata, prestazioni) e tipi di impatto (alta, media, bassa)
* Tutte le raccomandazioni per un particolare tipo di raccomandazione
* Conteggio delle risorse interessate per categoria di raccomandazione

![Advisor in Azure Resource Graph Explorer (Esplora diagramma risorse di Azure)](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Advisor resource types in Azure Graph

Tipi di risorse Advisor disponibili in [Diagramma risorse:](https://docs.microsoft.com/azure/governance/resource-graph/)sono disponibili 3 tipi di risorse per l'esecuzione di query in Risorse Advisor.Available Advisor resource types in Resource Graph : There are 3 resource types available for querying under Advisor resources. Ecco l'elenco delle risorse che sono ora disponibili per l'esecuzione di query in Resource Graph.
* Microsoft.Advisor/configurations
* Microsoft.Advisor/raccomandazioni
* Microsoft.Advisor/soppressioni

Questi tipi di risorse sono elencati in una nuova tabella denominata AdvisorResources, che è anche possibile eseguire query in Resource Graph Explorer nel portale di Azure.These resource types are listed under a new table named as AdvisorResources, which you can also query in the Resource Graph Explorer in Azure portal.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione a Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Consigli per l'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
