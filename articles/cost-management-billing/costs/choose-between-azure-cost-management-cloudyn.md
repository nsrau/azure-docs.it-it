---
title: Scegliere tra Gestione costi di Azure e Cloudyn
description: Questo articolo consente di stabilire quale sia la soluzione di gestione dei costi più adatta in base alle esigenze, se Gestione costi di Azure o Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684967"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Scegliere tra Gestione costi di Azure e Cloudyn

Il servizio Cloudyn sarà deprecato entro la fine del 2020. Le funzionalità di Cloudyn esistenti vengono integrate direttamente nel portale di Azure laddove possibile. Ad eccezione dei clienti CSP, al momento non verrà eseguito l'onboarding di nuovi clienti. Il supporto per il prodotto esistente rimarrà disponibile fino a quando non sarà completamente deprecato.

Microsoft ha acquisito Cloudyn e sta eseguendo la migrazione delle relative funzionalità di gestione dei costi dal portale di Cloudyn in modalità nativa in Azure. Per usare le nuove funzionalità, accedere al portale di Azure e passare a [Gestione costi e fatturazione](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) nell'elenco di servizi di Azure. Rispetto a Cloudyn, l'esperienza nativa offre prestazioni più elevate e una latenza dei dati ridotta di circa otto ore.

La migrazione delle funzionalità chiave per le categorie di offerte relative a contratto Enterprise, pagamento a consumo e MSDN a Gestione costi di Azure è stata completata. La migrazione delle sottoscrizioni CSP a Gestione costi di Azure è ancora in corso.

Per le categorie di offerte non ancora trasferite, è necessario continuare a usare il portale Cloudyn. In tutti gli altri casi, è possibile usare Gestione costi di Azure.

## <a name="recommended-services-by-offer"></a>Servizi consigliati per offerta

| Offerte di Microsoft Azure | Servizio di gestione dei costi consigliato |
| --- | --- |
| Contratto Enterprise di Azure | [Gestione costi di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (pagamento a consumo/MSDN) | [Gestione costi di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Gestione costi di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Contratto del cliente Microsoft | [Gestione costi di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Contratto del cliente Microsoft supportato dai partner | [Gestione costi di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| CSP di Azure | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>Funzionalità di Gestione costi disponibili

Alcune funzionalità seguenti sono disponibili in Cloudyn, ma non tutte sono già disponibili in Gestione costi di Azure.

- API
- Raccomandazioni per l'ottimizzazione dei costi di Azure, tra cui:
    - Raccomandazioni per il corretto dimensionamento e l'arresto dell'istanza di Azure
    - Raccomandazioni per le prenotazioni di Azure
- Budget
- Analisi dei costi
- Esportare i dati in un account di archiviazione di Azure
- Latenza inferiore
- App modello di Power BI
- Supporto dei tag delle risorse
- Supporto per l'analisi dei costi tra cloud per AWS

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Gestione costi di Azure](../cost-management-billing-overview.md).