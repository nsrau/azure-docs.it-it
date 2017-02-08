---
title: 'Esercitazione: Integrazione di Azure Active Directory con UserVoice | Documentazione Microsoft'
description: Informazioni su come usare UserVoice con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: 593decb7595371396787a04e4a3252cfa65b2643


---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Esercitazione: Integrazione di Azure Active Directory con UserVoice
Questa esercitazione descrive l'integrazione di Azure e UserVoice.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di UserVoice

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a UserVoice potranno accedere all'applicazione tramite il sito aziendale di UserVoice (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione UserVoice
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenario")

## <a name="enabling-the-application-integration-for-uservoice"></a>Abilitazione dell'integrazione dell'applicazione UserVoice
Questa sezione descrive come abilitare l'integrazione dell'applicazione per UserVoice.

### <a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per UserVoice, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applications")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungi applicazione](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Add application")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Add an application from gallerry")

6. Nella **casella di ricerca** digitare **UserVoice**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Application gallery")

7. Nel riquadro dei risultati selezionare **UserVoice** e quindi fare clic su **Complete** (Completa) per aggiungere l'applicazione.
   
    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a UserVoice tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per UserVoice richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **UserVoice** del portale di Azure classico fare clic su **Configura Single Sign-On** per aprire la finestra di dialogo **Configura Single Sign-On **.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configure single sign-on")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a UserVoice** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configure single sign-on")

3. Nella casella di testo **URL di accesso UserVoice** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<nome-tenant\>.UserVoice.com*" e quindi fare clic su **Avanti**.
   
    ![Configura URL app](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configure App URL")

4. Nella pagina **Configura accesso Single Sign-On in UserVoice** fare clic su **Scarica certificato** per scaricare il certificato e quindi salvare il file del certificato localmente come **c:\\UserVoice.cer**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configure single sign-on")

5. In un'altra finestra del Web browser accedere al sito aziendale di UserVoice come amministratore.

6. Sulla barra degli strumenti in alto fare clic su Settings, quindi selezionare Web portal dal menu.
   
    ![Impostazioni](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Settings")

7. Nelle sezione **User authentication** (Autenticazione utente) della scheda **Web portal** (Portale Web) fare clic su **Edit** (Modifica) per aprire la finestra di dialogo **Edit User Authentication** (Modifica autenticazione utente).
   
    ![Web portal](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web portal")

8. Nella finestra di dialogo **Edit User Authentication** seguire questa procedura:
   
    ![Edit User Authentication](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Edit user authentication")
   
    a. Fare clic su **Single Sign-On (SSO)**.
 
    b. Nella finestra di dialogo **Configura accesso Single Sign-On in UserVoice** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **SSO Remote Sign-In** (Accesso remoto SSO).

    c. Nella finestra di dialogo **Configura accesso Single Sign-On in UserVoice** del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **SSO Remote Sign-Out** (Disconnessione remota SSO).
 
    d. Copiare il valore **Identificazione personale** del certificato esportato e copiarlo nella casella di testo **Current certificate SHA1 fingerprint** (Impronta digitale SHA1 certificato corrente) .  
      
    > [!TIP]
    > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
    > 
    > 
  
    e. Fare clic su **Salva le impostazioni di autenticazione**.

9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a UserVoice, è necessario eseguirne il provisioning in UserVoice.  
Nel caso di UserVoice, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al tenant di **UserVoice** .

2. Passare a **Impostazioni**.
   
    ![Impostazioni](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Settings")

3. Fare clic su **Generale**.

4. Fare clic su **Agents and permissions**.
   
    ![Agents and permissions](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents and permissions")

5. Fare clic su **Aggiungi amministratori**.
   
    ![Aggiungi amministratori](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Add admins")

6. Nella finestra di dialogo **Invita amministratori** seguire questa procedura:
   
    ![Invita amministratori](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invite admins")
   
    a. Nella casella di testo Emails digitare l'indirizzo di posta elettronica dell'account di cui si vuole eseguire il provisioning e quindi fare clic su **Add**.
   
    b. Fare clic su **Invita**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da UserVoice per eseguire il provisioning degli account utente Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Per assegnare gli utenti a UserVoice, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **UserVoice **fare clic su **Assegna utenti**.
   
    ![Assegna utenti](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Assign users")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


