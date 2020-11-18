---
title: 'Esercitazione: Configurare Tableau Online per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Tableau Online.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359205"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Esercitazione: Configurare Tableau Online per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Tableau Online e Azure Active Directory (Azure AD) per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti o gruppi in Tableau Online.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone i prerequisiti seguenti:

*   Un tenant di Azure AD.
*   [Tenant di Tableau Online](https://www.tableau.com/).
*   Account utente in Tableau Online con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'[API REST di Tableau Online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Questa API è disponibile per gli sviluppatori di Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Aggiungere Tableau Online da Azure Marketplace
Prima di configurare Tableau Online per il provisioning utenti automatico con Azure AD, è necessario aggiungere Tableau Online da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Tableau Online dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Tableau Online** e selezionare **Tableau Online** nel pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Tableau Online nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Assegnare utenti a Tableau Online

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti o gruppi in Azure AD devono poter accedere a Tableau Online. Per assegnare gli utenti o i gruppi a Tableau Online, seguire le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Suggerimenti importanti per l'assegnazione di utenti a Tableau Online

*   È consigliabile assegnare un singolo utente di Azure AD a Tableau Online per testare la configurazione del provisioning utenti automatico. Sarà possibile assegnare altri utenti o gruppi in un secondo momento.

*   Quando si assegna un utente a Tableau Online, selezionare qualsiasi ruolo valido specifico dell'applicazione, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurare il provisioning utenti automatico in Tableau Online

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD. In questo modo è possibile creare, aggiornare e disabilitare utenti e/o gruppi in Tableau Online in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare il Single Sign-On basato su SAML per Tableau Online. Seguire le istruzioni riportate nell'[esercitazione per l'accesso Single Sign-On di Tableau Online](tableauonline-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurare il provisioning utenti automatico per Tableau Online in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Tableau Online**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Tableau Online**.

    ![Collegamento di Tableau Online nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere il dominio, il nome utente dell'amministratore, la password dell'amministratore e l'URL del contenuto dell'account Tableau Online:

   * Nella casella **Dominio** compilare il sottodominio come descritto nel Passaggio 6.

   * Nel campo **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di Tableau Online. Un esempio è admin@contoso.com.

   * Nella casella **Password amministratore** immettere la password dell'account amministratore corrispondente al nome utente dell'amministratore.

   * Nella casella **URL del contenuto** compilare il sottodominio come descritto nel Passaggio 6.

6. Dopo aver eseguito l'accesso all'account amministratore di Tableau Online, è possibile ottenere i valori per **Dominio** e **URL del contenuto** dall'URL della pagina di amministrazione.

    * Il campo **Dominio** per l'account di Tableau Online può essere copiato da questa parte dell'URL:

        ![Dominio di Tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Il campo **URL del contenuto** per l'account Tableau Online può essere copiato da questa sezione. Si tratta di un valore definito durante la configurazione dell'account. In questo esempio, il valore è "contoso":

        ![URL del contenuto di Tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Il valore di **Dominio** potrebbe essere diverso da quello mostrato di seguito.

7. Dopo aver compilato le caselle mostrate nel passaggio 5, selezionare **Test connessione** per verificare che Azure AD possa connettersi a Tableau Online. Se la connessione non riesce, assicurarsi che l'account Tableau Online abbia autorizzazioni di amministratore e riprovare.

    ![Opzione Test connessione per Tableau Online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo e-mail di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica di Tableau Online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Tableau** (Sincronizza utenti di Azure Active Directory in Tableau).

    ![Sincronizzazione degli utenti in Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Tableau Online nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Corrispondenza attributi utente in Tableau Online](./media/tableau-online-provisioning-tutorial/attribute.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Tableau** (Sincronizza gruppi di Azure Active Directory con Tableau).

    ![Sincronizzazione dei gruppi in Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Tableau Online nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Corrispondenza attributi gruppo in Tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Tableau Online, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato di provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Tableau Online. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Ambito di Tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Opzione Salva per Tableau Online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti nell'**Ambito** della sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Le sincronizzazioni successive vengono eseguite circa ogni 40 minuti durante l'esecuzione del servizio di provisioning di Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report dell'attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Tableau Online.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="change-log"></a>Registro delle modifiche
* 30/09/2020 - È stato aggiunto il supporto per l'attributo "authSetting" per Utenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png