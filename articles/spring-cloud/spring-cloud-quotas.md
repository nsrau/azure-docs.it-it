---
title: Piani di servizio e quote per il cloud Spring di Azure
description: Informazioni sulle quote di servizio e sui piani di servizio per Azure Spring cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 0518b13ea228b4834a095a9bf126b131e70a5f45
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851554"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quote e piani di servizio per Azure Spring cloud

Tutti i servizi di Azure impostano i limiti e le quote predefiniti per le risorse e le funzionalità.  Durante il periodo di anteprima, Azure Spring cloud offre un solo piano di servizio.

Questo articolo descrive in dettaglio le quote del servizio offerte durante il periodo di anteprima corrente.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Quote e livelli di servizio cloud della primavera di Azure

Durante il periodo di anteprima, Azure Spring cloud offre un solo livello di servizio.

Gruppi | Importo
------- | -------
vCPU | 4 per istanza del servizio
Memoria | 8 GByte per istanza di servizio
Istanze del servizio Azure Spring Cloud per area e per sottoscrizione | 10
Numero totale di istanze di app per ogni istanza del servizio Azure Spring Cloud | 500
Totale istanze app per applicazione Spring | 20
Volumi permanenti | 10 x 50 GByte

Quando si raggiunge una quota, viene visualizzato un errore 400 che indica che la quota supera il limite per la sottoscrizione della *sottoscrizione* nell'area geografica in *cui è stato creato il servizio cloud Spring di Azure*.

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Se la risorsa richiede un aumento, [creare una richiesta di supporto](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
