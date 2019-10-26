---
title: Usare un dashboard delle risorse per eseguire una verifica dell'accesso in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Questo articolo descrive come usare un dashboard delle risorse per eseguire una verifica di accesso in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: de2c04a0ea109046848d443db2e6ab9a9dd21ee0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895582"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Usare un dashboard delle risorse per eseguire una verifica dell'accesso in Privileged Identity Management

È possibile usare un dashboard delle risorse per eseguire una verifica dell'accesso in Privileged Identity Management (PIM). Il dashboard della visualizzazione amministratore in Azure Active Directory (Azure AD) ha tre componenti principali:

- Rappresentazione grafica delle attivazioni dei ruoli delle risorse
- Grafici che visualizzano la distribuzione delle assegnazioni di ruolo in base al tipo di assegnazione
- Un'area dati contenente informazioni alle nuove assegnazioni di ruolo

![Screenshot del dashboard Visualizzazione amministratore che mostra grafici e diagrammi](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Screenshot del dashboard Visualizzazione amministratore che mostra elenchi di dati](media/pim-resource-roles-overview-dashboards/role-settings.png)

La rappresentazione grafica delle attivazioni del ruolo delle risorse copre gli ultimi sette giorni. Come ambito per questi dati viene specificata la risorsa selezionata e vengono visualizzate attivazioni per i ruoli più comuni (proprietario, collaboratore, amministratore accesso utenti) e per tutti i ruoli combinati.

Su uno dei lati del grafico attivazioni, due grafici visualizzano la distribuzione delle assegnazioni di ruolo in base al tipo di assegnazione, sia per gli utenti che per i gruppi. È possibile cambiare il valore in una percentuale o viceversa selezionando una sezione del grafico.

Sotto i grafici vengono elencati il numero di utenti e gruppi con nuove assegnazioni di ruolo negli ultimi 30 giorni e i ruoli ordinati in base alle assegnazioni totali in ordine decrescente.

## <a name="next-steps"></a>Passaggi successivi

- [Avviare una verifica di accesso per i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-start-access-review.md)
