---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adaptive Suite | Microsoft Docs'
description: Informazioni su come usare Adaptive Suite con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 652b0cef09625ed354c2c72178cc98a0482bb789


---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Esercitazione: Integrazione di Azure Active Directory con Adaptive Suite
Questa esercitazione descrive l'integrazione di Azure e Adaptive Suite.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Adaptive Suite valido

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad Adaptive Suite potranno accedere all'applicazione tramite il sito aziendale di Adaptive Suite (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Adaptive Suite
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scenario")

## <a name="enabling-the-application-integration-for-adaptive-suite"></a>Abilitazione dell'integrazione dell'applicazione per Adaptive Suite
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Adaptive Suite.

### <a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Adaptive Suite, procedere come segue:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Adaptive Suite**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Adaptive Suite** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad Adaptive Suite tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Adaptive Suite richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Adaptive Suite** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Adaptive Suite** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configure Single Sign-On")
3. Nella pagina **Configurare le impostazioni dell'app** nella casella di testo **URL di risposta** digitare l'URL usando il modello "*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" e fare clic su **Avanti**.
   
   > [!NOTE]
   > È possibile ottenere questo valore dalla pagina **SAML SSO Settings** di Adaptive Suite.
   > 
   > 
   
   ![Configurare le impostazioni dell'app](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configure App Settings")
4. Nella pagina **Configura accesso Single Sign-On in Adaptive Suite** per scaricare il certificato fare clic su **Scarica certificato** e infine salvare il file di certificato localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Adaptive Suite come amministratore.
6. Passare alla pagina **Admin**.
   
   ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")
7. Nella sezione **Users and Roles** (Utenti e ruoli) fare clic su **Manage SAML SSO Settings** (Gestisci impostazioni SSO SAML).
   
   ![Manage SAML SSO Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Manage SAML SSO Settings")
8. Nella pagina **SAML SSO Settings** eseguire queste operazioni:
   
   ![SAML SSO Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO Settings")
   
   1. Nella casella di testo **Identity provider name** digitare un nome per la configurazione.
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in Adaptive Suite** del portale di Azure classico copiare il valore di **ID entità** e incollarlo nella casella di testo **Identity provider Entity ID** (ID entità provider di identità).
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in Adaptive Suite** del portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Identity provider SSO URL** (URL SSO provider di identità).
   4. Nella finestra di dialogo **Configure single sign-on at Adaptive Suite** (Configura accesso Single Sign-On in Adaptive Suite) del portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Custom logout URL** (URL di disconnessione personalizzato).
   5. Per caricare il certificato scaricato, fare clic su **Scegli file**.
   6. Per **SAML user id**, selezionare il **nome utente dell'utente di Adaptive Insights**.
   7. Per **SAML user id location**, selezionare l'**ID utente in NameID of Subject**.
   8. Per **SAML NameID format**, selezionare **Email address**.
   9. Per **Enable SAML**, selezionare **Allow SAML SSO and direct Adaptive Insights login**.
   10. Fare clic su **Salva**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad Adaptive Suite, è necessario eseguirne il provisioning in Adaptive Suite.  
Nel caso di Adaptive Suite, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Adaptive Suite** come amministratore.
2. Passare alla pagina **Admin**.
   
   ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")
3. Nella sezione **Users and Roles** (Utenti e ruoli) fare clic su **Add User** (Aggiungi utente).
   
   ![Aggiunta di un utente](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Add User")
4. Nella sezione **Nuovo utente** seguire questa procedura:
   
   ![Submit](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Submit")
   
   1. Nelle caselle di testo corrispondenti **Name**, **Login**, **Email**, **Password** digitare il nome, l'account di accesso, l'indirizzo di posta elettronica e la password di un account utente Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Selezionare un valore in **Role**.
   3. Fare clic su **Submit**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Adaptive Suite per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Per assegnare gli utenti ad Adaptive Suite, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Adaptive Suite** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


