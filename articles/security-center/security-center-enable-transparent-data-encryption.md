---
title: Abilitare Transparent Data Encryption nel Centro sicurezza di Azure | Documentazione Microsoft
description: "In questo documento è illustrato come implementare la raccomandazione **AbilitareTransparent Data Encryption** del Centro sicurezza di Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 2a2963affdbff3710ad08f86c6ed4e6304335559
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Abilitare Transparent Data Encryption nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglia di abilitare Transparent Data Encryption (TDE) nel database SQL, se non è già abilitato. TDE protegge i dati e consente di soddisfare i requisiti di conformità tramite la crittografia del database, backup associati e file di log delle transazioni inattive, senza richiedere modifiche all'applicazione. Per altre informazioni, vedere [Transparent Data Encryption con il database SQL di Azure](https://msdn.microsoft.com/library/dn948096).

Questa indicazione si applica esclusivamente al servizio SQL di Azure e non include SQL in esecuzione sulle macchine virtuali.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Abilita Transparent Data Encryption**.
   ![Abilita Transparent Data Encryption][1]
2. Viene visualizzato il pannello **Abilita Transparent Data Encryption nei database SQL** . Selezionare un database SQL su cui abilitare la crittografiaTDE.
   ![Selezionare database SQL per abilitare la crittografia TDE][2]
3. Nel pannello **Transparent Data Encryption** selezionare **ON** in Crittografia dati e selezionare **Salva** nella barra multifunzione situata nella parte superiore del pannello.
   ![Attivare la crittografia TDE][3]

   Dopo aver abilitato TDE nel database SQL selezionato, lo **Stato crittografia** diventerà **Crittografato**.    

   ![Stato della crittografia][4]

## <a name="see-also"></a>Vedere anche
Questo documento illustra come implementare la raccomandazione "Abilitare Transparent Data Encryption" del Centro sicurezza. Per altre informazioni su TDE di SQL, vedere gli argomenti seguenti:

* [Transparent Data Encryption con il database SQL di Azure](https://msdn.microsoft.com/library/dn948096)
* [Introduzione a Transparent Data Encryption (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
