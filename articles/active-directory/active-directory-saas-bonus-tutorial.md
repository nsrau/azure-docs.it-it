---
title: 'Esercitazione: Integrazione di Azure Active Directory con Bonus.ly | Documentazione Microsoft'
description: Informazioni su come usare Bonus.ly con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 25a4e33ab85139bd3f871f46cef1d12a7b9cd06b


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Esercitazione: Integrazione di Azure Active Directory con Bonus.ly
Questa esercitazione descrive l'integrazione di Azure e Bonus.ly. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di test di Bonus.ly

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Bonus.ly
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-bonus-tutorial/IC773679.png "Scenario")

## <a name="enabling-the-application-integration-for-bonusly"></a>Abilitazione dell'integrazione dell'applicazione per Bonus.ly
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Bonus.ly.

### <a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Bonus.ly, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Abilitare l'autenticazione Single Sign-On](./media/active-directory-saas-bonus-tutorial/IC773680.png "Enable single sign-on")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-bonus-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-bonus-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Bonus.ly**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-bonus-tutorial/IC773681.png "Application gallery")
7. Nel riquadro dei risultati selezionare **Bonus.ly** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Bonus.ly tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Bonus.ly richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Bonus.ly** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Bonus.ly** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configure single sign-on")
3. Nella casella di testo **URL tenant di Bonus.ly** della pagina **Configura URL app** digitare l'URL nel formato "*https://\<nome-tenant\>.Bonus.ly*" e quindi fare clic su **Avanti**: 
   
   ![Configura URL app](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configure app URL")
4. Nella pagina **Configura accesso Single Sign-On in Bonus.ly** fare clic su **Scarica certificato** e quindi salvare il file di certificato localmente come **c:\\Bonusly.cer**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configure single sign-on")
5. In una finestra diversa del Web browser accedere al tenant **Bonus.ly** .
6. Nella barra degli strumenti in alto fare clic su **Settings** (Impostazioni) e quindi selezionare **Integrations and apps** (Integrazioni e app).
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")
7. In **Single Sign-On** selezionare **SAML**.
8. Nella pagina della finestra di dialogo **SAML** eseguire la procedura seguente:
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")
   
   1. Nella finestra di dialogo **Configura accesso Single Sign-On in Bonus.ly** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **IdP SSO target URL** (URL di destinazione SSO IdP).
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in Bonus.ly** del portale di Azure classico copiare il valore di **ID autorità di certificazione** e incollarlo nella casella di testo **IdP Issuer** (Autorità di certificazione IdP).
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in Bonus.ly** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **IdP Login URL** (URL di accesso IdP).
   4. Copiare il valore di **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Cert Fingerprint** (Impronta digitale del certificato).
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
      > 
      > 
9. Fare clic su **save**.
10. Nel portale di Microsoft Azure classico selezionare la conferma della configurazione dell'accesso e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Bonus.ly, è necessario eseguirne il provisioning in Bonus.ly.  
Nel caso di Bonus.ly, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. In una finestra diversa del Web browser accedere al tenant di Bonus.ly.
2. Fare clic su **Settings**
   
   ![Settings](./media/active-directory-saas-bonus-tutorial/IC781041.png "Settings")
3. Fare clic sulla scheda **Users and bonuses** .
   
   ![Users and bonuses](./media/active-directory-saas-bonus-tutorial/IC781042.png "Users and bonuses")
4. Fare clic su **Gestisci utenti**.
   
   ![Gestisci utenti](./media/active-directory-saas-bonus-tutorial/IC781043.png "Manage Users")
5. Fare clic su **Add User**.
   
   ![Add User](./media/active-directory-saas-bonus-tutorial/IC781044.png "Add User")
6. Nella finestra di dialogo **Aggiungi utente** seguire la procedura seguente:
   
   ![Aggiunta di un utente](./media/active-directory-saas-bonus-tutorial/IC781045.png "Add User")
   
   1. Per **Email** (E-mail), **First name** (Nome) e **Last name** (Cognome) digitare i dati di un account AAD valido di cui eseguire il provisioning.
   2. Fare clic su **Salva**.
   
   > [!NOTE]
   > Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
   > 
   > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Bonus.ly per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Per assegnare gli utenti a Bonus.ly, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione Bonus.ly fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-bonus-tutorial/IC773690.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-bonus-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


