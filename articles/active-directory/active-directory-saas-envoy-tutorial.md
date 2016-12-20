---
title: 'Esercitazione: Integrazione di Azure Active Directory con Envoy | Documentazione Microsoft'
description: Informazioni su come usare Envoy con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cb3062d8b3ce6640ae07da8887f0e0f14a89d05


---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>Esercitazione: Integrazione di Azure Active Directory con Envoy
Questa esercitazione descrive l'integrazione di Azure ed Envoy.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Envoy

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Envoy potranno accedere all'applicazione tramite il sito aziendale di Envoy (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Envoy
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario")

## <a name="enabling-the-application-integration-for-envoy"></a>Abilitazione dell'integrazione dell'applicazione per Envoy
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Envoy.

### <a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Envoy, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-envoy-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-envoy-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Envoy**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-envoy-tutorial/IC776760.png "Application gallery")
7. Nel riquadro dei risultati selezionare **Envoy** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Envoy tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Envoy richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Envoy** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Abilita Single Sign-On](./media/active-directory-saas-envoy-tutorial/IC776778.png "Enable single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Envoy** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configure single sign-on")
3. Nella casella di testo **URL accesso Envoy** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<tenant-name\>.Envoy.com*" e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configure app URL")
4. Nella pagina **Configura accesso Single Sign-On in Envoy** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente come **c:\\Envoy.cer**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configure single sign-on")
5. In un'altra finestra del Web browser accedere al sito aziendale di Envoy come amministratore.
6. Nel barra degli strumenti in alto fare clic su **Impostazioni**.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")
7. Fare clic su **Azienda**.
   
   ![Azienda](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")
8. Fare clic su **SAML**.
   
   ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")
9. Nella sezione di configurazione **SAML Authentication** seguire questa procedura:
   
   ![SAML Authentication](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML authentication")
   
   > [!NOTE]
   > Il valore dell'ID della sede centrale viene generato automaticamente dall'applicazione.
   > 
   > 
   
   1. Copiare il valore di **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Impronta digitale**.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
      > 
      > 
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in Envoy** del portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Identity Provider HTTP SAML URL** (URL del provider di identità HTTP SAML).
   3. Fare clic su **Salva modifiche**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è necessaria alcuna attività di configurazione per il provisioning degli utenti in Envoy.  
Quando un utente assegnato prova ad accedere a Envoy usando il pannello di accesso, Envoy verifica se l'utente esiste.  
Se l'account utente non è presente, Envoy lo crea automaticamente.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Per assegnare gli utenti a Envoy, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Envoy** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-envoy-tutorial/IC776787.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-envoy-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


