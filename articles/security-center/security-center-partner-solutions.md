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
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 6b7d2da8ababba65146503ecfbe6fd6e142a359c
ms.openlocfilehash: 2d7ab8fbed1d4edb60416c7c6b7ae08ddbdec91a
ms.lasthandoff: 02/06/2017


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

Il riquadro **Soluzioni partner** mostra il numero di soluzioni dei partner e un riepilogo dello stato per tali soluzioni.

La voce **STATO** di una soluzione di un partner può indicare uno degli stati seguenti:

* Protetto (verde): nessun problema di integrità.
* Danneggiato (rosso): è presente un problema di integrità che richiede attenzione immediata.
* Segnalazione arrestata (arancione): la soluzione ha arrestato la segnalazione dello stato di integrità.
* Sconosciuto (arancione): l'integrità della soluzione è attualmente sconosciuta, a causa di un processo di aggiunta di una nuova risorsa alla soluzione esistente non riuscito.
* Non segnalato (grigio): la soluzione non ha ancora inviato alcuna segnalazione. Lo stato di una soluzione può essere non segnalato se la soluzione è appena stata connessa ed è ancora in fase di distribuzione.

Se nella sottoscrizione non è integrata alcuna soluzione, il riquadro indica che non ci sono soluzioni. Selezionando il riquadro **Soluzioni partner**, è possibile aprire il pannello **Raccomandazioni** per distribuire soluzioni di sicurezza dei partner.

![Nessuna soluzione partner][2]

Per visualizzare l'integrità delle soluzioni dei partner:

1. Selezionare il riquadro **Soluzioni partner** . Viene visualizzato un pannello contenente un elenco di soluzioni dei partner connesse al Centro sicurezza.

   ![Soluzioni partner][3]
2. Selezionare una soluzione dei partner. In questo esempio viene selezionata la soluzione **F5-WAF2** .  Viene visualizzato un pannello che mostra che lo stato della soluzione partner e le risorse associate alla soluzione. Selezionare **Console della soluzione** per aprire l'esperienza di gestione dei partner per questa soluzione.

   ![Dettagli della soluzione di un partner][4]
3. Tornare al pannello **F5-WAF2** e selezionare **Collega app**. Verrà visualizzato il pannello **Collega applicazioni** . In questo pannello è possibile connettere risorse alla soluzione del partner.

   ![Collegare risorse alla soluzione di un partner][5]

## <a name="see-also"></a>Vedere anche
Questo documento ha presentato il riquadro **Soluzioni partner** del Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

