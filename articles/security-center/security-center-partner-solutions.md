---
title: Gestione delle soluzioni connesse dei partner nel Centro sicurezza di Azure | Microsoft Docs
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
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Gestione delle soluzioni connesse dei partner nel Centro sicurezza di Azure
Questo articolo contiene informazioni dettagliate su come gestire e monitorare le soluzioni di sicurezza connesse nel Centro sicurezza di Azure.

## <a name="monitoring-partner-solutions"></a>Monitoraggio delle soluzioni dei partner
Per monitorare lo stato di integrità delle soluzioni di sicurezza connesse ed eseguire attività di gestione di base:

1. In **Centro sicurezza - Panoramica** selezionare **Soluzioni di sicurezza**.

  ![Selezionare le soluzioni di sicurezza][1]

  La sezione **Soluzioni connesse** include le soluzioni di sicurezza connesse al Centro sicurezza e informazioni sullo stato di integrità di ogni soluzione.

  ![Soluzioni partner][2]

   Una soluzione partner può avere uno degli stati seguenti:

   * Integro (verde): non ci sono problemi di integrità.
   * Danneggiato (rosso): è presente un problema di integrità che richiede attenzione immediata.
   * Problemi di integrità (arancione): la soluzione non segnala più lo stato di integrità.
   * Segnalazione non inviata (grigio): la soluzione non ha ancora inviato alcuna segnalazione. Lo stato di una soluzione può non essere segnalato se la soluzione è stata connessa recentemente ed è ancora in fase di distribuzione oppure se non sono disponibili dati relativi all'integrità.

   > [!NOTE]
   > Se i dati relativi allo stato di integrità non sono disponibili, il Centro sicurezza mostra la data e l'ora dell'ultimo evento ricevuto, per indicare se la soluzione invia segnalazioni o meno. Se non sono disponibili dati relativi all'integrità e non sono stati ricevuti avvisi negli ultimi 14 giorni, il Centro sicurezza indica che la soluzione non è integra o non invia segnalazioni.
   >
   >

2. Selezionare **VISUALIZZA** per altre informazioni e opzioni, tra cui:

  - **Console della soluzione**. Apre l'esperienza di gestione per questa soluzione.
  - **Collega la macchina virtuale**. Apre il pannello Collega applicazioni. In questo pannello è possibile connettere risorse alla soluzione del partner.
  - **Elimina soluzione**.
  - **Configura**.

   ![Dettagli della soluzione di un partner][3]

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come gestire e monitorare le soluzioni di sicurezza connesse nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Panoramica delle soluzioni di sicurezza](security-center-partner-integration.md): informazioni su come connettere e gestire le soluzioni di sicurezza.
* [Connessione di Microsoft Advanced Threat Analytics](security-center-ata-integration.md): informazioni su come connettere gli avvisi di ATA.
* [Connessione di Azure Active Directory (AD) Identity Protection](security-center-aadip-integration.md): informazioni su come connettere gli avvisi di Azure AD Identity Protection.
* [Integrazione di partner e soluzioni](security-center-partner-integration.md): panoramica dell'integrazione di altre soluzioni di sicurezza.
* [Gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
