---
title: Introduzione ai criteri di sicurezza del Centro sicurezza di Azure | Microsoft Docs
description: "Informazioni sui criteri di sicurezza e sulle funzionalità principali del Centro sicurezza di Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: f85f7077e5227818d062d114370e7344601a998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="security-policies-overview"></a>Panoramica dei criteri di sicurezza
Questo documento offre una panoramica dei criteri di sicurezza del Centro sicurezza.

## <a name="what-are-security-policies"></a>Informazioni sui criteri di sicurezza
Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza. Nel Centro sicurezza è possibile definire criteri per le sottoscrizioni di Azure, che possono essere adattati al tipo di carico di lavoro o alla riservatezza dei dati. Ad esempio, le applicazioni che usano dati regolamentati come le informazioni personali possono richiedere un livello di sicurezza maggiore di quello degli altri carichi di lavoro. 

I criteri del Centro sicurezza includono i componenti seguenti:

- Raccolta di dati: provisioning dell'agente e impostazioni della [raccolta di dati](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- Criteri di sicurezza: un [criterio di Azure](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) che determina quali controlli vengono monitorati e consigliati dal Centro sicurezza. È possibile modificarli nel Centro sicurezza o usare i criteri di Azure per creare nuove definizioni, definire criteri aggiuntivi e assegnare criteri nei gruppi di gestione.
- Notifiche tramite posta elettronica: contatti di sicurezza e impostazioni di [notifica tramite posta elettronica](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).
- Piano tariffario: sono disponibili le [opzioni](https://docs.microsoft.com/azure/security-center/security-center-pricing) Gratuito o Standard, che determinano le funzionalità del Centro sicurezza disponibili per le risorse nell'ambito. Questo criterio può essere specificato per sottoscrizioni, gruppi di risorse e aree di lavoro. 


## <a name="who-can-edit-security-policies"></a>Utenti che possono modificare i criteri di sicurezza
Il Centro sicurezza usa il controllo degli accessi in base al ruolo, che fornisce ruoli predefiniti assegnabili a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. All'utente viene infatti assegnato il ruolo Proprietario, Collaboratore o Lettore per la sottoscrizione o il gruppo di risorse a cui appartiene la risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

- Ruolo con autorizzazioni di lettura per la sicurezza: l'utente che appartiene a questo ruolo può visualizzare i diritti per il Centro sicurezza, inclusi avvisi, criteri, raccomandazioni e integrità, ma non potrà apportare modifiche.
- Amministratore della sicurezza: come il ruolo con autorizzazioni di lettura per la sicurezza, ma può anche aggiornare i criteri di sicurezza e ignorare raccomandazioni e avvisi.


## <a name="next-steps"></a>Passaggi successivi
In questo documento sono state fornite informazioni sui criteri di sicurezza nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md): informazioni sulla gestione e la protezione dei dati nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.


