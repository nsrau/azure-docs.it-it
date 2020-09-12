---
title: Correggere le raccomandazioni nel centro sicurezza di Azure | Microsoft Docs
description: Questo articolo illustra come correggere le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse e conformarsi ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 4bad3227e08c0fbe0d280967e45bbef9d477e1b3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569136"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Correzione delle raccomandazioni nel Centro sicurezza di Azure

Le raccomandazioni forniscono suggerimenti su come proteggere meglio le risorse. Per implementare una raccomandazione, seguire la procedura di correzione fornita nella raccomandazione.

## <a name="remediation-steps"></a>Procedura di correzione <a name="remediation-steps"></a>

Dopo aver esaminato tutte le raccomandazioni, decidere quale risolvere prima di tutto. Si consiglia di usare l' [effetto del Punteggio sicuro](security-center-recommendations.md#monitor-recommendations) per definire la priorità delle operazioni da eseguire prima.

1. Nell'elenco fare clic sull'indicazione.

1. Seguire le istruzioni riportate nella sezione relativa alla **procedura di correzione** . Ogni raccomandazione dispone di un proprio set di istruzioni. Nella schermata seguente vengono illustrati i passaggi correttivi per la configurazione delle applicazioni in modo da consentire solo il traffico su HTTPS.

    ![Dettagli delle raccomandazioni](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Al termine, viene visualizzata una notifica che informa se la correzione ha avuto esito positivo.

## <a name="quick-fix-remediation"></a>Correzione rapida<a name="one-click"></a>

La correzione rapida consente di correggere rapidamente una raccomandazione su più risorse. È disponibile solo per raccomandazioni specifiche. La correzione rapida semplifica la correzione e consente di aumentare rapidamente il Punteggio sicuro, migliorando la sicurezza dell'ambiente.

Per implementare il monitoraggio e l'aggiornamento della correzione rapida:

1. Dall'elenco di raccomandazioni con la **correzione rapida.** etichetta, fare clic sull'indicazione.

    [![Selezionare correzione rapida.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Dalla scheda **risorse non integre** selezionare le risorse su cui si desidera implementare l'indicazione e **quindi fare clic su Correggi**.

    > [!NOTE]
    > Alcune delle risorse elencate potrebbero essere disabilitate, perché non si dispone delle autorizzazioni appropriate per modificarle.

1. Nella casella Conferma leggere i dettagli e le implicazioni per la correzione.

    ![Correzione rapida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Le implicazioni sono elencate nella casella grigio della finestra Correggi **risorse** che viene visualizzata dopo aver **fatto clic su**Correggi. Elencano le modifiche che si verificano quando si procede con la correzione rapida.

1. Inserire i parametri rilevanti, se necessario, e approvare la correzione.

    > [!NOTE]
    > Potrebbero essere necessari alcuni minuti dopo il completamento della correzione per visualizzare le risorse nella scheda **risorse integre** . Per visualizzare le azioni correttive, controllare il [log attività](#activity-log).

1. Al termine, viene visualizzata una notifica che informa se la correzione ha avuto esito positivo.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Registrazione correzione rapida nel log attività <a name="activity-log"></a>

Per applicare la configurazione nella risorsa, l'operazione di monitoraggio e aggiornamento usa una chiamata API per la distribuzione di modelli o una PATCH REST. Queste operazioni vengono registrate nel [log attività di Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come correggere le raccomandazioni nel centro sicurezza. Per ulteriori informazioni sul centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
