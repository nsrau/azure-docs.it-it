---
title: Privileged Identity Management per le risorse di Azure - Avvisi di sicurezza | Microsoft Docs
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
ms.openlocfilehash: 86c9a0f12b2598ffbd02810a11622b13b0363a1f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---alerts"></a>Privileged Identity Management - Ruoli delle risorse - Avvisi
PIM per le risorse di Azure genera attivi in presenza di attività sospette o non sicure nel proprio ambiente. Una volta attivato, un avviso viene visualizzato nella pagina Avvisi. 

![](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Esaminare gli avvisi
Selezionare un avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso oltre a suggerimenti per la correzione.
![](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Avvisi
| Avviso | Gravità | Trigger | Raccomandazione |
| --- | --- | --- | --- |
| **Troppi proprietari assegnati a una risorsa** |Media |Troppi utenti hanno il ruolo Proprietario. |Controllare gli utenti nell'elenco e riassegnarne alcuni a ruoli con privilegi meno elevati. |
| **Troppi proprietari permanenti assegnati a una risorsa** |Media |Troppi utenti sono assegnati in modo permanente a un ruolo. |Controllare gli utenti nell'elenco e riassegnarne alcuni per richiedere l'attivazione per l'uso dei ruoli. |
| **È stato creato un ruolo duplicato** |Media |Più ruoli hanno gli stessi criteri. |Usare solo uno di questi ruoli. |


### <a name="severity"></a>Gravità
* **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri. 
* **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
* **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza
Dalla pagina Avvisi passare a Impostazioni.
![](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Personalizzare le impostazioni per i diversi avvisi adattandole all'ambiente e agli obiettivi di sicurezza specifici.
![](media/azure-pim-resource-rbac/rbac-alert-settings.png)
