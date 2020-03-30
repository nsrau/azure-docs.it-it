---
title: 'Esercitazione: Configurare uno zoscaler One per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in .scaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064173"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Esercitazione: Configurare uno zscaler One per il provisioning automatico degli utentiTutorial: Configure sscaler One for automatic user provisioning

Questa esercitazione illustra i passaggi da eseguire in Azure AD e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e gruppi in .

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning degli utenti di Azure AD. Per informazioni sulle operazioni eseguite da questo servizio, sul suo funzionamento e sulle domande frequenti, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS (Software-as-A-Service) con Azure Active Directory.](../active-directory-saas-app-provisioning.md)


## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che si dispone di:The scenario outlined in this tutorial assumes that you have:

* Un tenant di Azure AD.
* Un'inquilina di sscaler One.
* Un account utente in un account con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'API SCIM One di scalare. Questa API è disponibile per gli sviluppatori di scalare uno per gli account con il pacchetto Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Aggiungere uno di scalare da Azure Marketplace

Prima di configurare il primo di scalare per il provisioning automatico degli utenti con Azure AD, aggiungere l'oggetto Scaler One da Azure Marketplace all'elenco delle applicazioni SaaS gestite.

Per aggiungere uno di scalare dal Marketplace, attenersi alla seguente procedura.

1. Nel [portale di Azure,](https://portal.azure.com)nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory.**

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Uno scalare** e selezionare **Uno scalare** dal riquadro dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Zscaler One nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Assegnare gli utenti a .scaler One

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, decidere quali utenti o gruppi in Azure AD devono accedere a .scaler One. Per assegnare questi utenti o gruppi a scalare uno, seguire le istruzioni in [Assegnare un utente o un gruppo a un'app aziendale.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Suggerimenti importanti per l'assegnazione di utenti a

* È consigliabile assegnare un singolo utente di Azure AD a .scaler One per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a scalare uno, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurare il provisioning automatico degli utenti in uno di scalare

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. Utilizzarlo per creare, aggiornare e disabilitare gli utenti o i gruppi in scalare Uno in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare l'accesso Single Sign-On basato su SAML per .scaler One. Seguire le istruzioni riportate [nell'esercitazione Single Sign-On](zscaler-One-tutorial.md)di scala1 . Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurare il provisioning automatico degli utenti per l'one di scalare in Azure ADConfigure automatic user provisioning for sscaler One in Azure AD

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni** > aziendali**Tutte le applicazioni** > **: una scalare**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler One**.

    ![Il collegamento Uno di scalare nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Provisioning di un scalatore](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning di scalare uno](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **Credenziali di amministratore** compilare le caselle URL **tenant** e **Token segreto** con le impostazioni per l'account con la scalare uno, come descritto nel passaggio 6.

6. Per ottenere l'URL del tenant e il token segreto, passare a**Impostazioni autenticazione** **di amministrazione** > nell'interfaccia utente del portale con scalabilità uno. In **Authentication Type** (Tipo di autenticazione) selezionare **SAML**.

    ![Impostazioni di autenticazione di .scaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selezionare **Configura SAML** per aprire le opzioni **Configura SAML.**

    ![SAML di configurazione di una scalare](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selezionare **Abilita provisioning basato su SCIM** per ottenere le impostazioni in **URL di base** e token **Bearer**. Quindi salvare le impostazioni. Copiare l'impostazione **URL di base** **nell'URL tenant** nel portale di Azure.Copy the Base URL setting to Tenant URL in the Azure portal. Copiare l'impostazione **Gettone bearer** in **Token segreto** nel portale di Azure.Copy the Bearer Token setting to Secret Token in the Azure portal.

7. Dopo aver compilato le caselle visualizzate nel passaggio 5, selezionare Test connessione per assicurarsi che Azure AD sia in grado di connettersi a .scaler One.After you fill in the boxes shown in Step 5, select **Test Connection** to make sure that Azure AD can connect to sscaler One. Se la connessione non riesce, assicurati che l'account con estensione scaler One disponga delle autorizzazioni di amministratore e riprova.

    ![Connessione di prova con scaler One Test](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Nella casella **Email di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche di errore di provisioning. Selezionare la casella di controllo **Invia una notifica tramite posta elettronica quando si verifica un errore.**

    ![E-mail di notifica one](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare Sincronizza gli utenti di **Azure Active Directory con la scalare uno.**

    ![Sincronizzazione di un utente con scalare](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a .scaler One nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in .scaler One per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi utente corrispondenti con un scaler](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con con scalare uno**.

    ![Sincronizzazione di un gruppo con scaler A](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi di gruppo che vengono sincronizzati da Azure AD a .scaler One nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare la corrispondenza con i gruppi in Uno scalatore per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi di gruppo corrispondenti con un solo scalare](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, seguire le istruzioni nell'esercitazione sui [filtri di ambito.](./../active-directory-saas-scoping-filters.md)

15. Per abilitare il servizio di provisioning di Azure AD per l'oggetto scaler One, nella sezione **Impostazioni** modificare **Stato provisioning** su **Attivato**.

    ![Stato di provisioning con scalare uno](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti o i gruppi di cui si desidera eseguire il provisioning in .scaler One. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Un ambito](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salvataggio scalare uno](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti, purché venga eseguito il servizio di provisioning di Azure AD. 

È possibile utilizzare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in uno di scalare.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
