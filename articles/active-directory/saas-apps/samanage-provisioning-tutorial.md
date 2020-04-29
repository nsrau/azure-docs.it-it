---
title: 'Esercitazione: Configurare Samanage per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060491"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Esercitazione: Configurare Samanage per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in samanage e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e gruppi in samanage.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS (software-as-a-Service) con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che siano disponibili:

* Un tenant di Azure AD.
* Un [tenant](https://www.samanage.com/pricing/) di samanage con il pacchetto professionale.
* Un account utente in samanage con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning Azure AD si basa sull' [API REST](https://www.samanage.com/api/)samanage. Questa API è disponibile per gli sviluppatori di samanage per gli account con il pacchetto Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Aggiungi samanage da Azure Marketplace

Prima di configurare samanage per il provisioning utenti automatico con Azure AD, aggiungere samanage da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere samanage dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![Icona Azure Active Directory](common/select-azuread.png)

2. Passare ad **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di **ricerca immettere** samanage e selezionare samanage nel **Pannello dei risultati** . Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Samanage nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Assegnare gli utenti a samanage

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a samanage. Per assegnare questi utenti o gruppi a samanage, seguire le istruzioni riportate in [assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Suggerimenti importanti per l'assegnazione di utenti a Samanage

*    Attualmente, i ruoli di samanage vengono popolati automaticamente e dinamicamente nell'interfaccia utente di portale di Azure. Prima di assegnare i ruoli di samanage agli utenti, assicurarsi che una sincronizzazione iniziale venga completata con samanage per recuperare i ruoli più recenti nel tenant di samanage.

*    È consigliabile assegnare un singolo Azure AD utente a samanage per testare la configurazione iniziale del provisioning utenti automatico. È possibile assegnare utenti e gruppi aggiuntivi in un secondo momento dopo che i test hanno avuto esito positivo.

*    Quando si assegna un utente a samanage, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di **accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configurare il provisioning utenti automatico per samanage

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. È possibile usarlo per creare, aggiornare e disabilitare utenti o gruppi in samanage in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare la Single Sign-On basata su SAML per samanage. Seguire le istruzioni riportate nell' [esercitazione Single Sign-on](samanage-tutorial.md)samanage. Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configurare il provisioning utenti automatico per samanage in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni** > **aziendali tutte le applicazioni** > **Samanage**samanage.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Samanage**.

    ![Collegamento samanage nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Impostare la **modalità di provisioning** su **automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l' **URL del tenant** di samanage e il **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a samanage. Se la connessione non riesce, verificare che l'account Samanage abbia autorizzazioni di amministratore e riprovare.

    ![Connessione test samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Posta elettronica di notifica di samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a**samanage.

    ![Sincronizzazione utente samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Samanage. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Samanage che corrisponde agli attributi utente](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Per consentire il mapping di gruppo, nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Samanage** (Sincronizza gruppi di Azure Active Directory con Samanage).

    ![Sincronizzazione del gruppo samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Impostare **Abilitato** su **Sì** per sincronizzare i gruppi. Esaminare gli attributi gruppo sincronizzati da Azure AD a Samanage nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi del gruppo corrispondenti samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'esercitazione relativa al [filtro di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per samanage, nella sezione **Impostazioni** impostare **stato del provisioning** **su**attivato.

    ![Stato del provisioning di samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in samanage. Nella sezione **Impostazioni** selezionare i valori desiderati nell' **ambito**. Quando si seleziona l'opzione **Sincronizza tutti gli utenti e i gruppi** , prendere in considerazione le limitazioni descritte nella sezione seguente "limitazioni del connettore".

    ![Ambito samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salvataggio samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nell' **ambito** nella sezione **Impostazioni** . La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti a condizione che venga eseguito il servizio di provisioning Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio Azure AD provisioning in samanage.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Se si seleziona l'opzione **Sincronizza tutti gli utenti e i gruppi** e si configura un valore per l'attributo **ruoli** samanage, il valore nella casella **valore predefinito se null (facoltativo)** deve essere espresso nel formato seguente:

- {"displayName": "Role"}, dove Role è il valore predefinito desiderato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
