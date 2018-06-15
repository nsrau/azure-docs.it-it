---
title: Assegnare ruoli per le risorse di Azure usando Privileged Identity Management | Microsoft Docs
description: Descrive come assegnare i ruoli in Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 246467d5eeebd43b8d89d98a30fdfc1c5ca0909a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233656"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Assegnare ruoli per le risorse di Azure usando Privileged Identity Management

## <a name="assign-roles"></a>Assegnare ruoli

Per assegnare un utente o un gruppo a un ruolo quando si visualizza il riquadro **Ruoli**, selezionare il ruolo e quindi **Aggiungi utente**. 

![Riquadro "Ruoli" con il pulsante "Aggiungi utente"](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

È possibile anche selezionare **Aggiungi utente** dal riquadro **Membri**.

![Riquadro "Membri" con il pulsante "Aggiungi utente"](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Se si aggiunge un utente o un gruppo dal riquadro **Membri**, è necessario: 

1. Scegliere un ruolo dal riquadro **Selezionare un ruolo** prima di poter selezionare un utente o un gruppo.

   ![Riquadro "Selezionare un ruolo"](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Scegliere un utente o un gruppo dalla directory.

3. Scegliere il tipo di assegnazione appropriato dal menu a discesa: 

   - **Just-In-Time**: fornisce all'utente o ai membri del gruppo un accesso idoneo ma non permanente al ruolo per un periodo di tempo specificato o illimitato, se configurato nelle impostazioni del ruolo. 
   - **Diretto**: non richiede all'utente o ai membri del gruppo di attivare l'assegnazione di ruolo ed è noto come accesso permanente. Microsoft consiglia di usare l'assegnazione di tipo diretto per l'uso a breve termine, ovvero nei casi in cui l'accesso non sarà richiesto al completamento dell'attività. Un esempi sono i turni con reperibilità o le attività urgenti.

4. Se l'assegnazione deve essere permanente, ovvero permanentemente idonea per un'assegnazione Just-In-Time o permanentemente attiva per un'assegnazione di tipo diretto, selezionare la casella di controllo sotto la casella **Tipo di assegnazione**.

   ![Riquadro "Impostazioni di appartenenza" con la casella "Tipo di assegnazione" e la casella di controllo correlata](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >È possibile che la casella di controllo non sia modificabile se un altro amministratore ha specificato la durata massima dell'assegnazione per ogni tipo di assegnazione nelle impostazioni del ruolo.

   Per indicare una durata specifica per l'assegnazione, deselezionare la casella di controllo e modificare le caselle relative a data e ora di inizio e/o fine.

   ![Riquadro "Impostazioni di appartenenza" con le caselle per la data e l'ora di inizio e di fine](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Gestire le assegnazioni di ruoli

Gli amministratori possono gestire le assegnazioni di ruoli selezionando **Ruoli** o **Membri** dal riquadro sinistro. La selezione di **Ruoli** consente agli amministratori di definire l'ambito delle attività di gestione a un ruolo specifico. Selezionando **Membri** vengono visualizzate tutte le assegnazioni di ruoli utente e di gruppo per la risorsa.

![Riquadro "Ruoli"](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![Riquadro "Membri"](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Se è presente un'attivazione di ruolo in sospeso, viene visualizzato un banner di notifica nella parte superiore della pagina quando si visualizza l'appartenenza.


## <a name="modify-existing-assignments"></a>Modificare le assegnazioni esistenti

Per modificare le assegnazioni esistenti dalla visualizzazione dei dettagli relativi all'utente o al gruppo, selezionare **Modifica le impostazioni** dalla barra delle azioni. Modificare il tipo di assegnazione in **Just-In-Time** o **Diretto**.

![Riquadro "Dettagli utente" con il pulsante "Modifica impostazioni"](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
