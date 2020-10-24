---
title: 'Esercitazione: Configurare Tableau Online per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Tableau online.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: fa28b299a33f5386edc6ce14c523d2c332b0767b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520505"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Esercitazione: Configurare Tableau Online per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Tableau online e Azure Active Directory (Azure AD) per configurare Azure AD per eseguire automaticamente il provisioning e il deprovisioning di utenti e gruppi in Tableau online.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone i prerequisiti seguenti:

*   Un tenant di Azure AD.
*   Un [tenant Tableau online](https://www.tableau.com/).
*   Un account utente in Tableau online con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning Azure AD si basa sull' [API REST di tableau online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Questa API è disponibile per gli sviluppatori Tableau online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Aggiungi Tableau online da Azure Marketplace
Prima di configurare Tableau online per il provisioning utenti automatico con Azure AD, aggiungere Tableau online da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Tableau online dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Tableau online** e selezionare **Tableau online** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Tableau Online nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Assegnare gli utenti a Tableau online

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a Tableau online. Per assegnare questi utenti o gruppi a Tableau online, seguire le istruzioni riportate in [assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Suggerimenti importanti per l'assegnazione di utenti a Tableau Online

*   È consigliabile assegnare un singolo utente Azure AD a Tableau online per testare la configurazione del provisioning utenti automatico. Sarà possibile assegnare altri utenti o gruppi in un secondo momento.

*   Quando si assegna un utente a Tableau online, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurare il provisioning utenti automatico in Tableau online

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in Tableau online in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare la Single Sign-On basata su SAML per Tableau online. Seguire le istruzioni riportate nell' [esercitazione su Tableau Online Single Sign-on](tableauonline-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurare il provisioning utenti automatico per Tableau online in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**  >  **tutte le applicazioni**  >  **Tableau online**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Tableau Online**.

    ![Collegamento di tableau online nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning di tableau online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **credenziali amministratore** immettere il dominio, il nome utente amministratore, la password amministratore e l'URL del contenuto dell'account Tableau online:

   * Nella casella **dominio** compilare il sottodominio in base al passaggio 6.

   * Nella casella **nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant Tableau online. Un esempio è admin@contoso.com.

   * Nella casella **password amministratore** immettere la password dell'account amministratore che corrisponde al nome utente amministratore.

   * Nella casella **URL contenuto** compilare il sottodominio in base al passaggio 6.

6. Dopo aver effettuato l'accesso all'account amministrativo per Tableau online, è possibile ottenere i valori per **dominio** e **URL contenuto** dall'URL della pagina di amministrazione.

    * Il campo **Dominio** per l'account di Tableau Online può essere copiato da questa parte dell'URL:

        ![Dominio Tableau online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * L' **URL del contenuto** per l'account Tableau online può essere copiato da questa sezione. Si tratta di un valore definito durante la configurazione dell'account. In questo esempio, il valore è "contoso":

        ![URL contenuto Tableau online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Il **dominio** potrebbe essere diverso da quello illustrato di seguito.

7. Dopo aver compilato le caselle visualizzate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a Tableau online. Se la connessione non riesce, verificare che l'account Tableau online disponga delle autorizzazioni di amministratore e riprovare.

    ![Connessione test di tableau online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo e-mail di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica Tableau online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Tableau** (Sincronizza utenti di Azure Active Directory in Tableau).

    ![La sincronizzazione utente Tableau online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Tableau online nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Tableau online attributi utente corrispondenti](./media/tableau-online-provisioning-tutorial/attribute.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Tableau** (Sincronizza gruppi di Azure Active Directory con Tableau).

    ![La sincronizzazione del gruppo Tableau online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Tableau online nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Tableau online attributi di gruppo corrispondenti](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning Azure AD per Tableau online, nella sezione **Impostazioni** impostare **stato del provisioning** **su**attivato.

    ![Stato provisioning di tableau online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Tableau online. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Ambito Tableau online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Tableau online Save](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti nell'**Ambito** della sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Le sincronizzazioni successive vengono eseguite circa ogni 40 minuti durante l'esecuzione del servizio di provisioning di Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report dell'attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning Azure AD in Tableau online.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="change-log"></a>Registro delle modifiche
* 09/30/2020-è stato aggiunto il supporto per l'attributo "authSetting" per gli utenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png