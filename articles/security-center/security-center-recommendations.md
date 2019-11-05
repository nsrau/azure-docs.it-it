---
title: Raccomandazioni per la sicurezza nel centro sicurezza di Azure | Microsoft Docs
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
ms.openlocfilehash: 6a01ddbc733ce52c02e9abef2569adbfbd19c6a8
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520742"
---
# <a name="security-recommendations-in-azure-security-center"></a>Raccomandazioni di sicurezza nel Centro sicurezza di Azure 
Questo argomento illustra come visualizzare e comprendere le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse di Azure.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>

## <a name="what-are-security-recommendations"></a>Informazioni sulle raccomandazioni di sicurezza

Le raccomandazioni sono azioni da eseguire per proteggere le risorse.

Centro sicurezza che analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Vengono quindi fornite indicazioni su come rimuoverle.

Ogni raccomandazione fornisce:

- Descrizione breve degli elementi consigliati.
- Procedura di correzione da eseguire per implementare la raccomandazione. <!-- In some cases, Quick Fix remediation is available. -->
- Quali risorse sono necessarie per eseguire l'azione consigliata su di essi.
- L' **effetto del Punteggio sicuro**, ovvero la quantità di tempo per cui il Punteggio sicuro aumenterà se si implementa questa raccomandazione.

## Monitorare le raccomandazioni<a name="monitor-recommendations"></a>

Il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Il riquadro **raccomandazioni** in **Panoramica** Mostra il numero totale di raccomandazioni identificate dal centro sicurezza.

![Panoramica del Centro sicurezza](./media/security-center-recommendations/asc-overview.png)

1. Selezionare il **riquadro raccomandazioni** in **Panoramica**. Verrà visualizzato l'elenco **raccomandazioni** .

      ![Visualizzare raccomandazioni](./media/security-center-recommendations/view-recommendations.png)

    È possibile filtrare le raccomandazioni. Per filtrare le raccomandazioni, selezionare **Filtro** nel pannello **Raccomandazioni**. Viene visualizzato il pannello **Filtro** in cui è possibile selezionare i valori relativi a gravità e stato da visualizzare.

   * **Raccomandazioni**: raccomandazione.
   * Un punteggio **sicuro**: un punteggio generato dal centro sicurezza usando le raccomandazioni per la sicurezza e l'applicazione di algoritmi avanzati per determinare il livello di importanza di ogni raccomandazione. Per altre informazioni, vedere [calcolo del Punteggio sicuro](security-center-secure-score.md#secure-score-calculation).
   * **RISORSA**: elenca le risorse a cui si applica questa raccomandazione.
   * **Barre di stato**: descrive il livello di gravità di tale raccomandazione particolare:
       * **Alta (rossa)** : esiste una vulnerabilità con una risorsa significativa, ad esempio un'applicazione, una macchina virtuale o un gruppo di sicurezza di rete, che richiede attenzione.
       * **Media (arancione)** : esiste una vulnerabilità e sono necessari passaggi aggiuntivi o non critici per eliminarla o per completare un processo.
       * **Bassa (blu)** : esiste una vulnerabilità che deve essere risolta, ma non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente. 
       * **Integro (verde)** :
       * **Non disponibile (grigio)** :

1. Per visualizzare i dettagli di ogni raccomandazione, fare clic sull'indicazione.

    ![Dettagli delle raccomandazioni](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Vedere [modelli di distribuzione classica e gestione risorse](../azure-classic-rm.md) per le risorse di Azure.
 
## <a name="next-steps"></a>Passaggi successivi

Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per informazioni su come correggere le raccomandazioni:

* [Correggere le raccomandazioni](security-center-remediate-recommendations.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
