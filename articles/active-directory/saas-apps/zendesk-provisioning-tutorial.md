---
title: 'Esercitazione: Configurare Zendesk per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zendesk.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 5c038cc309d8f1f13a04de01ed83e04afa70ba6d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546169"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zendesk per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Zendesk e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e gruppi in Zendesk.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone i prerequisiti seguenti:

* Un tenant di Azure AD.
* Un tenant Zendesk con il piano Professional o superiore abilitato.
* Un account utente in Zendesk con autorizzazioni di amministratore.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Aggiungere Zendesk da Azure Marketplace

Prima di configurare Zendesk per il provisioning utenti automatico con Azure AD, aggiungere Zendesk da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Zendesk dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Zendesk** e selezionare **Zendesk** nel pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Zendesk nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Assegnare gli utenti a Zendesk

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a zendesk. Per assegnare gli utenti o i gruppi a Zendesk, seguire le istruzioni riportate in [assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Suggerimenti importanti per l'assegnazione di utenti a Zendesk

* Attualmente, i ruoli Zendesk vengono popolati automaticamente e dinamicamente nell'interfaccia utente di portale di Azure. Prima di assegnare i ruoli di Zendesk agli utenti, verificare che venga completata una sincronizzazione iniziale con Zendesk per recuperare i ruoli più recenti nel tenant di zendesk.

* È consigliabile assegnare un singolo utente Azure AD a Zendesk per testare la configurazione iniziale del provisioning utenti automatico. È possibile assegnare altri utenti o gruppi in un secondo momento dopo che i test hanno avuto esito positivo.

* Quando si assegna un utente a Zendesk, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurare il provisioning utenti automatico in Zendesk 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in Zendesk in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare la Single Sign-On basata su SAML per zendesk. Seguire le istruzioni riportate nell' [esercitazione Zendesk Single Sign-on](zendesk-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurare il provisioning utenti automatico per Zendesk in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**  >  **tutte le applicazioni**  >  **Zendesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zendesk**.

    ![Collegamento di Zendesk nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Nella sezione **credenziali amministratore** immettere il nome utente amministratore, il token segreto e il dominio dell'account zendesk. Ecco alcuni esempi di questi valori:

   * Nella casella **nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di zendesk. Un esempio è admin@contoso.com.

   * Nella casella **token segreto** compilare il token Secret come descritto nel passaggio 6.

   * Nella casella **dominio** compilare il sottodominio del tenant di zendesk. Per un account con URL tenant, ad esempio `https://my-tenant.zendesk.com` , il sottodominio è **My-tenant**.

6. Il token segreto per l'account Zendesk si trova in **Admin**  >  **API**  >  **Impostazioni**API di amministrazione. Assicurarsi che **l'accesso ai token** sia impostato su **abilitato**.

    ![Impostazioni amministratore di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token Zendesk Secret](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Dopo aver compilato le caselle visualizzate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a zendesk. Se la connessione non riesce, verificare che l'account Zendesk disponga delle autorizzazioni di amministratore e riprovare.

    ![Connessione test Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo e-mail di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Posta elettronica di notifica Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zendesk** (Sincronizza utenti di Azure Active Directory con Zendesk).

    ![Sincronizzazione utente Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Zendesk nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zendesk per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Zendesk attributi utente corrispondenti](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Zendesk**.

    ![Sincronizzazione del gruppo Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Zendesk nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zendesk per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi di gruppo corrispondenti Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning Azure AD per Zendesk, nella sezione **Impostazioni** impostare **stato del provisioning** **su**attivato.

    ![Stato del provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Zendesk. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Ambito Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Salva Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti nell'**Ambito** della sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Le sincronizzazioni successive vengono eseguite circa ogni 40 minuti durante l'esecuzione del servizio di provisioning di Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report dell'attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning Azure AD in Zendesk.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Zendesk supporta l'utilizzo di gruppi per gli utenti con solo ruoli di **Agent** . Per ulteriori informazioni, vedere la [documentazione di Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando un ruolo personalizzato viene assegnato a un utente o a un gruppo, il Azure AD servizio di provisioning utenti automatico assegna anche l' **agente**ruolo predefinito. È possibile assegnare un ruolo predefinito solo agli Agenti. Per ulteriori informazioni, vedere la [documentazione dell'API Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
