---
title: Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f191eac841bd9f79844a09cd25725de7ea8b154
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895783"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Privileged Identity Management

Privileged Identity Management (PIM) genera avvisi in caso di attività sospette o non sicure nell'organizzazione di Azure Active Directory (Azure AD). Una volta attivato, un avviso viene visualizzato nella pagina Avvisi.

![Risorse di Azure-pagina avvisi che elenca l'avviso, il livello di rischio e il conteggio](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Esaminare gli avvisi

Selezionare un avviso per visualizzare un report in cui sono elencati gli utenti o i ruoli che hanno attivato l'avviso, oltre alle indicazioni per la correzione.

![Segnalazione avvisi che mostra l'ora dell'ultima analisi, la descrizione, i passaggi di mitigazione, il tipo, la gravità, l'effetto sulla sicurezza e come evitare la prossima volta](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Avvisi

| Alert | Severity | Trigger | Recommendation |
| --- | --- | --- | --- |
| **Troppi proprietari assegnati a una risorsa** |Media |Troppi utenti hanno il ruolo di proprietario. |Controllare gli utenti nell'elenco e riassegnarne alcuni a ruoli con privilegi meno elevati. |
| **Troppi proprietari permanenti assegnati a una risorsa** |Media |Troppi utenti sono assegnati in modo permanente a un ruolo. |Controllare gli utenti nell'elenco e riassegnarne alcuni per richiedere l'attivazione per l'uso dei ruoli. |
| **È stato creato un ruolo duplicato** |Media |Più ruoli hanno gli stessi criteri. |Usare solo uno di questi ruoli. |

### <a name="severity"></a>Severity

- **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri. 
- **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
- **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza

Dalla pagina Avvisi passare a **Impostazioni**.

![Pagina avvisi con impostazioni evidenziate](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalizzare le impostazioni per i diversi avvisi adattandole all'ambiente e agli obiettivi di sicurezza specifici.

![Impostazione della pagina per un avviso per abilitare e configurare le impostazioni](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
