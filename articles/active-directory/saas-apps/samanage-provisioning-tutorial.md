---
title: 'Esercitazione: Configurare Samanage per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Samanage.
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
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d474d9bfd6016885eaa21afcea5d44d39c624084
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104630"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Esercitazione: Configurare Samanage per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Samanage e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare il provisioning e il deprovisioning di utenti e gruppi a Samanage automaticamente.

> [!NOTE]
> Questa esercitazione descrive un connettore che si basa il servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio, come funziona e domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni software-as-a-service (SaaS) con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione si presuppone di avere:

* Un tenant di Azure AD.
* Oggetto [tenant di Samanage](https://www.samanage.com/pricing/) con il pacchetto Professional.
* Un account utente in Samanage con autorizzazioni di amministratore.

> [!NOTE]
> Integrazione del provisioning di Azure AD si basa sul [API Rest di Samanage](https://www.samanage.com/api/). Questa API è disponibile per gli sviluppatori di Samanage per gli account con il pacchetto Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Aggiungere Samanage da Azure Marketplace

Prima di configurare Samanage per provisioning utenti automatico con Azure AD, aggiungere Samanage da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Samanage dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![L'icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Samanage** e selezionare **Samanage** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Add**.

    ![Samanage nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Assegnare utenti a Samanage

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, stabilire quali utenti o gruppi in Azure AD devono poter accedere a Samanage. Per assegnare tali utenti o gruppi a Samanage, seguire le istruzioni in [assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Suggerimenti importanti per l'assegnazione di utenti a Samanage

*    Oggi, i ruoli di Samanage vengono automaticamente e in modo dinamico popolati nel portale di Azure dell'interfaccia utente. Prima di assegnare i ruoli di Samanage per gli utenti, assicurarsi che venga completata una sincronizzazione iniziale in Samanage per recuperare i ruoli più recenti nel tenant di Samanage.

*    È consigliabile assegnare un singolo utente di Azure AD a Samanage per testare il configurazione del provisioning utenti automatico iniziale. È possibile assegnare altri utenti e gruppi in un secondo momento dopo i test hanno esito positivo.

*    Quando si assegna un utente a Samanage, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configura provisioning utenti automatico per Samanage

Questa sezione descrive i passaggi per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in Samanage in base alle assegnazioni utente o gruppo in Azure AD.

> [!TIP]
> È anche possibile abilitare basato su SAML single sign-on per Samanage. Seguire le istruzioni di [esercitazione Samanage single sign-on](samanage-tutorial.md). Accesso Single sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari tra loro.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configurare il provisioning utenti automatico per Samanage in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali** > **tutte le applicazioni** > **Samanage**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Samanage**.

    ![Il collegamento di Samanage nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di Provisioning di Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Sotto il **credenziali amministratore** sezione, immettere il nome utente amministratore e la password dell'amministratore dell'account Samanage. Ecco alcuni esempi di questi valori:

   * Nel **Admin Username** casella, immettere il nome utente dell'account amministratore nel tenant di Samanage. Un esempio è admin@contoso.com.

   * Nel **Password amministratore** casella, immettere la password dell'account amministratore che corrisponde al nome utente amministratore.

6. Dopo aver inserito le caselle indicate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure AD possa connettersi a Samanage. Se la connessione non riesce, assicurarsi che l'account Samanage abbia autorizzazioni di amministratore e riprovare.

    ![Connessione di Test di Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. Nel **notifica tramite posta elettronica** casella, immettere l'indirizzo di posta elettronica della persona o il gruppo per la ricezione di notifiche di errore di provisioning. Selezionare il **invia una notifica di posta elettronica quando si verifica un errore** casella di controllo.

    ![Notifica di posta elettronica di Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Selezionare **Salva**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Samanage** (Sincronizza utenti di Azure Active Directory con Samanage).

    ![Sincronizzazione utente di Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Samanage. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Attributi utente corrispondenti di Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Per consentire il mapping di gruppo, nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Samanage** (Sincronizza gruppi di Azure Active Directory con Samanage).

    ![Sincronizzazione dei gruppi di Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Impostare **Abilitato** su **Sì** per sincronizzare i gruppi. Esaminare gli attributi gruppo sincronizzati da Azure AD a Samanage nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Attributi del gruppo corrispondenti Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Seguire le istruzioni per configurare i filtri di ambito, il [esercitazione filtro ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare Azure AD per Samanage, servizio di provisioning nel **le impostazioni** sezione, modificare **stato del Provisioning** al **su**.

    ![Stato di Provisioning di Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Definire gli utenti o gruppi che si desidera eseguire il provisioning in Samanage. Nel **le impostazioni** , selezionare i valori desiderati nelle **ambito**. Quando si seleziona il **tutti gli utenti e gruppi di sincronizzazione** opzione, prendere in considerazione le limitazioni, come descritto nella seguente sezione "Limitazioni di Connector".

    ![Ambito di Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Quando si è pronti per eseguire il provisioning, selezionare **salvare**.

    ![Salvataggio di Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nella **ambito** nel **impostazioni** sezione. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano circa ogni 40 minuti fino a quando viene eseguito il servizio di provisioning di Azure AD. 

È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti per il rapporto attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio in Samanage di provisioning di Azure AD.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Se si seleziona il **Sincronizza tutti gli utenti e gruppi** opzione e configurare un valore per il Samanage **ruoli** attributo, il valore nel **il valore predefinito Se null (è facoltativo)** finestra deve essere espressa nel formato seguente:

- {"displayName": "role"}, dove il valore predefinito da ruolo.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
