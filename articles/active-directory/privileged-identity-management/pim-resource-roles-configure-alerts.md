---
title: Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Gestione delle identità con privilegi - Azure Active Directory . Documenti Microsoft
description: Informazioni su come configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023077"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Gestione identità con privilegiConfigure security alerts for Azure resource roles in Privileged Identity Management

La gestione delle identità con privilegi (PIM, Privileged Identity Management) genera avvisi quando è presente un'attività sospetta o non sicura nell'organizzazione di Azure Active Directory (Azure AD). Una volta attivato, un avviso viene visualizzato nella pagina Avvisi.

![Risorse di Azure - Pagina Avvisi che elenca noglia, livello di rischio e conteggioAzure resources - Alerts page listing alert, risk level, and count](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Esaminare gli avvisi

Selezionare un avviso per visualizzare un report in cui sono elencati gli utenti o i ruoli che hanno attivato l'avviso, insieme alle indicazioni per la correzione.

![Report di avviso che mostra il tempo dell'ultima analisi, la descrizione, i passaggi di attenuazione, il tipo, la gravità, l'impatto sulla sicurezza e come impedire la prossima volta](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Avvisi

| Avviso | Gravità | Trigger | Recommendation |
| --- | --- | --- | --- |
| **Troppi proprietari assegnati a una risorsa** |Media |Troppi utenti hanno il ruolo di proprietario. |Controllare gli utenti nell'elenco e riassegnarne alcuni a ruoli con privilegi meno elevati. |
| **Troppi proprietari permanenti assegnati a una risorsa** |Media |Troppi utenti sono assegnati in modo permanente a un ruolo. |Controllare gli utenti nell'elenco e riassegnarne alcuni per richiedere l'attivazione per l'uso dei ruoli. |
| **È stato creato un ruolo duplicato** |Media |Più ruoli hanno gli stessi criteri. |Usare solo uno di questi ruoli. |

### <a name="severity"></a>Gravità

- **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri. 
- **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
- **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza

Dalla pagina Avvisi passare a **Impostazioni**.

![Pagina Avvisi con l'applicazione impostazioni evidenziata](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalizzare le impostazioni per i diversi avvisi adattandole all'ambiente e agli obiettivi di sicurezza specifici.

![Pagina Impostazione di un avviso per abilitare e configurare le impostazioni](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo delle risorse di Azure in Gestione delle identità con privilegiConfigure Azure resource role settings in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
