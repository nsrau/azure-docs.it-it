---
title: Piani di servizio e quote per il cloud Spring di Azure
description: Informazioni sulle quote di servizio e sui piani di servizio per Azure Spring cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607677"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quote e piani di servizio per Azure Spring cloud

Tutti i servizi di Azure impostano i limiti e le quote predefiniti per le risorse e le funzionalità.  Durante il periodo di anteprima, Azure Spring cloud offre un solo piano di servizio.

Questo articolo descrive in dettaglio le quote del servizio offerte durante il periodo di anteprima corrente.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Quote e livelli di servizio cloud della primavera di Azure

Durante il periodo di anteprima, Azure Spring cloud offre un solo livello di servizio.

Gruppi | Amount
------- | -------
vCPU | 4
Memoria | 8 Gbyte
Sottoscrizione di Azure Spring cloud | 1
Istanze del servizio cloud Spring di Azure per area per sottoscrizione | 2
Totale istanze app per ogni istanza di servizio cloud di Azure Spring | 50
Totale istanze app per applicazione Spring | 20
Volumi permanenti | 10 x 50 Gbyte

Quando si raggiunge una quota, viene visualizzato un errore 400 che indica che la quota supera il limite per la sottoscrizione della *sottoscrizione* nell'area geografica in *cui è stato creato il servizio cloud Spring di Azure*.

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Se la risorsa richiede un aumento, [creare una richiesta di supporto](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
