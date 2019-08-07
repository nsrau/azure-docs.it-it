---
title: Correggere le raccomandazioni nel centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come correggere le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse di Azure e rimanere in conformità con i criteri di sicurezza.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779005"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Correggere le raccomandazioni nel centro sicurezza di Azure

Le raccomandazioni forniscono suggerimenti su come proteggere meglio le risorse.  Per implementare una raccomandazione, seguire la procedura di correzione fornita nella raccomandazione. 

## Procedura di correzione<a name="remediation-steps"></a>

Dopo aver esaminato tutte le raccomandazioni, decidere quale risolvere prima di tutto. Si consiglia di usare l' [effetto del Punteggio sicuro](security-center-recommendations.md#monitor-recommendations) per definire la priorità delle operazioni da eseguire prima.

1. Nell'elenco fare clic sull'indicazione.
1. Seguire le istruzioni riportate nella sezione relativa alla **procedura di correzione** . Ogni raccomandazione dispone di un proprio set di istruzioni. Di seguito vengono illustrati i passaggi correttivi per la configurazione delle applicazioni in modo da consentire solo il traffico su HTTPS.

    ![Dettagli raccomandazione](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Al termine, viene visualizzata una notifica che informa se la correzione ha avuto esito positivo.

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come correggere le raccomandazioni nel centro sicurezza. Per ulteriori informazioni sul centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
