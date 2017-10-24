---
title: Gestione delle soluzioni dei partner nel Centro sicurezza di Azure | Microsoft Docs
description: "Questo documento descrive in modo dettagliato il modo in cui il Centro sicurezza di Azure permette di monitorare in modo immediato lo stato di integrità delle soluzioni dei partner integrate nella sottoscrizione di Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: 3ff38892f198ab5dfb9e08c1c01b942810a40260
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure
Questo documento contiene informazioni dettagliate su come monitorare lo stato di integrità delle soluzioni dei partner nel Centro sicurezza di Azure.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio. Questo argomento non costituisce una guida dettagliata.
>
>

## <a name="monitoring-partner-solutions"></a>Monitoraggio delle soluzioni dei partner
Il riquadro **Soluzioni di sicurezza** in **Panoramica** permette di monitorare in modo immediato lo stato di integrità delle soluzioni dei partner integrate nella sottoscrizione di Azure.

![Riquadro Soluzioni partner][1]

Per visualizzare l'integrità delle soluzioni dei partner:

1. Selezionare il riquadro **Soluzioni di sicurezza**. Viene visualizzato **Soluzioni di sicurezza** contenente un elenco di soluzioni dei partner connesse al Centro sicurezza.

   ![Soluzioni partner][3]

   Una soluzione partner può avere uno degli stati seguenti:

   * Protetto (verde): nessun problema di integrità.
   * Danneggiato (rosso): è presente un problema di integrità che richiede attenzione immediata.
   * Segnalazione arrestata (arancione): la soluzione ha arrestato la segnalazione dello stato di integrità.
   * Sconosciuto (arancione): l'integrità della soluzione è attualmente sconosciuta, a causa di un processo di aggiunta di una nuova risorsa alla soluzione esistente non riuscito.
   * Non segnalato (grigio): la soluzione non ha ancora inviato alcuna segnalazione. Lo stato di una soluzione può non essere segnalato se la soluzione è stata connessa recentemente ed è ancora in fase di distribuzione.

2. Selezionare una soluzione dei partner. Viene visualizzato un pannello che mostra che lo stato della soluzione partner e le risorse associate alla soluzione. Selezionare **Console della soluzione** per aprire l'esperienza di gestione dei partner per questa soluzione.

   ![Dettagli della soluzione di un partner][4]
3. Tornare al pannello **Qualys** e selezionare **Collega la macchina virtuale**. Verrà visualizzato il pannello **Collega applicazioni** . In questo pannello è possibile connettere risorse alla soluzione del partner.

   ![Collegare risorse alla soluzione di un partner][5]

## <a name="next-steps"></a>Passaggi successivi
Questo documento ha presentato il riquadro **Soluzioni di sicurezza** del Centro sicurezza. Per altre informazioni sull'integrazione con i partner per migliorare la sicurezza complessiva, vedere [Integrazione di partner e soluzioni](security-center-partner-integration.md).

Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
