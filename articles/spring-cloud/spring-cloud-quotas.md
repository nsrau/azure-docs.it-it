---
title: Piani di servizio e quote per il cloud Spring di Azure
description: Informazioni sulle quote di servizio e sui piani di servizio per Azure Spring cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cda22c63a70c5121e6a6972c66bdc0a4bb5158fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089465"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quote e piani di servizio per Azure Spring cloud

Tutti i servizi di Azure impostano i limiti e le quote predefiniti per le risorse e le funzionalità.   Azure Spring cloud offre due piani tariffari: Basic e standard. In questo articolo vengono illustrati i limiti dettagliati per entrambi i livelli.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Livelli di servizio e limiti di Azure Spring cloud

| Risorsa | Basic | Standard
------- | ------- | -------
vCPU | 1 per istanza del servizio | 4 per istanza del servizio
Memory | 2 GB per istanza del servizio | 8 GB per istanza del servizio
Istanze del servizio Azure Spring Cloud per area e per sottoscrizione | 10 | 10
Numero totale di istanze di app per ogni istanza del servizio Azure Spring Cloud | 25 | 500
Volumi permanenti | app da 1 GB/app x 10 | App 50 GB/app x 10


Durante il periodo di anteprima, Azure Spring cloud offre un solo livello di servizio. Quando si raggiunge un limite, si riceverà un errore 400 che indica che la quota supera il limite per *la* sottoscrizione della sottoscrizione nell' *area geografica in cui viene creato il servizio cloud Spring di Azure*.

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare i limiti predefiniti. Se l'installazione richiede un aumento, [creare una richiesta di supporto](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
