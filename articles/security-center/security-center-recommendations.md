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
ms.openlocfilehash: 6363100c844d071a3bb47521cec6ff7e988f6af8
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263222"
---
# <a name="security-recommendations-in-azure-security-center"></a>Raccomandazioni di sicurezza nel Centro sicurezza di Azure 
Questo argomento illustra come visualizzare e comprendere le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse di Azure.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>

## <a name="what-are-security-recommendations"></a>Informazioni sulle raccomandazioni di sicurezza

Le raccomandazioni sono azioni da eseguire per proteggere le risorse.

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Vengono quindi fornite indicazioni su come correggere tali vulnerabilità.

Ogni raccomandazione fornisce:

- Breve descrizione del problema.
- Procedura di correzione da eseguire per implementare la raccomandazione.
- Risorse interessate.

## <a name="monitor-recommendations"></a>Monitorare le raccomandazioni <a name="monitor-recommendations"></a>

Il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Il riquadro **raccomandazioni** in **Panoramica** Mostra il numero totale di raccomandazioni identificate dal centro sicurezza.

![Panoramica del Centro sicurezza](./media/security-center-recommendations/asc-overview.png)

1. Selezionare il **riquadro raccomandazioni** in **Panoramica**. Verrà visualizzato l'elenco **raccomandazioni** .

1. I consigli sono raggruppati in controlli di sicurezza.

      ![Raccomandazioni raggruppate per controllo di sicurezza](./media/security-center-recommendations/view-recommendations.png)

1. Espandere un controllo e selezionare una raccomandazione specifica per visualizzare la pagina di raccomandazione.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Pagina dei dettagli della raccomandazione." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    La pagina include:

    - **Indicatore di gravità**
    - **Intervallo di aggiornamento**  (se pertinente) 
    - **Descrizione** : breve descrizione del problema
    - **Passaggi correttivi** -Descrizione dei passaggi manuali necessari per correggere il problema di sicurezza sulle risorse interessate. Per consigli con la correzione rapida, è possibile selezionare **Visualizza la logica di monitoraggio e aggiornamento** prima di applicare la correzione consigliata alle risorse. 
    - **Risorse interessate** : le risorse sono raggruppate in schede:
        - **Risorse integre** : risorse rilevanti che non sono interessate o per le quali il problema è già stato risolto.
        - **Risorse non integre** : risorse ancora interessate dal problema identificato.
        - **Risorse non applicabili** : risorse per le quali la raccomandazione non può fornire una risposta definitiva. La scheda non applicabile include anche i motivi di ogni risorsa. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Risorse non applicabili con motivi.":::


 
## <a name="next-steps"></a>Passaggi successivi

Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per informazioni su come correggere le raccomandazioni:

* [Correggere le raccomandazioni](security-center-remediate-recommendations.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
