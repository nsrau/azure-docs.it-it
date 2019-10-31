---
title: Creare aree di lavoro per i portali app personali in Azure Active Directory | Microsoft Docs
description: Usare le aree di lavoro app personali per personalizzare le pagine delle app per un'esperienza di app personali più semplice per gli utenti finali. Organizzare le applicazioni in gruppi con schede separate.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199834"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Creare aree di lavoro nel portale app personali (anteprima)

Gli utenti possono usare il portale app personali (anteprima) per visualizzare e avviare le applicazioni basate sul cloud a cui hanno accesso. Per impostazione predefinita, tutte le applicazioni a cui un utente può accedere sono elencate insieme in un'unica pagina. Per organizzare meglio questa pagina per gli utenti, se si dispone di una licenza Azure AD Premium P1 o P2 è possibile configurare le aree di lavoro. Con un'area di lavoro, è possibile raggruppare le applicazioni correlate, ad esempio per ruolo, attività o progetto, e visualizzarle in una scheda separata. Un'area di lavoro applica essenzialmente un filtro alle applicazioni a cui un utente può già accedere, in modo che l'utente veda solo le applicazioni nell'area di lavoro a cui sono state assegnate.

> [!NOTE]
> Questo articolo illustra come un amministratore può abilitare e creare aree di lavoro. Per informazioni sull'utente finale su come usare il portale e le aree di lavoro delle app personali, vedere [accedere e usare le aree di lavoro](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-my-apps-preview-features"></a>Abilita le funzionalità di anteprima delle app personali

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore utente o amministratore globale.

2. Passare a **Azure Active Directory** > **impostazioni utente**.

3. In **anteprime funzionalità utente**selezionare **Gestisci impostazioni anteprima funzionalità utente**.

4. In **utenti possono usare le funzionalità di anteprima per le app personali**, scegliere una delle opzioni seguenti:
   * **Selezionato** : Abilita le funzionalità di anteprima per un gruppo specifico. Utilizzare l'opzione **Seleziona un gruppo** per selezionare il gruppo per il quale si desidera abilitare le funzionalità di anteprima.  
   * **All** : Abilita le funzionalità di anteprima per tutti gli utenti.

   ![Funzionalità di anteprima utente](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> Per aprire il portale app personali, gli utenti possono usare il collegamento `https://myapps.microsoft.com` o il collegamento personalizzato per l'organizzazione, ad esempio `https://myapps.microsoft.com/contoso.com`. Se gli utenti non vengono reindirizzati alla versione di anteprima delle app personali, gli utenti devono provare `https://myapplications.microsoft.com` o `https://myapplications.microsoft.com/contoso.com`.

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per creare un'area di lavoro, è necessario disporre di una licenza Azure AD Premium P1 o P2.

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore con una licenza Azure ad Premium P1 o P2.

2. Passare a **Azure Active Directory** > **applicazioni aziendali**.

3. In **Gestisci**selezionare **aree di lavoro (anteprima)** .

4. Selezionare **nuova area di lavoro**. Nella pagina **nuova area di lavoro** immettere un **nome** per l'area di lavoro. si consiglia di non usare "area di lavoro" nel nome. Immettere quindi una **Descrizione**.

   ![Creazione di una nuova area di lavoro](media/access-panel-workspaces/new-workspace.png)

5. Selezionare **Rivedi e crea**. Verranno visualizzate le proprietà per la nuova area di lavoro.

6. Selezionare la scheda **applicazioni** . In **Aggiungi applicazioni**selezionare tutte le applicazioni che si desidera aggiungere all'area di lavoro oppure utilizzare la casella di **ricerca** per trovare le applicazioni. 

   ![Aggiungere applicazioni all'area di lavoro](media/access-panel-workspaces/add-applications.png)

7. Selezionare **Aggiungi**. Verrà visualizzato l'elenco delle applicazioni selezionate. È possibile utilizzare le frecce su e giù per modificare l'ordine delle applicazioni nell'elenco.

   ![Elenco di applicazioni nell'area di lavoro](media/access-panel-workspaces/add-applications-list.png)

8. Selezionare la scheda **utenti e gruppi** . Per aggiungere un utente o un gruppo, selezionare **Aggiungi utente**. 

9. Nella pagina **Seleziona membri** selezionare gli utenti o i gruppi a cui si vuole assegnare l'area di lavoro. In alternativa, utilizzare la casella di **ricerca** per trovare utenti o gruppi.

   ![Assegnare utenti e gruppi all'area di lavoro](media/access-panel-workspaces/add-users-and-groups.png)

10. Al termine della selezione di utenti e gruppi, scegliere **Seleziona**.

11. Per modificare il ruolo di un utente dall' **accesso in lettura** al **proprietario** o viceversa, fare clic sul ruolo corrente e selezionare un nuovo ruolo.

    ![Assegnare ruoli a utenti e gruppi](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Visualizzare i log di controllo

I log di controllo registrano le operazioni dell'area di lavoro app, incluse le azioni dell'utente finale per la creazione dell'area di lavoro Gli eventi seguenti vengono generati dalle app personali:

* Creare un'area di lavoro
* Modifica area di lavoro
* Elimina area di lavoro
* Avviare un'applicazione (utente finale)
* Aggiunta di applicazioni self-service (utente finale)
* Eliminazione di applicazioni self-service (utente finale)

È possibile accedere ai log di controllo nel [portale di Azure](https://portal.azure.com) selezionando **Azure Active Directory** > **applicazioni aziendali** > **log di controllo** nella sezione attività. Per **servizio**selezionare **app personali**.

   ![Assegnare ruoli a utenti e gruppi](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>Passaggi successivi
[Esperienza dell'utente finale per le applicazioni in Azure Active Directory](end-user-experiences.md)