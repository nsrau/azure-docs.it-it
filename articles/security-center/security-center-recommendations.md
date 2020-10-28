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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: c0952e0f267f9c2511a2e80fa7106958d8dd0ee5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791886"
---
# <a name="security-recommendations-in-azure-security-center"></a>Raccomandazioni di sicurezza nel Centro sicurezza di Azure 
Questo argomento illustra come visualizzare e comprendere le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse di Azure.


## <a name="what-are-security-recommendations"></a>Informazioni sulle raccomandazioni di sicurezza

Le raccomandazioni sono azioni da eseguire per proteggere le risorse.

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità di sicurezza. Fornisce quindi raccomandazioni su come correggere tali vulnerabilità.

Ogni raccomandazione fornisce:

- Breve descrizione del problema
- Procedura di correzione da eseguire per implementare la raccomandazione
- Risorse interessate

## <a name="monitor-recommendations"></a>Monitorare le raccomandazioni <a name="monitor-recommendations"></a>

Il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. 

1. Dal menu del Centro sicurezza aprire la pagina **raccomandazioni** per visualizzare le raccomandazioni applicabili all'ambiente in uso. I consigli sono raggruppati in controlli di sicurezza.

      ![Raccomandazioni raggruppate per controllo di sicurezza](./media/security-center-recommendations/view-recommendations.png)

1. Espandere un controllo e selezionare una raccomandazione specifica per visualizzare la pagina dei dettagli della raccomandazione.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Pagina dei dettagli della raccomandazione." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    La pagina include:

    1. **Applicare** e **negare** i pulsanti sulle raccomandazioni supportate (vedere Impedisci configurazioni non configurate [con le raccomandazioni Imponi/nega](prevent-misconfigurations.md))
    1. **Indicatore di gravità**
    1. **Intervallo di aggiornamento**  (se pertinente) 
    1. **Descrizione** : breve descrizione del problema
    1. **Passaggi correttivi** -Descrizione dei passaggi manuali necessari per correggere il problema di sicurezza sulle risorse interessate. Per consigli con la correzione rapida, è possibile selezionare **Visualizza la logica di monitoraggio e aggiornamento** prima di applicare la correzione consigliata alle risorse. 
    1. **Risorse interessate** : le risorse sono raggruppate in schede:
        - **Risorse integre** : risorse rilevanti che non sono interessate o per le quali il problema è già stato risolto.
        - **Risorse non integre** : risorse ancora interessate dal problema identificato.
        - **Risorse non applicabili** : risorse per le quali la raccomandazione non può fornire una risposta definitiva. La scheda non applicabile include anche i motivi di ogni risorsa. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Pagina dei dettagli della raccomandazione.":::
    1. Pulsanti di azione per correggere la raccomandazione o attivare un'app per la logica.

## <a name="preview-recommendations"></a>Suggerimenti per l'anteprima

Le raccomandazioni contrassegnate come **Anteprima** non sono incluse nei calcoli del Punteggio sicuro.

È comunque necessario correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima.

Esempio di una raccomandazione in anteprima:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Pagina dei dettagli della raccomandazione.":::
 
## <a name="next-steps"></a>Passaggi successivi

Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per informazioni correlate:

- [Correggere le raccomandazioni](security-center-remediate-recommendations.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Impedisci configurazioni errate con le raccomandazioni Imponi/nega](prevent-misconfigurations.md).
- [Automatizzare le risposte ai trigger del Centro sicurezza](workflow-automation.md): automatizzare le risposte alle raccomandazioni
- [Esentare una risorsa da una raccomandazione](exempt-resource.md)
- [Raccomandazioni sulla sicurezza: una guida di riferimento](recommendations-reference.md)