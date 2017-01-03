---
title: 'Esercitazione: Integrazione di Azure Active Directory con FM:Systems | Microsoft Docs'
description: Informazioni su come usare FM:Systems con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 789ec6f0a230f49ecd935bc85ea4ad984e936da9
ms.openlocfilehash: bd2bfa1696215ded3da40d349111efa13ddbed37


---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Esercitazione: Integrazione di Azure Active Directory con FM:Systems
Questa esercitazione descrive l'integrazione di Azure e FM:Systems.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di FM:Systems abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a FM:Systems potranno accedere all'applicazione tramite il sito aziendale di FM:Systems (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per FM:Systems
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scenario")

## <a name="enabling-the-application-integration-for-fmsystems"></a>Abilitazione dell'integrazione dell'applicazione per FM:Systems
Questa sezione descrive come abilitare l'integrazione dell'applicazione per FM:Systems.

### <a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per FM:Systems, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Applications")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungi applicazione](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Add application")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Add an application from gallerry")

6. Nella **casella di ricerca** digitare **FM:Systems**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Application Gallery")

7. Nel riquadro dei risultati selezionare **FM:Systems** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![FM:Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM:Systems")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a FM:Systems tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **FM:Systems** del Portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configure Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a FM:Systems** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configure Single Sign-On")

3. Nella pagina **Configura URL app** seguire questa procedura:
   
    ![Configura URL app](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configure App URL")
   
    a. Nella casella di testo **FM:Systems Sign On URL (URL di accesso a FM:Systems)**, digitare l'**URL di risposta** di FM: Systems(ad esempio: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  
      
    > [!NOTE]
    > Richiedere questo valore dal team di supporto di FM:Systems.
    > 
    > 
   
    b. Fare clic su **Avanti**

4. Nella pagina **Configura accesso Single Sign-On in FM:Systems** fare clic su **Download metadata (Scarica metadati)** per scaricare il file di metadati e salvare i metadati nel computer.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configure Single Sign-On")

5. Inviare il file di metadati scaricato al team di supporto di FM:Systems.
   
    > [!NOTE]
    > La configurazione effettiva dell'accesso Single Sign-On deve essere eseguita dal team di supporto di FM:Systems.
    > Una volta completata l'abilitazione dell'accesso Single Sign-On per la sottoscrizione, si riceverà una notifica.
    > 
    > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad FM:Systems, è necessario eseguirne il provisioning in FM:Systems.  
Nel caso di FM:Systems, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. In una finestra del Web browser accedere al sito aziendale di FM:Systems come amministratore.

2. Passare a **Amministrazione del sistema \> Gestisci sicurezza \> Utenti \> Elenco utenti**.
   
    ![System Administration](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "System Administration")

3. Fare clic su **Crea nuovo utente**.
   
    ![Crea nuovo utente](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Create New User")

4. Nella sezione **Crea utente** , eseguire la procedura seguente:
   
    ![Crea utente](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Create User")
   
    a. Nelle apposite caselle di testo, digitare il nome utente, la password, la conferma password, l'indirizzo di posta elettronica e l'ID dipendente di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   
    b. Fare clic su **Avanti**.
 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Per assegnare gli utenti a FM:Systems seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **FM:Systems** fare clic su **Assegna utenti**.
   
    ![Assegna utenti](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Assign Users")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


