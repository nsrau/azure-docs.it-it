---
title: Scelta della capacità per la distribuzione di QnA Maker - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Guida alla scelta della capacità per la distribuzione di QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b0219b9f7dbbee52406dab9d808134fa2e2a689d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376313"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Scelta della capacità per la distribuzione di QnA Maker

Il servizio QnA Maker stabilisce una dipendenza da tre risorse di Azure:
1.  Servizio app (per il runtime)
2.  Ricerca di Azure (per l'archiviazione di domande e risposte)
3.  Application Insights (facoltativo, per archiviare log di chat e dati di telemetria)

Prima di creare il servizio QnA Maker, è necessario stabilire quali livelli dei servizi precedenti soddisfano le proprie esigenze. 

Ci sono generalmente tre parametri da considerare:
1. **La velocità effettiva che il servizio deve fornire**: scegliere il [piano app](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) appropriato per il servizio app in base alle proprie esigenze. È possibile [aumentare](https://docs.microsoft.com/en-us/azure/app-service/web-sites-scale) o ridurre le prestazioni dell'app. Ciò influenzerà anche la scelta dello SKU di Ricerca di Azure. Per altri dettagli, vedere [qui](https://docs.microsoft.com/en-us/azure/search/search-sku-tier).

2. **Dimensioni e numero di Knowledge Base**: scegliere lo [SKU di Ricerca di Azure](https://azure.microsoft.com/en-in/pricing/details/search/) appropriato per lo scenario. È possibile pubblicare N-1 Knowledge Base in un particolare livello, dove N è il numero massimo di indici consentiti nel livello. Verificare anche le dimensioni massime e il numero di documenti consentiti per ogni livello.

3. **Numero di documenti come origini**: lo SKU gratuito del servizio di gestione di QnA Maker limita a 3 (1 MB ciascuno) il numero di documenti gestibili tramite il portale e le API. Lo SKU Standard non pone limiti al numero di documenti gestibili. Per informazioni dettagliate, vedere [qui](https://aka.ms/qnamaker-pricing).

La tabella seguente indica alcune linee guida generali.

|                        | Gestione di QnA Maker | Servizio app | Ricerca di Azure | Limitazioni                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Sperimentazione        | SKU gratuito             | Livello gratuito   | Livello gratuito    | Pubblicazione di massimo 2 Knowledge Base, dimensioni 50 MB  |
| Ambiente di sviluppo/test   | SKU Standard         | Condiviso      | Basic        | Pubblicazione di massimo 4 Knowledge Base, dimensioni 2 GB    |
| Ambiente di produzione | SKU Standard         | Basic       | Standard     | Pubblicazione di massimo 49 Knowledge Base, dimensioni 25 GB |

Per l'aggiornamento dello stack di QnA Maker, vedere [Aggiornare il servizio QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiornare il servizio QnA Maker](../How-To/upgrade-qnamaker-service.md)
