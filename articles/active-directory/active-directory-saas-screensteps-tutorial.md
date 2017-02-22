---
title: 'Esercitazione: Integrazione di Azure Active Directory con ScreenSteps | Microsoft Docs'
description: Informazioni su come usare ScreenSteps con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dd50971e16da2cec046dd6dbe2f94dd58d01fb09
ms.openlocfilehash: c2f0e5b1348a25f4cb4c92b2a837fd22207e5dad


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Esercitazione: Integrazione di Azure Active Directory con ScreenSteps
In questa esercitazione viene illustrata l'integrazione di Azure e ScreenSteps.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant ScreenSteps

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a ScreenSteps saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di ScreenSteps (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per ScreenSteps
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario")

## <a name="enabling-the-application-integration-for-screensteps"></a>Abilitazione dell'integrazione dell'applicazione per ScreenSteps
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per ScreenSteps.

### <a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per ScreenSteps, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **ScreenSteps**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **ScreenSteps**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a ScreenSteps tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **ScreenSteps** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a ScreenSteps** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso ScreenSteps** usando il modello seguente "*https://\<tenant-name\>.ScreenSteps.com*" e fare clic su **Avanti**.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurare l'URL dell'app")

4. Nella pagina **Configura accesso Single Sign-On in ScreenSteps** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file del certificato sul computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurare l'accesso Single Sign-On")

5. In un'altra finestra del Web browser accedere al sito aziendale di ScreenSteps come amministratore.

6. Fare clic su **Account Management**.
   
    ![Gestione degli account](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestione degli account")

7. Fare clic su **Remote Authentication**.
   
    ![Autenticazione remota](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticazione remota")

8. Fare clic su **Crea endpoint di autenticazione**.
   
    ![Autenticazione remota](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticazione remota")

9. Nella sezione **Create an Authentication Endpoint** , eseguire la procedura seguente:
   
    ![Creare un endpoint di autenticazione](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Creare un endpoint di autenticazione")
   
    1. Nella casella di testo **Title** digitare un titolo.
    2. Nell'elenco **Mode** selezionare **SAML**.
    3. Fare clic su **Crea**.

10. Nella sezione **Remote Authentication Endpoint** eseguire la procedura seguente:
    
    ![Endpoint di autenticazione remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Endpoint di autenticazione remota")
    
    1. Nella pagina **Configure single sign-on at ScreenSteps** (Configura accesso Single Sign-On in ScreenSteps) del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Remote Login URL** (URL accesso remoto).

    2. Nella pagina **Configure single sign-on at ScreenSteps** (Configura accesso Single Sign-On in ScreenSteps) del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Log out URL** (URL di disconnessione).

    3. Fare clic su **Scegli un file**e caricare il certificato scaricato.

    4. Fare clic su **Update**.

11. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurare l'accesso Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a **ScreenSteps**, è necessario eseguirne il provisioning in **ScreenSteps**.  
Nel caso di **ScreenSteps**, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente in ScreenSteps, eseguire la procedura seguente:
1. Accedere al tenant **ScreenSteps**.

2. Fare clic su **Account Management**.
   
    ![Gestione degli account](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestione degli account")

3. Fare clic su **Users**.
   
    ![Utenti](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Utenti")

4. Fare clic su **Create a user**.
   
    ![Tutti gli utenti](./media/active-directory-saas-screensteps-tutorial/IC778545.png "Tutti gli utenti")

5. Dall’elenco **User Role** selezionare un ruolo per l'utente.

6. Nella sezione User Role (Ruolo utente) digitare "**First name** (Nome), **Last name** (Cognome), **Email** (Indirizzo di posta elettronica), **Login** (Account di accesso), **Password** (Password) e **Password Confirmation** (Password di conferma)" di un account di AAD valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
   
    ![Nuovo utente](./media/active-directory-saas-screensteps-tutorial/IC778546.png "Nuovo utente")

7. Nella sezione Groups selezionare **Authentication Group (SAML)** e fare clic su **Create User**.
   
    ![Gruppi](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Gruppi")

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da ScreenSteps per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>Per assegnare gli utenti ad ScreenSteps eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **ScreenSteps** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


