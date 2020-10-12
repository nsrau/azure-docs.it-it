---
title: Creare raccolte per i portali delle app personali in Azure Active Directory | Microsoft Docs
description: Usare le raccolte di app personali per personalizzare le pagine delle app per un'esperienza di app personali più semplice per gli utenti finali. Organizzare le applicazioni in gruppi con schede separate.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f520141d36726e94dc8d49d7e5aa95bb35d5484
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85956237"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Creare le raccolte nel portale App personali

Gli utenti possono usare il portale app personali per visualizzare e avviare le applicazioni basate sul cloud a cui hanno accesso. Per impostazione predefinita, tutte le applicazioni a cui un utente può accedere sono elencate insieme in un'unica pagina. Per organizzare meglio questa pagina per gli utenti, se si dispone di una licenza Azure AD Premium P1 o P2, è possibile configurare le raccolte. Con una raccolta è possibile raggruppare le applicazioni correlate, ad esempio per ruolo, attività o progetto, e visualizzarle in una scheda separata. Una raccolta applica essenzialmente un filtro alle applicazioni a cui un utente può già accedere, in modo che l'utente veda solo le applicazioni nella raccolta che sono state assegnate.

> [!NOTE]
> Questo articolo illustra come un amministratore può abilitare e creare raccolte. Per informazioni sull'uso del portale e delle raccolte di app personali da parte dell'utente finale, vedere [Access and use Collections](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-the-latest-my-apps-features"></a>Abilita le funzionalità delle app personali più recenti

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore utente o amministratore globale.

2. Passare a **Azure Active Directory**  >  **impostazioni utente**.

3. In **anteprime funzionalità utente**selezionare **Gestisci impostazioni anteprima funzionalità utente**.

4. In **utenti possono usare le funzionalità di anteprima per le app personali**, scegliere una delle opzioni seguenti:
   * **Selezionato** : Abilita le funzionalità per un gruppo specifico. Utilizzare l'opzione **Seleziona un gruppo** per selezionare il gruppo per il quale si desidera abilitare le funzionalità.  
   * **All** : Abilita le funzionalità per tutti gli utenti.

> [!NOTE]
> Per aprire il portale app personali, gli utenti possono usare il collegamento `https://myapps.microsoft.com` o il collegamento personalizzato per l'organizzazione, ad esempio `https://myapps.microsoft.com/contoso.com` . Dopo aver abilitato la nuova esperienza app personali, il banner **disponibile nell'esperienza applicazioni personali viene** visualizzato nella parte superiore della pagina App personali e gli utenti possono selezionare **prova** per visualizzare la nuova esperienza. Per interrompere l'uso della nuova esperienza, gli utenti possono selezionare **Sì** nel banner **lascia nuova esperienza** nella parte superiore della pagina.

## <a name="create-a-collection"></a>Creare una raccolta

Per creare una raccolta, è necessario disporre di una licenza Azure AD Premium P1 o P2.

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore con una licenza Azure ad Premium P1 o P2.

2. Passare a **Azure Active Directory**  >  **applicazioni aziendali**.

3. In **Gestisci**selezionare **raccolte**.

4. Selezionare **nuova raccolta**. Nella pagina **nuovo insieme** immettere un **nome** per la raccolta. si consiglia di non usare "raccolta" nel nome. Immettere quindi una **Descrizione**.

   ![Pagina nuova raccolta](media/acces-panel-collections/new-collection.png)

5. Selezionare la scheda **applicazioni** . Selezionare **+ Aggiungi applicazione**, quindi nella pagina **Aggiungi applicazioni** Selezionare tutte le applicazioni che si desidera aggiungere alla raccolta oppure utilizzare la casella di **ricerca** per trovare le applicazioni.

   ![Aggiungere un'applicazione alla raccolta](media/acces-panel-collections/add-applications.png)

6. Al termine dell'aggiunta di applicazioni, selezionare **Aggiungi**. Verrà visualizzato l'elenco delle applicazioni selezionate. È possibile utilizzare le frecce su per modificare l'ordine delle applicazioni nell'elenco. Per spostare un'applicazione verso il basso o per eliminarla dalla raccolta, selezionare il menu **altro** (**...**).

7. Selezionare la scheda **proprietari** . Selezionare **+ Aggiungi utenti e gruppi**, quindi nella pagina **Aggiungi utenti e gruppi** Selezionare gli utenti o i gruppi a cui si vuole assegnare la proprietà. Al termine della selezione di utenti e gruppi, scegliere **Seleziona**.

9. Selezionare la scheda **utenti e gruppi** . Selezionare **+ Aggiungi utenti e gruppi**, quindi nella pagina **Aggiungi utenti e gruppi** Selezionare gli utenti o i gruppi a cui si vuole assegnare la raccolta. In alternativa, utilizzare la casella di **ricerca** per trovare utenti o gruppi. Al termine della selezione di utenti e gruppi, scegliere **Seleziona**.

   ![Aggiungere utenti e gruppi](media/acces-panel-collections/add-users-and-groups.png)

11. Selezionare **Rivedi e crea**. Verranno visualizzate le proprietà per la nuova raccolta.


## <a name="view-audit-logs"></a>Visualizzare i log di controllo

I log di controllo registrano le operazioni raccolte di app personali, incluse le azioni dell'utente finale per la creazione della raccolta. Gli eventi seguenti vengono generati dalle app personali:

* Crea raccolta
* Modifica la raccolta
* Elimina raccolta
* Avviare un'applicazione (utente finale)
* Aggiunta di applicazioni self-service (utente finale)
* Eliminazione di applicazioni self-service (utente finale)

È possibile accedere ai log di controllo nel [portale di Azure](https://portal.azure.com) selezionando **Azure Active Directory**  >  log di controllo delle**applicazioni aziendali**  >  **Audit logs** nella sezione attività. Per **servizio**selezionare **app personali**.

## <a name="get-support-for-my-account-pages"></a>Ottenere supporto per le pagine account personali

Dalla pagina My Apps (app personali), un utente può selezionare **My account**  >  **View My** account per aprire le impostazioni dell'account. Nella pagina Azure AD **account personale** , gli utenti possono gestire le informazioni di sicurezza, i dispositivi, le password e altro ancora. Possono anche accedere alle impostazioni dell'account Office.

Se è necessario inviare una richiesta di supporto per un problema con la pagina account Azure AD o con l'account di Office, attenersi alla procedura seguente per instradare correttamente la richiesta: 

* Per i problemi relativi alla **Azure ad pagina "account personale"** , aprire una richiesta di supporto dall'interno del portale di Azure. Passare a **portale di Azure**  >  **Azure Active Directory**  >  **nuova richiesta di supporto**.

* Per i problemi relativi alla pagina **"account personale" di Office** , aprire una richiesta di supporto dall'interfaccia di amministrazione di Microsoft 365. Passare a **Microsoft 365**supporto dell'interfaccia di amministrazione  >  **Support**. 

## <a name="next-steps"></a>Passaggi successivi
[Esperienze per gli utenti finali per le applicazioni in Azure Active Directory](end-user-experiences.md)