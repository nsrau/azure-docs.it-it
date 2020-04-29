---
title: Dati di Advisor nel grafico delle risorse di Azure
description: Eseguire query per i dati di Advisor in Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502451"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Eseguire una query per i dati di Advisor in Resource Graph Explorer (grafico delle risorse di Azure)

Le risorse di Advisor vengono ora caricate in [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Questo costituisce la base per molti scenari di clienti su larga scala per le raccomandazioni di Advisor. Pochi scenari che non erano possibili prima di eseguire su larga scala e che ora possono essere realizzati usando il grafico risorse sono:
* Offre la possibilità di eseguire query complesse per tutte le sottoscrizioni in portale di Azure
* Indicazioni riepilogate in base ai tipi di categoria (ad esempio disponibilità elevata, prestazioni) e tipi di effetto (alta, media, bassa)
* Tutti i consigli per un particolare tipo di raccomandazione
* Conteggio risorse interessate per categoria raccomandazione

![Advisor in Azure Resource Graph Explorer](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Tipi di risorse Advisor in Azure Graph

Tipi di risorse Advisor disponibili nel [grafico risorse](https://docs.microsoft.com/azure/governance/resource-graph/): sono disponibili tre tipi di risorse per l'esecuzione di query in risorse Advisor. Di seguito è riportato l'elenco delle risorse ora disponibili per l'esecuzione di query nel grafico risorse.
* Microsoft. Advisor/configurazioni
* Microsoft. Advisor/raccomandazioni
* Microsoft. Advisor/evitamenti

Questi tipi di risorse sono elencati in una nuova tabella denominata AdvisorResources, in cui è anche possibile eseguire query in Resource Graph Explorer in portale di Azure.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione a Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
* [API REST Advisor](https://docs.microsoft.com/rest/api/advisor/)
