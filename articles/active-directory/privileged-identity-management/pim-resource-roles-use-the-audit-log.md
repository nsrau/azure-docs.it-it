---
title: Visualizzare la cronologia dei controlli per i ruoli delle risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come visualizzare la cronologia dei controlli per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: c0536423e9640f78149b612ec66b0a07cdcf24bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189312"
---
# <a name="view-audit-history-for-azure-resource-roles-in-pim"></a>Visualizzare la cronologia dei controlli per i ruoli delle risorse di Azure in PIM

L'opzione Controllo delle risorse fornisce una visualizzazione di tutte le attività del ruolo per la risorsa. È possibile filtrare le informazioni usando una data predefinita o un intervallo personalizzato.
![Filtrare informazioni](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Il controllo delle risorse consente anche l'accesso rapido ai dettagli delle attività di un utente. Sotto **Tipo di controllo**, selezionare **Activate**. Selezionare **(attività)** per visualizzare le azioni dell'utente su risorse di Azure.
![Dettagli attività](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Altri dettagli attività](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="my-audit"></a>Controllo personale

Controllo personale offre una visualizzazione delle attività personali del ruolo dell'utente. È possibile filtrare le informazioni usando una data predefinita o un intervallo personalizzato.
![Attività del ruolo personale](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visualizzare l'attivazione e l'attività delle risorse di Azure

Se è necessario verificare quali azioni sono state effettuate da un utente specifico su diverse risorse, è possibile esaminare l'attività di Risorse di Azure associata a un periodo di attivazione specifico. Selezionare prima di tutto un utente dalla visualizzazione **Membri** o dall'elenco di membri in un ruolo specifico. Il risultato è una visualizzazione grafica delle azioni dell'utente in Risorse di Azure divise per data. Mostra inoltre le attivazioni di ruolo recenti nell’arco dello stesso periodo di tempo.

![Dettagli utente](media/azure-pim-resource-rbac/rbac-user-details.png)

Se si seleziona un'attivazione di ruolo specifica, verranno visualizzati i dettagli relativi all'attivazione del ruolo e l'attività di Risorse di Azure corrispondente effettuata nel periodo in cui l'utente è risultato attivo.

![Selezionare l'attivazione del ruolo](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare la cronologia dei controlli per i ruoli della directory di Azure AD in PIM](pim-how-to-use-audit-log.md)
