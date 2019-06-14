---
title: Autorizzazioni nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo articolo illustra come Centro sicurezza di Azure usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e identificare le azioni consentite per ogni ruolo.
services: security-center
cloud: na
documentationcenter: na
author: rkarlin
manager: barbkess
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: aa6c154e14fbf5291e2493f4f27b9d4501ba18f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60905620"
---
# <a name="permissions-in-azure-security-center"></a>Autorizzazioni nel Centro sicurezza di Azure

Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md), con [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

* **Ruolo con autorizzazioni di lettura per la sicurezza**: un utente che appartiene a questo ruolo ha i diritti di visualizzazione nel Centro sicurezza. L'utente può visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non può apportare modifiche.
* **Amministratore della sicurezza**: un utente che appartiene a questo ruolo ha gli stessi diritti del Ruolo con autorizzazioni di lettura per la sicurezza e può anche aggiornare i criteri di sicurezza e rimuovere gli avvisi e le raccomandazioni.

> [!NOTE]
> I ruoli di sicurezza, Ruolo con autorizzazioni di lettura per la sicurezza e Amministratore della protezione, hanno accesso solo al Centro sicurezza. I ruoli di sicurezza non hanno accesso ad altre aree del servizio di Azure come Archiviazione, Web e dispositivi mobili o Internet delle cose.
>
>

## <a name="roles-and-allowed-actions"></a>Ruoli e azioni consentite

La tabella seguente contiene i ruoli e le azioni consentite in Centro sicurezza. Un simbolo X indica che l'azione è consentita per il ruolo.

| Ruolo | Modificare i criteri di sicurezza | Applicare i suggerimenti per la sicurezza per una risorsa | Ignorare gli avvisi e le raccomandazioni | Visualizzare gli avvisi e le raccomandazioni |
|:--- |:---:|:---:|:---:|:---:|
| Proprietario della sottoscrizione | X | X | X | X |
| Collaboratore alla sottoscrizione | -- | X | X | X |
| Proprietario del gruppo di risorse | -- | X | -- | X |
| Collaboratore del gruppo di risorse | -- | X | -- | X |
| Reader | -- | -- | -- | X |
| Amministratore della sicurezza | X | -- | X | X |
| Ruolo con autorizzazioni di lettura per la sicurezza | -- | -- | -- | X |

> [!NOTE]
> È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Assegnare ad esempio il ruolo di lettore agli utenti che devono visualizzare solo le informazioni sull'integrità della sicurezza di una risorsa, ma non eseguono alcuna azione, come l'applicazione di consigli e la modifica di criteri.
>
>

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come Centro sicurezza usa PC usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e sono state identificate le azioni consentite per ogni ruolo. Ora che è stata acquisita familiarità con le assegnazioni di ruolo necessari per monitorare lo stato di sicurezza della sottoscrizione, modificare i criteri di sicurezza e applicare i consigli, si apprenderà come eseguire queste operazioni:

- [Impostare criteri di sicurezza in Centro sicurezza](tutorial-security-policy.md)
- [Gestire i suggerimenti per la sicurezza in Centro sicurezza](security-center-recommendations.md)
- [Monitorare l'integrità della sicurezza delle risorse di Azure](security-center-monitoring.md)
- [Gestire e rispondere agli avvisi di sicurezza in Centro sicurezza](security-center-managing-and-responding-alerts.md)
- [Monitorare le soluzioni di sicurezza dei partner](security-center-partner-solutions.md)
