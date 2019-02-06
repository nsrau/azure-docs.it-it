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
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 564517052796ee5dbc022ff92afcaa0216bdf8ea
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196839"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in PIM
Privileged Identity Management (PIM) per le risorse di Azure genera avvisi in presenza di attività sospette o non sicure nell'ambiente. Una volta attivato, un avviso viene visualizzato nella pagina Avvisi. 

![Pagina degli avvisi](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Esaminare gli avvisi
Selezionare un avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso, insieme a suggerimenti per la correzione.

![Report degli avvisi](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Avvisi
| Avviso | Gravità | Trigger | Recommendation |
| --- | --- | --- | --- |
| **Troppi proprietari assegnati a una risorsa** |Media |Troppi utenti hanno il ruolo di proprietario. |Controllare gli utenti nell'elenco e riassegnarne alcuni a ruoli con privilegi meno elevati. |
| **Troppi proprietari permanenti assegnati a una risorsa** |Media |Troppi utenti sono assegnati in modo permanente a un ruolo. |Controllare gli utenti nell'elenco e riassegnarne alcuni per richiedere l'attivazione per l'uso dei ruoli. |
| **È stato creato un ruolo duplicato** |Media |Più ruoli hanno gli stessi criteri. |Usare solo uno di questi ruoli. |


### <a name="severity"></a>Gravità
* **Alta**: richiede un'azione immediata a causa di una violazione dei criteri. 
* **Medium**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
* **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza
Dalla pagina Avvisi passare a **Impostazioni**.
![Impostazioni](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Personalizzare le impostazioni per i diversi avvisi adattandole all'ambiente e agli obiettivi di sicurezza specifici.
![Personalizzare le impostazioni](media/azure-pim-resource-rbac/rbac-alert-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare gli avvisi di sicurezza per i ruoli delle risorse in PIM](pim-resource-roles-configure-alerts.md)
