---
title: Come esaminare le assegnazioni di ruolo RBAC in sinapsi Studio
description: Questo articolo descrive come esaminare le assegnazioni di ruolo RBAC con sinapsi Studio
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 6d6a0bdb9a6aaa2d9ca75ccd4a6d71e9046bee4a
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523548"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Come esaminare le assegnazioni di ruolo RBAC per le sinapsi

I ruoli di controllo degli accessi in base al ruolo vengono usati per assegnare autorizzazioni a utenti, gruppi e altre entità di sicurezza per consentire l'accesso e l'uso delle risorse sinapsi.  [Altre informazioni](https://go.microsoft.com/fwlink/?linkid=2148306)

Questo articolo illustra come esaminare le assegnazioni di ruolo correnti per un'area di lavoro.

Con qualsiasi ruolo di controllo degli accessi in base al ruolo, è possibile elencare le assegnazioni di ruolo RBAC per tutti gli ambiti, incluse le assegnazioni per gli oggetti a cui non si ha accesso. Solo un amministratore di sinapsi può concedere l'accesso a sinapsi RBAC.   

## <a name="open-synapse-studio"></a>Aprire Synapse Studio  

Per esaminare le assegnazioni di ruolo, aprire prima di tutto [sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro. 

![Accedi all'area di lavoro](./media/common/login-workspace.png) 
 
 Dopo aver aperto l'area di lavoro, selezionare **gestione** Hub a sinistra, quindi espandere la sezione **sicurezza** e selezionare **controllo di accesso**. 

 ![Selezionare controllo di accesso nella sezione sicurezza a sinistra](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Esaminare le assegnazioni di ruolo dell'area di lavoro

La schermata controllo di accesso elenca tutte le assegnazioni di ruolo correnti per l'area di lavoro, raggruppate in base al ruolo. Ogni assegnazione include il nome dell'entità, il tipo di entità, il ruolo e il relativo ambito.

![Schermata di controllo di accesso](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Se a un'entità viene assegnato lo stesso ruolo in ambiti diversi, verranno visualizzate più assegnazioni per il server principale, una per ogni ambito.  

Se un ruolo viene assegnato a un gruppo di sicurezza, verranno visualizzati i ruoli assegnati in modo esplicito al gruppo ma non i ruoli ereditati dai gruppi padre.  

È possibile filtrare l'elenco in base al nome dell'entità o al messaggio di posta elettronica e filtrare selettivamente i tipi di oggetto, i ruoli e gli ambiti. Immettere il nome o l'alias di posta elettronica nel filtro dei nomi per visualizzare i ruoli assegnati all'utente. Solo un amministratore di sinapsi può modificare i ruoli.

>[!Important] 
>Se si è un membro diretto o indiretto di un gruppo a cui sono assegnati ruoli, è possibile che si disponga di autorizzazioni che non vengono visualizzate.

>[!tip]
>È possibile trovare l'appartenenza a gruppi usando Azure Active Directory nel portale di Azure.  

Se si crea una nuova area di lavoro, all'entità servizio MSI dell'area di lavoro viene assegnato automaticamente il ruolo di amministratore sinapsi nell'ambito dell'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Informazioni [su come gestire le assegnazioni di ruolo RBAC per le sinapsi](./how-to-manage-synapse-rbac-role-assignments.md).

Informazioni [sul ruolo necessario per eseguire attività specifiche](./synapse-workspace-understand-what-role-you-need.md)