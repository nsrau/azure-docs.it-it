---
title: Abilitare la raccolta dati nel Centro sicurezza di Azure | Documentazione Microsoft
description: " Informazioni su come abilitare la raccolta dati nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7e9ad8cd8c77c57c37dc208b86b3727a4e1dc7b5
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>Abilitare la raccolta dati nel Centro sicurezza di Azure

> [!NOTE]
> A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>
>

Il Centro sicurezza raccoglie i dati dalle macchine virtuali per valutarne lo stato della sicurezza, indicare raccomandazioni sulla sicurezza e segnalare le minacce. La prima volta che si accede al Centro sicurezza, è possibile abilitare la raccolta dati viene abilitata in tutte le macchine virtuali della sottoscrizione. Se la raccolta dati non è abilitata, il Centro sicurezza consiglierà di attivarla nei criteri di sicurezza per la sottoscrizione in questione.

Quando la raccolta dati è attivata, il Centro sicurezza effettua il provisioning di Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate esistenti e in quelle nuove che vengono create. Microsoft Monitoring Agent esegue l'analisi per diverse configurazioni di sicurezza. Il sistema operativo genera anche gli eventi del registro eventi. Esempi di tali dati sono: tipo e versione del sistema operativo, log del sistema operativo (registri eventi di Windows), processi in esecuzione, nome computer, indirizzi IP, utente connesso e ID tenant. Microsoft Monitoring Agent legge le voci e le configurazioni del log eventi e copia i dati nell'area di lavoro per l'analisi. Microsoft Monitoring Agent copia anche i file di dump di arresto anomalo del sistema nelle aree di lavoro.

Se si usa la versione gratuita del Centro sicurezza di Azure, è anche possibile disabilitare la raccolta dati dalle macchine virtuali disabilitando la raccolta dati nei criteri di sicurezza. La disabilitazione della raccolta dati consente di limitare le valutazioni relative alla sicurezza per le macchine virtuali. Per ulteriori informazioni, vedere [Disabilitazione della raccolta dati](#disabling-data-collection). Gli snapshot dei dischi delle macchine virtuali e la raccolta di elementi resteranno abilitati anche se la raccolta dati è stata disabilitata. La raccolta dati è richiesta per le sottoscrizioni a livello Standard del Centro sicurezza.

> [!NOTE]
> Per altre informazioni vedere i [piani tariffari](security-center-pricing.md) gratuito e standard del Centro sicurezza.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio. Questo argomento non costituisce una guida dettagliata.
>
>

1. Nel pannello **Raccomandazioni** selezionare **Abilita la raccolta di dati per le sottoscrizioni**.  Verrà visualizzato il pannello **Attiva la raccolta di dati** .
   ![Pannello Raccomandazioni][2]
2. Nel pannello **Attiva la raccolta di dati** selezionare la sottoscrizione. Verrà visualizzato il pannello **Criteri di sicurezza** per la sottoscrizione.
3. Nel pannello **Criteri di sicurezza** selezionare **Sì** in **Raccolta di dati** per raccogliere automaticamente i log. Attivando la raccolta dati verrà eseguito il provisioning dell'estensione di monitoraggio per tutte le VM correnti e nuove supportate nella sottoscrizione.
4. Selezionare **Salva**.
5. Selezionare **OK**.

## <a name="disabling-data-collection"></a>Disabilitazione della raccolta dati
Se si usa la versione gratuita del Centro sicurezza, è anche possibile disabilitare la raccolta dati dalle macchine virtuali in qualsiasi momento disabilitando la raccolta dati nei criteri di sicurezza. La raccolta dati è richiesta per le sottoscrizioni a livello Standard del Centro sicurezza.

1. Tornare al pannello **Centro sicurezza** e selezionare il riquadro **Criteri**. Verrà visualizzato il pannello **Security policy-Define policy per subscription** (Criteri di sicurezza - Definire i criteri per sottoscrizione).
   ![Selezionare il riquadro Criteri][5]
2. Nel pannello **Security policy-Define policy per subscription** (Criteri di sicurezza - Definire i criteri per sottoscrizione) selezionare la sottoscrizione per la quale disabilitare la raccolta dati.
3. Verrà visualizzato il pannello **Criteri di sicurezza** per la sottoscrizione.  Selezionare **No** in Raccolta di dati.
4. Fare clic su **Salva** nella barra multifunzione in alto.

## <a name="next-steps"></a>Passaggi successivi
Questo documento illustra come implementare la raccomandazione "Abilita raccolta dati" del Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
- [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md): informazioni sulla gestione e la protezione dei dati nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

