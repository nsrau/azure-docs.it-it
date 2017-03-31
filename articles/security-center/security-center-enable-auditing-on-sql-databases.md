---
title: Abilitare il controllo sui database SQL nel Centro sicurezza di Azure | Microsoft Docs
description: "In questo documento è illustrato come implementare la raccomandazione **Abilitare il controllo sui database SQL** del Centro sicurezza di Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: d36bb775d0b0fd732562b18787b65600c8fb5fa1
ms.lasthandoff: 02/17/2017


---
# <a name="enable-auditing-on-sql-databases-in-azure-security-center"></a>Abilitare il controllo sui database SQL nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglia di attivare il controllo per tutti i database SQL, se non è già abilitato. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Dopo aver attivato il controllo è possibile configurare le impostazioni di rilevamento delle minacce e gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. Essa consente di rilevare e rispondere a potenziali rischi appena si verificano.

Questa indicazione si applica esclusivamente al servizio SQL di Azure e non include SQL in esecuzione sulle macchine virtuali.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Abilita il controllo sui database SQL**.  Verrà visualizzato il pannello **Abilita il controllo sui database SQL** .

   ![Abilitare il controllo sui database SQL][1]
2. Selezionare un database SQL su cui abilitare il controllo. Viene visualizzato il pannello **Controllo e rilevamento minacce**.

   ![Controllo e rilevamento minacce][2]
3. Nel pannello **Controllo e rilevamento minacce** selezionare **ON** in **Controllo**.

   ![Attivare il controllo e rilevamento minacce][3]
4. Seguire i passaggi in [SQL Database Threat Detection in the Azure portal](../sql-database/sql-database-threat-detection-portal.md) (Rilevamento di minacce del database SQL nel portale di Azure) per attivare e configurare il rilevamento di minacce e per configurare l'elenco dei messaggi di posta elettronica che riceveranno gli avvisi di sicurezza al rilevamento di anomalie dell'attività.

## <a name="see-also"></a>Vedere anche
Questo documento illustra come implementare la raccomandazione "Abilitare il controllo sui database SQL" del Centro sicurezza. Per altre informazioni su come proteggere il database SQL, vedere gli argomenti seguenti:

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
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]:./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png

