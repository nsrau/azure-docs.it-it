---
title: 'Esercitazione: Configurare Zscaler One per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zscaler One.
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
ms.openlocfilehash: b8b6383c7808fd6c298d7776fc10572631bc6ddc
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359562"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zscaler One per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Zscaler One e Azure Active Directory (Azure AD) per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti o gruppi in Zscaler One.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone i prerequisiti seguenti:

* Un tenant di Azure AD.
* Un tenant di Zscaler One.
* Account utente in Zscaler One con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'API SCIM di Zscaler One. Questa API è disponibile per gli sviluppatori di Zscaler One per gli account con il pacchetto Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Aggiungere Zscaler One da Azure Marketplace

Prima di configurare Zscaler One per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zscaler One da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Zscaler One dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Zscaler One** e selezionare **Zscaler One** nel pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Zscaler One nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Assegnare gli utenti a Zscaler One

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti o gruppi in Azure AD devono poter accedere a Zscaler One. Per assegnare gli utenti o i gruppi a Zscaler One, seguire le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler One

* È consigliabile assegnare un singolo utente di Azure AD a Zscaler One per testare la configurazione del provisioning utenti automatico. Sarà possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a Zscaler One, selezionare qualsiasi ruolo valido specifico dell'applicazione, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurare il provisioning automatico degli utenti per Zscaler One

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD. In questo modo è possibile creare, aggiornare e disabilitare utenti e/o gruppi in Zscaler One in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare il Single Sign-On basato su SAML per Zscaler One. Seguire le istruzioni riportate nell'[esercitazione per l'accesso Single Sign-On di Zscaler One](zscaler-One-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurare il provisioning utenti automatico per Zscaler One in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Zscaler One**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler One**.

    ![Collegamento di Zscaler One nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **Credenziali amministratore** compilare le caselle **URL tenant** e **Token segreto** con le impostazioni dell'account Zscaler One, come descritto nel passaggio 6.

6. Per ottenere il token segreto e l'URL del tenant, passare a **Administration** > **Authentication Settings** (Amministrazione > Impostazioni di autenticazione) nell'interfaccia utente del portale di Zscaler One. In **Authentication Type** (Tipo di autenticazione) selezionare **SAML**.

    ![Impostazioni di autenticazione di Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selezionare **Configure SAML** (Configura SAML) per aprire le opzioni di **Configure SAML** (Configura SAML).

    ![Opzione Configure SAML di Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selezionare **Enable SCIM-Based Provisioning** (Abilita provisioning basato su SCIM) per ottenere le impostazioni in **Base URL** (URL di base) e **Bearer Token** (Token di connessione). Salvare quindi le impostazioni. Copiare l'impostazione **Base URL** (URL di base) in **URL tenant** nel portale di Azure. Copiare l'impostazione **Bearer Token** (Token di connessione) in **Token segreto** nel portale di Azure.

7. Dopo aver compilato le caselle mostrate nel passaggio 5, selezionare **Test connessione** per verificare che Azure AD possa connettersi a Zscaler One. Se la connessione non riesce, verificare che l'account Zscaler One disponga di autorizzazioni di amministratore e riprovare.

    ![Test connessione per Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo e-mail di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica di Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zscaler One** (Sincronizza utenti di Azure Active Directory con Zscaler One).

    ![Sincronizzazione degli utenti in Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Zscaler One nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zscaler One per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Corrispondenza attributi utente in Zscaler One](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Zscaler One** (Sincronizza gruppi di Azure Active Directory con Zscaler One).

    ![Sincronizzazione dei gruppi in Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Zscaler One nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zscaler One per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Corrispondenza attributi gruppo in Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Zscaler One, nella sezione **Impostazioni** impostare **Stato del provisioning** su **Sì**.

    ![Stato di provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Zscaler One. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Ambito di Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Opzione Salva per Zscaler One](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti nell'**Ambito** della sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Le sincronizzazioni successive vengono eseguite circa ogni 40 minuti durante l'esecuzione del servizio di provisioning di Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report dell'attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Zscaler One.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png