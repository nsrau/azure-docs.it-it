---
title: Come aumentare la concorrenza di un servizio Web di Azure Machine Learning | Documentazione Microsoft
description: Informazioni su come aumentare la concorrenza di un servizio Web di Azure Machine Learning aggiungendo altri endpoint.
services: machine-learning
documentationcenter: ''
author: aashishb
manager: srikants
editor: cgronlun
keywords: azure machine learning, servizi Web, messa in funzione, scalabilità, endpoint, concorrenza
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: aashishb
ms.openlocfilehash: bec69a273a9996782376196b193686ab8cfe1eaa
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Ridimensionamento di un servizio Web di Azure Machine Learning con l'aggiunta di altri endpoint
> [!NOTE]
> Questo argomento descrive le tecniche applicabili a un servizio Web di Machine Learning **classico**. 
> 
> 

Per impostazione predefinita, ogni servizio Web pubblicato è configurato per supportare 20 richieste simultanee e può gestire fino a 200 richieste simultanee. Azure Machine Learning ottimizza automaticamente questa impostazione per offrire le prestazioni migliori per il servizio Web, ignorando il valore del portale. 

Se si prevede di chiamare l'API con un carico superiore rispetto al valore di 200 per il numero massimo di chiamate simultanee supportate, è consigliabile creare più endpoint nello stesso servizio Web. È quindi possibile distribuire casualmente il carico tra tutti gli endpoint.

Il ridimensionamento di un servizio Web è un'attività comune. Alcuni motivi per il ridimensionamento consistono nella necessità di supportare più di 200 richieste simultanee, aumentare la disponibilità tramite più endpoint o fornire endpoint separati per il servizio Web. È possibile aumentare la scalabilità mediante l'aggiunta di altri endpoint per lo stesso servizio Web tramite il portale del [servizio Web di Azure Machine Learning](https://services.azureml.net/).

Per altre informazioni sull'aggiunta di nuovi endpoint, vedere [Creazione di endpoint](create-endpoint.md).

Tenere presente che l'uso di un numero elevato di chiamate simultanee può essere dannoso se non si chiama l'API con una frequenza altrettanto elevata. Se si inserisce un carico relativamente basso in un'API configurata per un carico elevato, è possibile che si verifichino timeout sporadici e/o picchi della latenza.

Le API sincrone in genere vengono usate nelle situazioni in cui si desidera una bassa latenza. La latenza qui indica il tempo impiegato dall'API per completare una richiesta e non tiene in considerazione i ritardi di rete. Si supponga di avere un'API con una latenza di 50 ms. Per utilizzare appieno la capacità disponibile con il livello di limitazione Elevato e il numero massimo di chiamate simultanee pari a 20, è necessario chiamare l'API 20 * 1000 / 50 = 400 volte al secondo. Estendendo ulteriormente questa capacità, un numero massimo di 200 chiamate simultanee permetterà di chiamare l'API 4000 volte al secondo, supponendo una latenza di 50 ms.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
