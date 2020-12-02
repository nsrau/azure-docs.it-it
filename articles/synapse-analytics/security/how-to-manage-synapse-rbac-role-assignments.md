---
title: Come gestire le assegnazioni RBAC RBAC in sinapsi Studio
description: Questo articolo descrive come assegnare e revocare i ruoli RBAC per le entità di sicurezza di AAD
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: a4016751944e5b7ec5d32dc586e9034db99c9d73
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523558"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Come gestire le assegnazioni di ruolo RBAC in sinapsi Studio

Il controllo degli accessi in base al ruolo consente di assegnare autorizzazioni a utenti, gruppi e altre entità di sicurezza per consentire l'accesso e l'uso delle risorse e degli elementi di codice della sinapsi.  [Altre informazioni](./synapse-workspace-synapse-rbac.md)

Questo articolo illustra come aggiungere ed eliminare le assegnazioni di ruolo RBAC RBAC.

>[!Note]
>- Per gestire le assegnazioni di ruolo RBAC RBAC è necessario avere il ruolo di amministratore sinapsi nell'area di lavoro o in un ambito di livello inferiore che includa gli oggetti da gestire. Se si è un amministratore di sinapsi nell'area di lavoro, è possibile concedere l'accesso a tutti gli oggetti nell'area di lavoro. 
>- Per consentire di riottenere l'accesso a un'area di lavoro nel caso in cui non sia stato assegnato o disponibile alcun amministratore sinapsi, gli utenti che dispongono delle autorizzazioni per gestire le assegnazioni di ruolo **RBAC di Azure** nell'area di lavoro possono gestire anche le assegnazioni di ruolo controllo degli accessi in base **al ruolo,** consentendo l'aggiunta di un amministratore di sinapsi o altre assegnazioni di ruolo
>- L'accesso ai pool SQL viene gestito tramite le autorizzazioni SQL.  Fatta eccezione per i ruoli di amministratore sinapsi e di amministratore SQL sinapsi, i ruoli di controllo degli accessi in base al ruolo sinapsi non concedono l'accesso ai pool SQL.

>[!important]
>- Per rendere effettive le modifiche apportate alle assegnazioni di ruolo di sinapsi RBAC, possono essere necessari 2-5 minuti. 
>- Se si gestiscono le autorizzazioni RBAC RBAC modificando l'appartenenza dei gruppi di sicurezza, le modifiche apportate all'appartenenza vengono gestite con Azure Active Directory.  Per rendere effettive le modifiche apportate all'appartenenza al gruppo potrebbero essere necessari alcuni minuti.

## <a name="open-synapse-studio"></a>Aprire Synapse Studio  

Per assegnare un ruolo a un utente, a un gruppo, a un'entità servizio o a un'identità gestita, aprire prima di tutto [la sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro. 

![Accedi all'area di lavoro](./media/common/login-workspace.png) 
 
 Dopo aver aperto l'area di lavoro, espandere la sezione **sicurezza** a sinistra e selezionare **controllo di accesso**. 

 ![Selezionare controllo di accesso nella sezione sicurezza a sinistra](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

Nella schermata controllo di accesso sono elencate le assegnazioni di ruolo correnti.  È possibile filtrare l'elenco in base al nome dell'entità o al messaggio di posta elettronica e filtrare in modo selettivo i tipi di oggetto, i ruoli o gli ambiti inclusi. Da questa schermata è possibile aggiungere o rimuovere assegnazioni di ruolo.  

## <a name="add-a-synapse-role-assignment"></a>Aggiungere un'assegnazione di ruolo sinapsi

Nella schermata controllo di accesso selezionare **+ Aggiungi** per creare una nuova assegnazione di ruolo

![Fare clic su + Aggiungi per creare una nuova assegnazione di ruolo](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

Nella scheda Aggiungi assegnazione ruolo è possibile creare assegnazioni di ruolo nell'ambito dell'area di lavoro o dell'elemento dell'area di lavoro. 

## <a name="add-workspace-scoped-role-assignment"></a>Aggiunta dell'assegnazione di ruolo con ambito area di lavoro

Selezionare prima di tutto l' **area di lavoro** come ambito, quindi selezionare il **ruolo sinapsi RBAC**.  Selezionare l' **entità** o le entità a cui assegnare il ruolo, quindi creare le assegnazioni di ruolo. 

![Aggiungi assegnazione ruolo area di lavoro-Seleziona ruolo](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

Il ruolo assegnato si applica a tutti gli oggetti applicabili nell'area di lavoro.

## <a name="add-workspace-item-scoped-role-assignment"></a>Aggiungi elemento area di lavoro-assegnazione di ruolo con ambito

Per assegnare un ruolo a un ambito con granularità fine, selezionare **elemento dell'area di lavoro** come ambito, quindi selezionare il **tipo di elemento** ambito.       

![Aggiungi assegnazione ruolo elemento area di lavoro-Seleziona tipo di elemento](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Selezionare l' **elemento** specifico da usare come ambito, quindi selezionare il **ruolo** da assegnare dall'elenco a discesa.  Nell'elenco a discesa sono elencati solo i ruoli validi per il tipo di elemento selezionato. [Altre informazioni](https://go.microsoft.com/fwlink/?linkid=2148306)  

![Aggiungi assegnazione ruolo elemento area di lavoro-Seleziona ruolo](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
**Selezionare quindi l'entità o le entità** a cui deve essere assegnato il ruolo.  È possibile selezionare più entità in modo iterativo.  Selezionare **applica** per creare le assegnazioni di ruolo.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Rimuovere un'assegnazione di ruolo RBAC

Per revocare l'accesso con controllo degli accessi in base al ruolo di sinapsi, è necessario rimuovere le assegnazioni  Nella schermata controllo di accesso usare i filtri per individuare le assegnazioni di ruolo da rimuovere.  Controllare le assegnazioni di ruolo e quindi selezionare **Rimuovi accesso**.   

![Eliminare un'assegnazione di ruolo per rimuovere l'accesso](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Tenere presente che le modifiche apportate alle assegnazioni di ruolo verranno applicate a 2-5 minuti.   

## <a name="next-steps"></a>Passaggi successivi

[Comprendere i ruoli di controllo degli accessi in base al ruolo necessari per eseguire attività comuni](./synapse-workspace-understand-what-role-you-need.md) 
