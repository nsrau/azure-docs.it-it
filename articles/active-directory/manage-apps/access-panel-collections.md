---
title: Creare raccolte per i portali delle app personali in Azure Active Directory Documenti Microsoft
description: Usare le raccolte di app personali per personalizzare le pagine delle app personali per un'esperienza più semplice per gli utenti finali. Organizza le applicazioni in gruppi con schede separate.
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
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120091"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Creare raccolte nel portale app personali

Gli utenti possono usare il portale App personali per visualizzare e avviare le applicazioni basate su cloud a cui hanno accesso. Per impostazione predefinita, tutte le applicazioni a cui un utente può accedere sono elencate insieme in un'unica pagina. Per organizzare meglio questa pagina per gli utenti, se si dispone di una licenza di Azure AD Premium P1 o P2 è possibile configurare le raccolte. Con una raccolta, è possibile raggruppare le applicazioni correlate (ad esempio, per ruolo di lavoro, attività o progetto) e visualizzarle in una scheda separata. Una raccolta applica essenzialmente un filtro alle applicazioni a cui un utente può già accedere, in modo che l'utente visualizzi solo le applicazioni nella raccolta che sono state loro assegnate.

> [!NOTE]
> Questo articolo illustra come un amministratore può abilitare e creare raccolte. Per informazioni per l'utente finale su come utilizzare il portale e le raccolte di app personali, vedere [Accedere e utilizzare le raccolte](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-the-latest-my-apps-features"></a>Abilitare le funzionalità più recenti di App personali

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come amministratore utente o amministratore globale.

2. Passare a**Impostazioni utente** **di Azure Active Directory** > .

3. In **Anteprime funzionalità utente**selezionare Gestisci impostazioni di anteprima funzionalità **utente.**

4. In Gli utenti possono utilizzare le funzionalità di **anteprima per Le mie app**scegliere una delle opzioni seguenti:
   * **Selezionato** - Abilita le funzioni per un gruppo specifico. Utilizzare l'opzione **Seleziona un gruppo** per selezionare il gruppo per il quale si desidera abilitare le feature.  
   * **Tutti:** abilita le funzionalità per tutti gli utenti.

> [!NOTE]
> Per aprire il portale App personali, `https://myapps.microsoft.com` gli utenti possono utilizzare il `https://myapps.microsoft.com/contoso.com`collegamento o il collegamento personalizzato per l'organizzazione, ad esempio . Dopo aver abilitato la nuova esperienza Di App personali, il banner **Un'esperienza aggiornata di applicazioni disponibili** verrà visualizzato nella parte superiore della pagina App personali e gli utenti potranno selezionare **Prova** per visualizzare la nuova esperienza. Per interrompere l'utilizzo della nuova esperienza, gli utenti possono selezionare **Sì** dal banner **Lascia nuova esperienza** nella parte superiore della pagina.

## <a name="create-a-collection"></a>Creare una raccolta

Per creare una raccolta, è necessario disporre di una licenza di Azure AD Premium P1 o P2.To create a collection, you must have an Azure AD Premium P1 or P2 license.

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come amministratore con una licenza di Azure AD Premium P1 o P2.

2. Passare ad Applicazioni**aziendali** **di Azure Active Directory** > .

3. In **Gestisci**selezionare **Raccolte**.

4. Selezionare **Nuova raccolta**. Nella pagina **Nuova raccolta** immettere un **Nome** per la raccolta (si consiglia di non usare "raccolta" nel nome. Quindi immettere una **Descrizione**.

   ![Nuova pagina di raccolta](media/acces-panel-collections/new-collection.png)

5. Selezionare la scheda **Applicazioni.** Selezionare **l'opzione Aggiungi applicazione**, quindi nella pagina **Aggiungi applicazioni** selezionare tutte le applicazioni che si desidera aggiungere alla raccolta oppure utilizzare la casella di **ricerca** per trovare le applicazioni.

   ![Aggiungere un'applicazione alla raccoltaAdd an application to the collection](media/acces-panel-collections/add-applications.png)

6. Al termine dell'aggiunta delle applicazioni, selezionare **Aggiungi**. Viene visualizzato l'elenco delle applicazioni selezionate. È possibile utilizzare le frecce verso l'alto per modificare l'ordine delle applicazioni nell'elenco. Per spostare un'applicazione verso il basso o per eliminarla dalla raccolta, selezionare il menu **Altro** (**...**).

7. Selezionare la scheda **Proprietari.** Selezionare **: Aggiungi utenti e gruppi**, quindi nella pagina Aggiungi utenti e **gruppi** selezionare gli utenti o i gruppi a cui si desidera assegnare la proprietà. Dopo aver selezionato utenti e gruppi, scegliere **Seleziona**.

9. Selezionare la scheda **Utenti e gruppi.** Selezionare **Aggiungi utenti e gruppi**, quindi nella pagina Aggiungi utenti e **gruppi** selezionare gli utenti o i gruppi a cui si desidera assegnare la raccolta. In alternativa, utilizzare la casella **di ricerca** per trovare utenti o gruppi. Dopo aver selezionato utenti e gruppi, scegliere **Seleziona**.

   ![Aggiungere utenti e gruppi](media/acces-panel-collections/add-users-and-groups.png)

11. Selezionare **Revisione e creazione**. Vengono visualizzate le proprietà per la nuova raccolta.


## <a name="view-audit-logs"></a>Visualizzare i log di controllo

I registri di controllo registrano le operazioni di raccolta di app personali, incluse le azioni dell'utente finale per la creazione di raccolte. Da My Apps vengono generati i seguenti eventi:

* Crea raccolta
* Modifica la raccolta
* Elimina raccolta
* Avviare un'applicazione (utente finale)
* Aggiunta di applicazioni self-service (utente finale)
* Eliminazione di applicazioni self-service (utente finale)

È possibile accedere ai log di controllo nel portale di Azure selezionando I log di controllo delle applicazioni aziendali di Azure Active Directory nella sezione Attività.You can access audit logs in the [Azure portal](https://portal.azure.com) by selecting **Azure Active Directory** > **Enterprise Applications** > **Audit logs** in the Activity section. Per **Servizio**, selezionare **Le mie applicazioni**.

## <a name="get-support-for-my-account-pages"></a>Ottenere supporto per le pagine del mio account

Dalla pagina App personali, un utente può selezionare **Il mio account** > **Visualizza il mio account** per aprire le impostazioni dell'account. Nella pagina **Account personale** di Azure AD gli utenti possono gestire le informazioni di sicurezza, i dispositivi, le password e altro ancora. Possono anche accedere alle impostazioni dell'account di Office.

Se è necessario inviare una richiesta di supporto per un problema con la pagina dell'account Azure AD o la pagina dell'account di Office, attenersi alla seguente procedura per instradare correttamente la richiesta: 

* Per problemi con la pagina "Account personale" di Azure AD, aprire una richiesta di supporto dal portale di Azure.For issues with the **Azure AD "My Account"** page, open a support request from within the Azure portal. Passare al **portale** > di Azure**Azure Active Directory** > **Richiesta di nuovo supporto**.

* Per problemi con la pagina "Account personale" di Office, aprire una richiesta di supporto dall'interfaccia di amministrazione di Microsoft 365.For issues with the **Office "My account"** page, open a support request from within the Microsoft 365 admin center. Passare al > **supporto dell'interfaccia**di **amministrazione di Microsoft 365**. 

## <a name="next-steps"></a>Passaggi successivi
[Esperienze per gli utenti finali per le applicazioni in Azure Active Directory](end-user-experiences.md)