---
title: Gestire gli avvisi di sicurezza per le risorse di Azure con Privileged Identity Management | Microsoft Docs
description: Descrive gli avvisi di sicurezza di PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c6c057541b3e3067de6331bab6ca9cccfa092710
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149186"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Gestire gli avvisi di sicurezza per le risorse di Azure con Privileged Identity Management
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
