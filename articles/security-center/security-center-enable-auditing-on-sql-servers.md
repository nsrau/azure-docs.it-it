---
title: Abilitare il controllo e il rilevamento delle minacce sui server SQL nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento viene illustrato come implementare la raccomandazione del Centro sicurezza di Azure * * il rilevamento di abilitare il controllo & minaccia in SQL Server * *.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 660b537aef8d175a478ff93d60b8391d55fc92ad
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Abilitare il controllo e il rilevamento delle minacce sui server SQL nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglia di attivare il controllo e il rilevamento delle minacce per tutti i database nei server SQL di Azure, se queste funzionalità non sono già abilitate. Il controllo e il rilevamento delle minacce possono agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Dopo aver attivato il controllo è possibile configurare le impostazioni di rilevamento delle minacce e gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. Essa consente di rilevare e rispondere a potenziali rischi appena si verificano.

Questa indicazione si applica esclusivamente al servizio SQL di Azure e non include i server SQL in esecuzione sulle macchine virtuali all'interno dei servizi di infrastruttura di Azure (Azure IaaS).

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Enable Auditing & Threat detection on SQL servers** (Abilita il controllo e il rilevamento delle minacce sui server SQL).  Verrà visualizzato il pannello **Enable Auditing & Threat detection on SQL servers** (Abilita il controllo e il rilevamento delle minacce sui server SQL).

   ![Abilitare il controllo sui server SQL][1]
2. Selezionare il server SQL su cui abilitare il controllo e il rilevamento delle minacce. Verrà visualizzato il pannello **Controllo e rilevamento minacce**.

3. Nel pannello **Controllo e rilevamento minacce** selezionare **ON** in **Controllo**.

   ![Attivare le impostazioni del servizio di controllo][2]
4. Seguire la procedura in [SQL database auditing in the Azure portal](../sql-database/sql-database-auditing-portal.md) (Controllo del database SQL nel portale di Azure) per configurare l'archiviazione in cui verranno archiviati i log di controllo. L'account di archiviazione della sottoscrizione per la raccolta dei dati è l'account di archiviazione predefinito.
5. Seguire i passaggi in [Introduzione individuazione Database SQL](../sql-database/sql-database-threat-detection.md) per attivare e configurare il rilevamento di minacce e per configurare l'elenco dei messaggi di posta elettronica che riceveranno gli avvisi di sicurezza al rilevamento di anomalie dell'attività.

## <a name="see-also"></a>Vedere anche
Questo articolo illustra come implementare la raccomandazione "Abilitare il controllo e il rilevamento delle minacce sui server SQL" del Centro sicurezza. Per altre informazioni su come proteggere il database SQL, vedere gli argomenti seguenti:

* [Protezione del Database SQL](../sql-database/sql-database-security-overview.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
