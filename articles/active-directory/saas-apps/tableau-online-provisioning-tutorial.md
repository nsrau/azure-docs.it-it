---
title: 'Esercitazione: configurare Tableau Online per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Tableau Online.
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
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123755"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Esercitazione: configurare Tableau Online per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Tableau Online e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e gruppi a Tableau Online.

> [!NOTE]
> Questa esercitazione descrive un connettore che si basa il servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio, come funziona e domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni software-as-a-service (SaaS) con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione si presuppone di avere:

*   Un tenant di Azure AD.
*   Oggetto [tenant di Tableau Online](https://www.tableau.com/).
*   Un account utente di Tableau Online con le autorizzazioni di amministratore.

> [!NOTE]
> Integrazione del provisioning di Azure AD si basa sul [API Rest di Tableau Online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Questa API è disponibile per gli sviluppatori di Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Aggiungere Tableau Online da Azure Marketplace
Prima di configurare Tableau Online per provisioning utenti automatico con Azure AD, aggiungere Tableau Online da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Tableau Online dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![L'icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Tableau Online** e selezionare **Tableau Online** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Add**.

    ![Tableau Online nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Assegnare gli utenti a Tableau Online

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, stabilire quali utenti o gruppi in Azure AD devono poter accedere a Tableau Online. Per assegnare tali utenti o gruppi a Tableau Online, seguire le istruzioni in [assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Suggerimenti importanti per l'assegnazione di utenti a Tableau Online

*   È consigliabile assegnare un singolo utente di Azure AD a Tableau Online per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Tableau Online, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurare il provisioning utenti automatico a Tableau Online

Questa sezione descrive i passaggi per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in Tableau Online usando le assegnazioni di gruppo o utente di Azure AD.

> [!TIP]
> È anche possibile abilitare basato su SAML single sign-on per Tableau Online. Seguire le istruzioni di [Tableau Online single sign-on di esercitazione](tableauonline-tutorial.md). Accesso Single sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari tra loro.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurare il provisioning utenti automatico a Tableau Online in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali** > **tutte le applicazioni** > **Tableau Online**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Tableau Online**.

    ![Il collegamento di Tableau Online nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità Provisioning di tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Sotto il **credenziali amministratore** sezione, immettere il dominio, nome utente amministratore, amministratore password e URL del contenuto dell'account di Tableau Online:

   * Nel **dominio** casella, immettere il sottodominio come descritto nel passaggio 6.

   * Nel **Admin Username** casella, immettere il nome utente dell'account amministratore nel Tenant di Clarizen. Un esempio è admin@contoso.com.

   * Nel **Password amministratore** casella, immettere la password dell'account amministratore che corrisponde al nome utente amministratore.

   * Nel **URL del contenuto** casella, immettere il sottodominio come descritto nel passaggio 6.

6. Dopo l'accesso al proprio account amministrativo di Tableau Online, è possibile ottenere i valori per **Domain** e **URL del contenuto** dall'URL della pagina di amministrazione.

    * Il campo **Dominio** per l'account di Tableau Online può essere copiato da questa parte dell'URL:

        ![Dominio tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Il **URL del contenuto** per di Tableau Online account può essere copiato da questa sezione. È un valore che viene definito durante la configurazione dell'account. In questo esempio, il valore è "contoso":

        ![URL del contenuto di tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > I **dominio** potrebbe essere diverso da quello illustrato di seguito.

7. Dopo aver inserito le caselle indicate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure AD possa connettersi a Tableau Online. Se la connessione non riesce, verificare che l'account di Tableau Online disponga delle autorizzazioni di amministratore e riprovare.

    ![Tableau Online Test connessione](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Nel **notifica tramite posta elettronica** casella, immettere l'indirizzo di posta elettronica della persona o il gruppo per la ricezione di notifiche di errore di provisioning. Selezionare il **invia una notifica di posta elettronica quando si verifica un errore** casella di controllo.

    ![Tableau Online notifica tramite posta elettronica](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Tableau** (Sincronizza utenti di Azure Active Directory in Tableau).

    ![Sincronizzazione di tableau Online utente](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Tableau Online nel **mapping di attributi** sezione. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Tableau Online corrispondenti agli attributi utente](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Tableau** (Sincronizza gruppi di Azure Active Directory con Tableau).

    ![Tableau Online del gruppo di sincronizzazione](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Tableau Online nel **mapping di attributi** sezione. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Tableau Online corrispondente gli attributi del gruppo](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Seguire le istruzioni per configurare i filtri di ambito, il [esercitazione filtro ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il provisioning del servizio di Tableau Online, in AD Azure i **impostazioni** sezione, modificare **stato del Provisioning** a **su**.

    ![Stato del Provisioning tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definire gli utenti o gruppi che si desidera eseguire il provisioning in Tableau Online. Nel **le impostazioni** , selezionare i valori desiderati nelle **ambito**.

    ![Ambito di tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **salvare**.

    ![Tableau Online salvataggio](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nella **ambito** nel **impostazioni** sezione. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano circa ogni 40 minuti fino a quando viene eseguito il servizio di provisioning di Azure AD. 

È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti per il rapporto attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio in Tableau Online di provisioning di Azure AD.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
