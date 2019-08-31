---
title: 'Capacità risorsa per la distribuzione: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Prima di creare il servizio QnA Maker, è necessario stabilire quale livello dei servizi precedenti soddisfa le proprie esigenze.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2d8f0fce3cb8f1cd8fdb596cb4e238a79d6cee4c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193493"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Scelta della capacità per la distribuzione di QnA Maker

Il servizio QnA Maker stabilisce una dipendenza da tre risorse di Azure:
1.  Servizio app (per il runtime)
2.  Ricerca di Azure (per l'archiviazione e la ricerca di QnAs)
3.  App Insights (facoltativo, per archiviare log di chat e dati di telemetria)

Prima di creare il servizio QnA Maker, è necessario stabilire quale livello dei servizi precedenti soddisfa le proprie esigenze. 

Ci sono generalmente tre parametri da considerare:

1. **La velocità effettiva che il servizio deve fornire**: scegliere il [piano app](https://azure.microsoft.com/pricing/details/app-service/plans/) appropriato per il servizio app in base alle proprie esigenze. È possibile [aumentare](https://docs.microsoft.com/azure/app-service/manage-scale-up) o ridurre le prestazioni dell'app. Ciò influenzerà anche la scelta dello SKU di Ricerca di Azure. Per altri dettagli, vedere [qui](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Dimensioni e numero di Knowledge Base:** : scegliere lo [SKU di Ricerca di Azure](https://azure.microsoft.com/pricing/details/search/) appropriato per lo scenario. È possibile pubblicare N-1 Knowledge Base in un particolare livello, dove N è il numero massimo di indici consentiti nel livello. Verificare anche le dimensioni massime e il numero di documenti consentiti per ogni livello.

    Ad esempio, se il livello include 15 indici consentiti, è possibile pubblicare 14 articoli della knowledge base (1 indice per ogni articolo della knowledge base pubblicato). Il quindicesimo indice viene usato per tutti gli articoli della knowledge base per la creazione e il testing. 

1. **Numero di documenti come origini**: lo SKU gratuito del servizio di gestione di QnA Maker limita a 3 (1 MB ciascuno) il numero di documenti gestibili tramite il portale e le API. Lo SKU Standard non pone limiti al numero di documenti gestibili. Per informazioni dettagliate, vedere [qui](https://aka.ms/qnamaker-pricing).

La tabella seguente indica alcune linee guida generali.

|                        | Gestione di QnA Maker | Servizio app | Ricerca di Azure | Limitazioni                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Sperimentazione        | SKU gratuito             | Livello gratuito   | Livello gratuito    | Pubblicazione di massimo 2 Knowledge Base, dimensioni 50 MB  |
| Ambiente di sviluppo/test   | SKU Standard         | Condiviso      | Basic        | Pubblicazione di massimo 14 Knowledge Base, dimensioni 2 GB    |
| Ambiente di produzione | SKU Standard         | Basic       | Standard     | Pubblicazione di massimo 49 Knowledge Base, dimensioni 25 GB |

Per l'aggiornamento dello stack di QnA Maker, vedere [Aggiornare il servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiornare il servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
