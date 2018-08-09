---
title: Controllare i ruoli per le risorse di Azure usando Privileged Identity Management | Microsoft Docs
description: Spiega come ottenere una visualizzazione di tutte le attività dei ruoli per una risorsa specifica.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8f328a609d696886ed452589d3cdfb5f45aec62a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621284"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Controllare i ruoli per le risorse di Azure usando Privileged Identity Management 

L'opzione Controllo delle risorse fornisce una visualizzazione di tutte le attività del ruolo per la risorsa. È possibile filtrare le informazioni usando una data predefinita o un intervallo personalizzato.
![Filtrare informazioni](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Il controllo delle risorse consente anche l'accesso rapido ai dettagli delle attività di un utente. Sotto **Tipo di controllo**, selezionare **Activate**. Selezionare **(attività)** per visualizzare le azioni dell'utente su risorse di Azure.
![Dettagli attività](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Altri dettagli attività](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Controllo personale

Controllo personale offre una visualizzazione delle attività personali del ruolo dell'utente. È possibile filtrare le informazioni usando una data predefinita o un intervallo personalizzato.
![Attività del ruolo personale](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visualizzare l'attivazione e l'attività di Risorse di Azure

Se è necessario verificare quali azioni sono state effettuate da un utente specifico su diverse risorse, è possibile esaminare l'attività di Risorse di Azure associata a un periodo di attivazione specifico. Selezionare prima di tutto un utente dalla visualizzazione **Membri** o dall'elenco di membri in un ruolo specifico. Il risultato è una visualizzazione grafica delle azioni dell'utente in Risorse di Azure divise per data. Mostra inoltre le attivazioni di ruolo recenti nell’arco dello stesso periodo di tempo.

![Dettagli utente](media/azure-pim-resource-rbac/rbac-user-details.png)

Se si seleziona un'attivazione di ruolo specifica, verranno visualizzati i dettagli relativi all'attivazione del ruolo e l'attività di Risorse di Azure corrispondente effettuata nel periodo in cui l'utente è risultato attivo.

![Selezionare l'attivazione del ruolo](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

