---
title: Autorizzazioni nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo articolo illustra come Centro sicurezza di Azure usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e identificare le azioni consentite per ogni ruolo.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/02/2020
ms.author: memildin
ms.openlocfilehash: 446abd8cca14c0c1c27458ef86cee4400252e3b0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965205"
---
# <a name="permissions-in-azure-security-center"></a>Autorizzazioni nel Centro sicurezza di Azure

Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md), che fornisce [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

* **Ruolo con autorizzazioni di lettura per la sicurezza**: un utente che appartiene a questo ruolo dispone dei diritti di visualizzazione nel Centro sicurezza. L'utente può visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non può apportare modifiche.
* **Amministratore della sicurezza**: un utente che appartiene a questo ruolo ha gli stessi diritti del Ruolo con autorizzazioni di lettura per la sicurezza e può anche aggiornare i criteri di sicurezza e rimuovere gli avvisi e le raccomandazioni.

> [!NOTE]
> I ruoli di sicurezza Ruolo con autorizzazioni di lettura per la sicurezza e Amministratore della protezione hanno accesso solo al Centro sicurezza. I ruoli di sicurezza non hanno accesso ad altre aree del servizio di Azure come Archiviazione, Web e dispositivi mobili o Internet delle cose.
>

## <a name="roles-and-allowed-actions"></a>Ruoli e azioni consentite

La tabella seguente contiene i ruoli e le azioni consentite in Centro sicurezza.

|Operazione|Ruolo con autorizzazioni di lettura per la sicurezza / <br> Reader |Amministrazione della protezione  |Collaboratore del gruppo di risorse / <br> Proprietario del gruppo di risorse  |Collaboratore alla sottoscrizione  |Proprietario della sottoscrizione  |
|:--- |:---:|:---:|:---:|:---:|:---:|
|Modificare i criteri di sicurezza|-|✔|-|-|✔|
|Aggiungere/assegnare iniziative, inclusi gli standard di conformità alle normative|-|-|-|-|✔|
|Abilitare/disabilitare Azure Defender|-|✔|-|-|✔|
|Abilitare/disabilitare il provisioning automatico|-|✔|-|✔|✔|
|Applicare i suggerimenti per la sicurezza per una risorsa</br> (e usare la [correzione rapida](security-center-remediate-recommendations.md#quick-fix-remediation))|-|-|✔|✔|✔|
|Ignorare gli avvisi|-|✔|-|✔|✔|
|Visualizzare gli avvisi e le raccomandazioni|✔|✔|✔|✔|✔|

> [!NOTE]
> È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Assegnare ad esempio il ruolo di lettore agli utenti che devono visualizzare solo le informazioni sull'integrità della sicurezza di una risorsa, ma non eseguono alcuna azione, come l'applicazione di consigli e la modifica di criteri.
>
>

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come viene usato il Controllo degli accessi in base al ruolo di Azure nel Centro sicurezza per assegnare autorizzazioni agli utenti e sono state identificate le azioni consentite per ogni ruolo. Ora che è stata acquisita familiarità con le assegnazioni di ruolo necessari per monitorare lo stato di sicurezza della sottoscrizione, modificare i criteri di sicurezza e applicare i consigli, si apprenderà come eseguire queste operazioni:

- [Impostare i criteri di sicurezza nel Centro sicurezza](tutorial-security-policy.md)
- [Gestire i suggerimenti per la sicurezza in Centro sicurezza](security-center-recommendations.md)
- [Monitorare l'integrità della sicurezza delle risorse di Azure](security-center-monitoring.md)
- [Gestire e rispondere agli avvisi di sicurezza in Centro sicurezza](security-center-managing-and-responding-alerts.md)
- [Monitorare le soluzioni di sicurezza dei partner](./security-center-partner-integration.md)