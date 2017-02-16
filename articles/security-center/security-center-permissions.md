---
title: Autorizzazioni nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo articolo illustra come Centro sicurezza di Azure usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e identificare le azioni consentite per ogni ruolo.
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5c4a08d96175d431e0a29dfc5927b64567c40117
ms.openlocfilehash: d1c8a39bbcda7d22fdce08d122098e994ca87451


---

# <a name="permissions-in-azure-security-center"></a>Autorizzazioni nel Centro sicurezza di Azure

Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md), con [ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

## <a name="roles-and-allowed-actions"></a>Ruoli e azioni consentite

La tabella seguente contiene i ruoli e le azioni consentite in Centro sicurezza. Un simbolo X indica che l'azione è consentita per il ruolo.

| Ruolo | Modificare i criteri di sicurezza | Applicare i suggerimenti per la sicurezza per una risorsa | Correggere o eliminare gli avvisi | Visualizzare avvisi in una sottoscrizione | Visualizzare avvisi per una risorsa specifica |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Proprietario della sottoscrizione | X | X | X | X | X |
| Collaboratore alla sottoscrizione | X | X | X | X | X |
| Proprietario del gruppo di risorse | -- | X | -- | -- | X |
| Collaboratore del gruppo di risorse | -- | X | -- | -- | X |
| Lettore | -- | -- | -- | X | X |

> [!NOTE]
> È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Assegnare ad esempio il ruolo di lettore agli utenti che devono visualizzare solo le informazioni sull'integrità della sicurezza di una risorsa, ma non eseguono alcuna azione, come l'applicazione di consigli e la modifica di criteri.
>
>

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come Centro sicurezza usa PC usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e sono state identificate le azioni consentite per ogni ruolo. Ora che è stata acquisita familiarità con le assegnazioni di ruolo necessari per monitorare lo stato di sicurezza della sottoscrizione, modificare i criteri di sicurezza e applicare i consigli, si apprenderà come eseguire queste operazioni:

- [Impostare criteri di sicurezza in Centro sicurezza](security-center-policies.md)
- [Gestire i suggerimenti per la sicurezza in Centro sicurezza](security-center-recommendations.md)
- [Monitorare l'integrità della sicurezza delle risorse di Azure](security-center-monitoring.md)
- [Gestire e rispondere agli avvisi di sicurezza in Centro sicurezza](security-center-managing-and-responding-alerts.md)
- [Monitorare le soluzioni di sicurezza dei partner](security-center-partner-solutions.md)



<!--HONumber=Dec16_HO2-->


