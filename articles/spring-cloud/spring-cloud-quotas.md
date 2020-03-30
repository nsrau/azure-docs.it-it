---
title: Piani di servizio e quote per Azure Spring CloudService plans and quotas for Azure Spring Cloud
description: Informazioni sulle quote di servizio e sui piani di servizio per Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278882"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quote e piani di servizio per Azure Spring Cloud

Tutti i servizi di Azure impostano limiti e quote predefiniti per risorse e funzionalità.  Durante il periodo di anteprima, Azure Spring Cloud offre un solo piano di servizio.

Questo articolo descrive in dettaglio le quote di servizio offerte durante il periodo di anteprima corrente.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Livelli e quote del servizio Azure Spring CloudAzure Spring Cloud service tiers and quotas

Durante il periodo di anteprima, Azure Spring Cloud offre un solo livello di servizio.

Risorsa | Amount
------- | -------
vCPU | 4 per istanza del servizio
Memoria | 8 GByte per istanza del servizio
Istanze del servizio Azure Spring Cloud per area e per sottoscrizione | 10
Numero totale di istanze di app per ogni istanza del servizio Azure Spring Cloud | 500
Totale istanze dell'app per applicazione Spring | 20
Volumi permanenti | 10 x 50 GByte

Quando si raggiunge una quota, verrà visualizzato un errore 400 con la dì: "Quota supera il limite per *la sottoscrizione* della sottoscrizione nell'area geografica in cui viene creato il servizio Cloud di Origine *di Azure.*

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Se la risorsa richiede un aumento, [creare una richiesta](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)di supporto .
