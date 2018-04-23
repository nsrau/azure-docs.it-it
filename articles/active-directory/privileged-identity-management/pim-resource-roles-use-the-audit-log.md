---
title: Privileged Identity Management per le risorse di Azure - Controllo delle risorse | Microsoft Docs
description: Spiega come ottenere una visualizzazione di tutte le attività dei ruoli per una risorsa specifica.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - Ruoli delle risorse - Controllo

L'opzione Controllo delle risorse fornisce una visualizzazione di tutte le attività del ruolo per la risorsa. È possibile filtrare le informazioni usando una data predefinita o un intervallo personalizzato.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

L'opzione Controllo delle risorse consente anche l'accesso rapido alla visualizzazione dei dettagli delle attività di un utente. In "Tipo di controllo" selezionare "Attiva". Fare clic su "(attività)" per visualizzare le azioni dell'utente su risorse di Azure.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Controllo personale

Controllo personale offre una visualizzazione delle attività personali del ruolo dell'utente. È possibile filtrare le informazioni usando una data predefinita o un intervallo personalizzato.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visualizzare l'attivazione e l'attività di Risorse di Azure

Se è necessario verificare quali azioni sono state effettuate da un utente specifico su diverse risorse, è possibile esaminare l'attività di Risorse di Azure associata a un periodo di attivazione specifico, per gli utenti idonei. Selezionare prima di tutto un utente dalla visualizzazione Membri o dall'elenco di membri in un ruolo specifico. Il risultato mostra una visualizzazione grafica delle azioni dell'utente in Risorse di Azure in base alla data e le attivazioni di ruoli recenti nello stesso periodo di tempo.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

Se si seleziona un'attivazione di ruolo specifica, verranno visualizzati i dettagli relativi all'attivazione del ruolo e l'attività di Risorse di Azure corrispondente effettuata nel periodo in cui l'utente è risultato attivo.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

