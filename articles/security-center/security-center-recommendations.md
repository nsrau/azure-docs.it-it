---
title: Raccomandazioni di sicurezza nel Centro sicurezza di Azure
description: Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245304"
---
# <a name="security-recommendations-in-azure-security-center"></a>Raccomandazioni di sicurezza nel Centro sicurezza di Azure 
Questo argomento illustra come visualizzare e comprendere i consigli nel Centro sicurezza di Azure per proteggere le risorse di Azure.This topic explains how to view and understand the recommendations in Azure Security Center to help you protect your Azure resources.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>

## <a name="what-are-security-recommendations"></a>Informazioni sulle raccomandazioni di sicurezza

Le raccomandazioni sono azioni da intraprendere per proteggere le risorse.

Centro sicurezza analizzando periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità della sicurezza. Fornisce quindi consigli su come rimuoverli.

Ogni raccomandazione fornisce:

- Breve descrizione di ciò che viene raccomandato.
- Le procedure di correzione da eseguire per implementare la raccomandazione. <!-- In some cases, Quick Fix remediation is available. -->
- Quali risorse hanno bisogno di eseguire l'azione consigliata su di esse.
- **L'impatto del punteggio sicuro**, ovvero l'importo che verrà aumentato se si implementa questa raccomandazione.

## <a name="monitor-recommendations"></a>Monitorare le raccomandazioni<a name="monitor-recommendations"></a>

Il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare potenziali vulnerabilità. Il riquadro **Consigli** in **Panoramica** mostra il numero totale di consigli identificati dal Centro sicurezza.

![Panoramica del Centro sicurezza](./media/security-center-recommendations/asc-overview.png)

1. Selezionare il **riquadro Consigli** in **Panoramica**. Viene visualizzato l'elenco **Raccomandazioni.**

      ![Visualizzare raccomandazioni](./media/security-center-recommendations/view-recommendations.png)

    È possibile filtrare le raccomandazioni. Per filtrare le raccomandazioni, selezionare **Filtro** nel pannello **Raccomandazioni**. Viene visualizzato il pannello **Filtro** in cui è possibile selezionare i valori relativi a gravità e stato da visualizzare.

   * **RECOMMENDATIONS**: La raccomandazione.
   * **SECURE SCORE IMPACT**: Punteggio generato dal Centro sicurezza utilizzando le raccomandazioni di sicurezza e applicando algoritmi avanzati per determinare quanto sia cruciale ogni raccomandazione. Per ulteriori informazioni, consultate [Calcolo del punteggio di sicurezza.](security-center-secure-score.md#secure-score-calculation)
   * **RISORSA**: elenca le risorse a cui si applica questa raccomandazione.
   * **STATUS BARS**: Descrive la gravità di quella particolare raccomandazione:
       * **Alta (rossa):** esiste una vulnerabilità con una risorsa significativa (ad esempio un'applicazione, una macchina virtuale o un gruppo di sicurezza di rete) e richiede attenzione.
       * **Medio (arancione):** esiste una vulnerabilità e sono necessari passaggi non critici o aggiuntivi per eliminarla o completare un processo.
       * **Basso (blu)**: Esiste una vulnerabilità che dovrebbe essere affrontata ma non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente. 
       * **Integro (verde)**:
       * **Non disponibile (grigio)**:

1. Per visualizzare i dettagli di ogni raccomandazione, fai clic sul suggerimento.

    ![Dettagli delle raccomandazioni](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Vedere [Modelli di distribuzione classici e di Resource Manager](../azure-classic-rm.md) per le risorse di Azure.See classic and Resource Manager deployment models for Azure resources.
 
## <a name="next-steps"></a>Passaggi successivi

Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per informazioni su come risolvere i suggerimenti:

* Correggere i suggerimenti: informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di [Azure.Remediate recommendations:](security-center-remediate-recommendations.md) Learn how to configure security policies for your Azure subscriptions and resource groups.
