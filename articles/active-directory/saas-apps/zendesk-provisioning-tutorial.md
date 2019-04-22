---
title: 'Esercitazione: configurare Zendesk per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf747fb75ea663d2c64038d73f48adb19d9fb804
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278582"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Esercitazione: configurare Zendesk per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Zendesk e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il de-provisioning di utenti e/o gruppi in Zendesk.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* Tenant di Zendesk con il piano [Enterprise](https://www.zendesk.com/product/pricing/) o superiore abilitato
* Account utente in Zendesk con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API REST di Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), disponibile per i team di Zendesk con piano Enterprise o superiore.

## <a name="adding-zendesk-from-the-gallery"></a>Aggiunta di Zendesk dalla raccolta

Prima di configurare Zendesk per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zendesk dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Zendesk dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zendesk**, selezionare **Zendesk** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zendesk nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-zendesk"></a>Assegnazione di utenti a Zendesk

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Zendesk. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Zendesk seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Suggerimenti importanti per l'assegnazione di utenti a Zendesk

* I ruoli di Zendesk vengono popolati oggi stesso in modo automatico e dinamico nell'interfaccia utente del portale di Azure. Prima di assegnare i ruoli Zendesk agli utenti, assicurarsi di completare una sincronizzazione iniziale con Zendesk per recuperare gli ultimi ruoli nel tenant Zendesk.

* È consigliabile assegnare un singolo utente di Azure AD a Zendesk per testare la configurazione iniziale del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento, una volta completati i test.
  
* È consigliabile assegnare un singolo utente di Azure AD a Zendesk per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Zendesk, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Configurazione del provisioning utenti automatico in Zendesk 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Zendesk in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Zendesk, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Zendesk](zendesk-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Zendesk in Azure AD:

1. Accedi per il [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **Zendesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Zendesk**.

    ![Collegamento di Zendesk nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Nella sezione **Credenziali amministratore** completare i campi **Nome utente amministratore**, **Token segreto** e **Dominio** dell'account Zendesk. Ecco alcuni esempi di questi valori:

   * Nel campo **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di Zendesk. Esempio: admin@contoso.com.

   * Nel campo **Token segreto** immettere il token del segreto come descritto nel passaggio 6.

   * Nel campo **Dominio** immettere il sottodominio del tenant di Zendesk.
     Esempio: per un account con un URL del tenant `https://my-tenant.zendesk.com`, il sottodominio sarà **tenant**.

6. Il valore di **Token segreto** per l'account Zendesk si trova in **Amministratore > API > Impostazioni**.
   Verificare che **Token Access** (Accesso tramite token) sia impostato su **Abilitato**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Zendesk. Se la connessione non riesce, verificare che l'account Zendesk abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Fare clic su **Save**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zendesk** (Sincronizza utenti di Azure Active Directory con Zendesk).

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Zendesk nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zendesk per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to ZenDesk** (Sincronizza gruppi di Azure Active Directory con Zendesk).

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Zendesk nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zendesk per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Zendesk, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Zendesk selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Zendesk.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Zendesk supporta l'utilizzo dei gruppi solo per gli utenti con il ruolo di agente. Per altre informazioni, vedere la [documentazione di Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando un ruolo personalizzato viene assegnato a un utente e/o gruppo, il servizio di provisioning utenti automatico Azure AD assegnerà anche il ruolo predefinito **Agente**. È possibile assegnare un ruolo predefinito solo agli **Agenti**. Per altre informazioni, vedere la [Documentazione dell'API Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
