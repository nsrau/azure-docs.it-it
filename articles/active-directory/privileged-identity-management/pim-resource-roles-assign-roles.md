---
title: Privileged Identity Management per risorse di Azure - Assegnare ruoli | Microsoft Docs
description: Descrive come assegnare i ruoli in Privileged Identity Management.
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
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - Ruoli delle risorse - Assegnazione

## <a name="assign-roles"></a>Assegnare ruoli

Per assegnare un utente o un gruppo a un ruolo, selezionare il ruolo (se è attiva la visualizzazione dei ruoli). 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

In alternativa fare clic su Aggiungi sulla barra delle azioni (se è attiva la visualizzazione Membri).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Se si aggiunge un utente o gruppo dalla scheda Membri, è necessario: 

1. Scegliere un ruolo dal menu Aggiungi prima di poter selezionare un utente o un gruppo.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Scegliere un utente o un gruppo dalla directory.

3. Scegliere il tipo di assegnazione appropriato dal menu a discesa. 

    - **Assegnazione Just-In-Time:** fornisce all'utente o ai membri del gruppo un accesso idoneo ma non permanente al ruolo per un periodo di tempo specificato o illimitato, se configurato nelle impostazioni del ruolo. 
    - **Assegnazione diretta:** non richiede all'utente o ai membri del gruppo di attivare l'assegnazione di ruolo ed è definito accesso permanente. Microsoft consiglia di usare l'assegnazione diretta per l'uso a breve termine, ad esempio per turni con reperibilità o attività che dipendono da un orario specifico, ovvero nei casi in cui l'accesso non sarà richiesto al termine dell'attività.

Una casella di controllo sotto l'elenco a discesa del tipo di assegnazione consente di specificare se l'assegnazione deve essere permanente, ovvero permanentemente idonea per l'attivazione dell'assegnazione Just-In-Time o permanentemente attiva per l'assegnazione diretta.

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>È possibile che la casella di controllo non sia modificabile se un altro amministratore ha specificato la durata massima dell'assegnazione per ogni tipo di assegnazione nelle impostazioni del ruolo.

 Per indicare una durata specifica per l'assegnazione, deselezionare la casella di controllo e modificare i campi relativi a data e ora di inizio e/o fine.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Gestire le assegnazioni di ruoli

Gli amministratori possono gestire le assegnazioni di ruolo selezionando Ruoli o Membri dal menu di spostamento a sinistra. La selezione di Ruoli consente agli amministratori di specificare un ruolo specifico come ambito per le rispettive attività di gestione, mentre l'opzione Membri mostra tutte le assegnazioni di ruolo per utenti e gruppi per la risorsa.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Se è presente un'attivazione di ruolo in sospeso, un banner di notifica viene visualizzato nella parte superiore della pagina quando si visualizza l'appartenenza.


## <a name="modify-existing-assignments"></a>Modificare le assegnazioni esistenti

Per modificare le assegnazioni esistenti dalla visualizzazione dei dettagli relativi a utenti/gruppi, selezionare Modifica le impostazioni dalla barra delle azioni nella parte superiore della pagina. Modificare il tipo di assegnazione in assegnazione Just-In-Time o assegnazione diretta.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
