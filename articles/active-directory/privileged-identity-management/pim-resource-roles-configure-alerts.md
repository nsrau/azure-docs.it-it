---
title: Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in PIM | Microsoft Docs
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
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 33bd13421b0c1574475516da942abe4e8c71ed52
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188833"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in PIM
Azure Privileged Identity Management (PIM) per le risorse di Azure genera avvisi nel caso di attività sospette o non sicure nell'ambiente. Una volta attivato, un avviso viene visualizzato nella pagina Avvisi. 

![Pagina degli avvisi](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Esaminare gli avvisi
Selezionare un avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso, insieme a suggerimenti per la correzione.

![Report degli avvisi](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Avvisi
| Avviso | Gravità | Trigger | Raccomandazione |
| --- | --- | --- | --- |
| **Troppi proprietari assegnati a una risorsa** |Media |Troppi utenti hanno il ruolo di proprietario. |Controllare gli utenti nell'elenco e riassegnarne alcuni a ruoli con privilegi meno elevati. |
| **Troppi proprietari permanenti assegnati a una risorsa** |Media |Troppi utenti sono assegnati in modo permanente a un ruolo. |Controllare gli utenti nell'elenco e riassegnarne alcuni per richiedere l'attivazione per l'uso dei ruoli. |
| **È stato creato un ruolo duplicato** |Media |Più ruoli hanno gli stessi criteri. |Usare solo uno di questi ruoli. |


### <a name="severity"></a>Gravità
* **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri. 
* **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
* **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza
Dalla pagina Avvisi passare a **Impostazioni**.
![Impostazioni](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Personalizzare le impostazioni per i diversi avvisi adattandole all'ambiente e agli obiettivi di sicurezza specifici.
![Personalizzare le impostazioni](media/azure-pim-resource-rbac/rbac-alert-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'autenticazione MFA (Multi-Factor Authentication) per i ruoli delle risorse di Azure in PIM](pim-resource-roles-require-mfa.md)
- [Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-alerts.md)
