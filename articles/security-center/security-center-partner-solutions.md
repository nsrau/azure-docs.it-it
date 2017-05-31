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
ms.date: 05/09/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5ffc3253cf07018ad6fe9f0112c75c2e1495e5df
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure
Questo documento contiene informazioni dettagliate su come monitorare lo stato di integrità delle soluzioni dei partner nel Centro sicurezza di Azure.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.
>
>

## <a name="monitoring-partner-solutions"></a>Monitoraggio delle soluzioni dei partner
Il riquadro **Soluzioni partner** nel pannello **Centro sicurezza** permette di monitorare in modo immediato lo stato di integrità delle soluzioni dei partner integrate nella sottoscrizione di Azure.

![Riquadro Soluzioni partner][1]

Il riquadro **Soluzioni partner** mostra il numero di soluzioni dei partner integrate con la sottoscrizione. Se non sono presenti soluzioni integrate, il riquadro visualizza il numero zero.

Per visualizzare l'integrità delle soluzioni dei partner:

1. Selezionare il riquadro **Soluzioni partner** . Viene visualizzato il pannello **Soluzioni partner** con un elenco di soluzioni dei partner connesse al Centro sicurezza.

   ![Soluzioni partner][3]

   Una soluzione partner può avere uno degli stati seguenti:

   * Protetto (verde): nessun problema di integrità.
   * Danneggiato (rosso): è presente un problema di integrità che richiede attenzione immediata.
   * Segnalazione arrestata (arancione): la soluzione ha arrestato la segnalazione dello stato di integrità.
   * Sconosciuto (arancione): l'integrità della soluzione è attualmente sconosciuta, a causa di un processo di aggiunta di una nuova risorsa alla soluzione esistente non riuscito.
   * Non segnalato (grigio): la soluzione non ha ancora inviato alcuna segnalazione. Lo stato di una soluzione può essere non segnalato se la soluzione è appena stata connessa ed è ancora in fase di distribuzione.

2. Selezionare una soluzione dei partner. In questo esempio viene selezionata la soluzione **Qualys**.  Viene visualizzato un pannello che mostra che lo stato della soluzione partner e le risorse associate alla soluzione. Selezionare **Console della soluzione** per aprire l'esperienza di gestione dei partner per questa soluzione.

   ![Dettagli della soluzione di un partner][4]
3. Tornare al pannello **Qualys** e selezionare **Collega la macchina virtuale**. Verrà visualizzato il pannello **Collega applicazioni** . In questo pannello è possibile connettere risorse alla soluzione del partner.

   ![Collegare risorse alla soluzione di un partner][5]

## <a name="next-steps"></a>Passaggi successivi
Questo documento ha presentato il riquadro **Soluzioni partner** del Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

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

