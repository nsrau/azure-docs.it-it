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
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f9d819533b97a126a324ab867b7185fd6415847
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851976"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Esercitazione: configurare Zendesk per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Zendesk e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e gruppi in Zendesk.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere automatizzare il provisioning e il deprovisioning [utenti in applicazioni SaaS (software-as-a-Service) con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che siano disponibili:

* Un tenant di Azure AD.
* Un tenant Zendesk con il piano Professional o superiore abilitato.
* Un account utente in Zendesk con autorizzazioni di amministratore.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Aggiungere Zendesk da Azure Marketplace

Prima di configurare Zendesk per il provisioning utenti automatico con Azure AD, aggiungere Zendesk da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Zendesk dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![Icona Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Zendesk** e selezionare **Zendesk** nel pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Zendesk nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Assegnare gli utenti a Zendesk

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a zendesk. Per assegnare gli utenti o i gruppi a Zendesk, seguire le istruzioni riportate in [assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Suggerimenti importanti per l'assegnazione di utenti a Zendesk

* Attualmente, i ruoli Zendesk vengono popolati automaticamente e dinamicamente nell'interfaccia utente di portale di Azure. Prima di assegnare i ruoli di Zendesk agli utenti, verificare che venga completata una sincronizzazione iniziale con Zendesk per recuperare i ruoli più recenti nel tenant di zendesk.

* È consigliabile assegnare un singolo utente Azure AD a Zendesk per testare la configurazione iniziale del provisioning utenti automatico. È possibile assegnare altri utenti o gruppi in un secondo momento dopo che i test hanno avuto esito positivo.

* Quando si assegna un utente a Zendesk, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurare il provisioning utenti automatico in Zendesk 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in Zendesk in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare l'accesso Single Sign-on basato su SAML per zendesk. Seguire le istruzioni riportate nell' [esercitazione sull'accesso Single Sign-on di Zendesk](zendesk-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurare il provisioning utenti automatico per Zendesk in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni** > aziendali**tutte le applicazioni** > **Zendesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Zendesk**.

    ![Collegamento di Zendesk nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Nella sezione **credenziali amministratore** immettere il nome utente amministratore, il token segreto e il dominio dell'account zendesk. Ecco alcuni esempi di questi valori:

   * Nella casella **nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di zendesk. Un esempio è admin@contoso.com.

   * Nella casella **token segreto** compilare il token Secret come descritto nel passaggio 6.

   * Nella casella **dominio** compilare il sottodominio del tenant di zendesk. Per un account con URL `https://my-tenant.zendesk.com`tenant, ad esempio, il sottodominio è **My-tenant**.

6. Il token segreto per l'account Zendesk si trova in**Impostazioni** **API** > di **Amministrazione** > . Assicurarsi che **l'accesso ai token** sia impostato su **abilitato**.

    ![Impostazioni amministratore di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token Zendesk Secret](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Dopo aver compilato le caselle visualizzate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a zendesk. Se la connessione non riesce, verificare che l'account Zendesk disponga delle autorizzazioni di amministratore e riprovare.

    ![Connessione test Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

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

14. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'esercitazione relativa al [filtro di ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning Azure AD per Zendesk, nella sezione **Impostazioni** impostare **stato** del provisioning **su**attivato.

    ![Stato del provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Zendesk. Nella sezione **Impostazioni** selezionare i valori desiderati nell' **ambito**.

    ![Ambito Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salva Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nell' **ambito** nella sezione **Impostazioni** . La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti a condizione che venga eseguito il servizio di provisioning Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning Azure AD in Zendesk.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Zendesk supporta l'utilizzo di gruppi per gli utenti con solo ruoli di **Agent** . Per ulteriori informazioni, vedere la [documentazione di Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando un ruolo personalizzato viene assegnato a un utente o a un gruppo, il Azure AD servizio di provisioning utenti automatico assegna anche l' **agente**ruolo predefinito. Solo agli agenti può essere assegnato un ruolo personalizzato. Per ulteriori informazioni, vedere la [documentazione dell'API Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
