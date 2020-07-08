---
title: Dati di Advisor in Azure Resource Graph
description: Eseguire query per i dati di Advisor in Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: c0786d1d09ff61ddd9c375c68b7199521e319a4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85117835"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Eseguire una query per i dati di Advisor in Resource Graph Explorer (grafico delle risorse di Azure)

Le risorse di Advisor vengono ora caricate in [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Questo costituisce la base per molti scenari di clienti su larga scala per le raccomandazioni di Advisor. Pochi scenari che non erano possibili prima di eseguire su larga scala e che ora possono essere realizzati usando il grafico risorse sono:
* Offre la possibilità di eseguire query complesse per tutte le sottoscrizioni in portale di Azure
* Indicazioni riepilogate in base ai tipi di categoria (ad esempio affidabilità, prestazioni) e ai tipi di effetto (alta, media, bassa)
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
* [Introduzione ad Advisor](advisor-get-started.md)
* [Raccomandazioni sui costi di Advisor](advisor-cost-recommendations.md)
* [Consigli sull'affidabilità di Advisor](advisor-high-availability-recommendations.md)
* [Raccomandazioni sulle prestazioni di Advisor](advisor-performance-recommendations.md)
* [Raccomandazioni sulla sicurezza di Advisor](advisor-security-recommendations.md)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
* [API REST Advisor](https://docs.microsoft.com/rest/api/advisor/)
