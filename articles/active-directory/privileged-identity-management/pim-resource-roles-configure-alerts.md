---
title: Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 827c42763eee39c717cedc90469ae765cc331272
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66253845"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) genera avvisi quando vengono eseguite operazioni sospette o non sicure nel proprio ambiente. Una volta attivato, un avviso viene visualizzato nella pagina Avvisi. 

![Pagina degli avvisi](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Esaminare gli avvisi
Selezionare un avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso, insieme a suggerimenti per la correzione.

![Report degli avvisi](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Avvisi
| Avviso | Severity | Trigger | Recommendation |
| --- | --- | --- | --- |
| **Troppi proprietari assegnati a una risorsa** |Media |Troppi utenti hanno il ruolo di proprietario. |Controllare gli utenti nell'elenco e riassegnarne alcuni a ruoli con privilegi meno elevati. |
| **Troppi proprietari permanenti assegnati a una risorsa** |Media |Troppi utenti sono assegnati in modo permanente a un ruolo. |Controllare gli utenti nell'elenco e riassegnarne alcuni per richiedere l'attivazione per l'uso dei ruoli. |
| **È stato creato un ruolo duplicato** |Media |Più ruoli hanno gli stessi criteri. |Usare solo uno di questi ruoli. |


### <a name="severity"></a>Severity
* **Alta**: richiede un'azione immediata a causa di una violazione dei criteri. 
* **Medium**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
* **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza
Dalla pagina Avvisi passare a **Impostazioni**.
![Impostazioni](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalizzare le impostazioni per i diversi avvisi adattandole all'ambiente e agli obiettivi di sicurezza specifici.
![Personalizzare le impostazioni](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
