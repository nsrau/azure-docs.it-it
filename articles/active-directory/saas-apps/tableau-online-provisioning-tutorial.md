---
title: 'Esercitazione: Configurare Tableau Online per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Tableau Online.Learn how to configure Azure Active Directory to automatically provision and deprovision user accounts to Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064216"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Esercitazione: Configurare Tableau Online per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Tableau Online e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e gruppi in Tableau Online.This tutorial demonstrates the steps to perform to tableau Online and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and deprovision users and groups to Tableau Online.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning degli utenti di Azure AD. Per informazioni sulle operazioni eseguite da questo servizio, sul suo funzionamento e sulle domande frequenti, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS (Software-as-A-Service) con Azure Active Directory.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che si dispone di:The scenario outlined in this tutorial assumes that you have:

*   Un tenant di Azure AD.
*   Un [tenant Tableau Online](https://www.tableau.com/).
*   Un account utente in Tableau Online con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'API Rest di [Tableau Online.](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm) Questa API è disponibile per gli sviluppatori di Tableau Online.This API is available to Tableau Online developers.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Aggiungere Tableau Online da Azure MarketplaceAdd Tableau Online from the Azure Marketplace
Prima di configurare Tableau Online per il provisioning automatico degli utenti con Azure AD, aggiungere Tableau Online da Azure Marketplace all'elenco delle applicazioni SaaS gestite.

Per aggiungere Tableau Online dal Marketplace, attenersi alla seguente procedura.

1. Nel [portale di Azure,](https://portal.azure.com)nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory.**

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Tableau Online** e selezionare **Tableau Online** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Tableau Online nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Assegnare utenti a Tableau OnlineAssign users to Tableau Online

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, decidere quali utenti o gruppi in Azure AD devono accedere a Tableau Online. Per assegnare questi utenti o gruppi a Tableau Online, seguire le istruzioni in [Assegnare un utente o un gruppo a un'app aziendale.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Suggerimenti importanti per l'assegnazione di utenti a Tableau Online

*   È consigliabile assegnare un singolo utente di Azure AD a Tableau Online per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare altri utenti o gruppi in un secondo momento.

*   Quando si assegna un utente a Tableau Online, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurare il provisioning automatico degli utenti in Tableau Online

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. Utilizzarlo per creare, aggiornare e disabilitare utenti o gruppi in Tableau Online in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile abilitare l'accesso Single Sign-On basato su SAML per Tableau Online. Seguire le istruzioni [dell'esercitazione sul single sign-on Tableau Online](tableauonline-tutorial.md). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurare il provisioning automatico degli utenti per Tableau Online in Azure ADConfigure automatic user provisioning for Tableau Online in Azure AD

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni** > aziendali**Tutte le applicazioni** > **Tableau Online**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Tableau Online**.

    ![Il collegamento Tableau Online nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning online Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere il dominio, il nome utente dell'amministratore, la password di amministratore e l'URL del contenuto dell'account Tableau Online:

   * Nella casella **Dominio** compilare il sottodominio in base al passaggio 6.

   * Nella casella **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant Clarizen. Un esempio è admin@contoso.com.

   * Nella casella **Password amministratore** immettere la password dell'account amministratore che corrisponde al nome utente dell'amministratore.

   * Nella casella **URL contenuto** compilare il sottodominio in base al passaggio 6.

6. Dopo aver eseguito l'accesso all'account amministrativo per Tableau Online, è possibile ottenere i valori per **URL dominio** e **contenuto** dall'URL della pagina di amministrazione.

    * Il campo **Dominio** per l'account di Tableau Online può essere copiato da questa parte dell'URL:

        ![Dominio Tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **L'URL dei contenuti** per il tuo account Tableau Online può essere copiato da questa sezione. Si tratta di un valore definito durante la configurazione dell'account. In questo esempio, il valore è "contoso":

        ![URL contenuto tableau online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Il **tuo dominio** potrebbe essere diverso da quello mostrato qui.

7. Dopo aver compilato le caselle illustrate nel passaggio 5, selezionare Test connessione per assicurarsi che Azure AD possa connettersi a Tableau Online.After you fill in the boxes shown in Step 5, select **Test Connection** to make sure that Azure AD can connect to Tableau Online. Se la connessione non riesce, assicurarsi che l'account Tableau Online disponga delle autorizzazioni di amministratore e riprovare.

    ![Connessione di prova tableau Online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Nella casella **Email di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche di errore di provisioning. Selezionare la casella di controllo **Invia una notifica tramite posta elettronica quando si verifica un errore.**

    ![E-mail di notifica Tableau Online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Tableau** (Sincronizza utenti di Azure Active Directory in Tableau).

    ![Sincronizzazione utente di Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Tableau Online nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi utente corrispondenti di Tableau Online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Tableau** (Sincronizza gruppi di Azure Active Directory con Tableau).

    ![Sincronizzazione dei gruppi di Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Tableau Online nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi del gruppo di corrispondenza Tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Per configurare i filtri di ambito, seguire le istruzioni nell'esercitazione sui [filtri di ambito.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Per abilitare il servizio di provisioning di Azure AD per Tableau Online, nella sezione **Impostazioni** modificare **Stato provisioning** su **Attivato**.

    ![Stato provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definire gli utenti o i gruppi di cui si desidera eseguire il provisioning in Tableau Online. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Ambito di Tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Tableau Online Salva](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti, purché venga eseguito il servizio di provisioning di Azure AD. 

È possibile utilizzare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Tableau Online.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
