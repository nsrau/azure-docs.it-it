---
title: 'Esercitazione: Integrazione di Azure Active Directory con Dropbox for Business | Microsoft Docs'
description: Informazioni su come usare Dropbox for Business con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: e328f8ecfad5f481462a7b9c0c4a16e966e33217


---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Esercitazione: Integrazione di Azure Active Directory con Dropbox for Business
Questa esercitazione descrive l'integrazione di Azure e Dropbox for Business.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di test in Dropbox for Business

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Dropbox for Business potranno accedere all'applicazione tramite il sito aziendale di Dropbox for Business (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Dropbox for Business
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario")

## <a name="enabling-the-application-integration-for-dropbox-for-business"></a>Abilitazione dell'integrazione dell'applicazione per Dropbox for Business
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Dropbox for Business.

### <a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Dropbox for Business, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungi applicazione](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Dropbox for Business**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Application gallery")
7. Nel riquadro dei risultati selezionare **Dropbox for Business** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Dropbox for Business tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 nel tenant di Dropbox for Business. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Dropbox for Business** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Dropbox for Business** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configure single sign-on")
3. Nella pagina **Configura URL app** seguire questa procedura:
   
    a. Accedere al tenant di Dropbox for Business. 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configure single sign-on")
   
    b. Nel riquadro di spostamento a sinistra fare clic su **Admin Console**. 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configure single sign-on")
   
    c. In **Console di amministrazione** fare clic su **Autenticazione** nel riquadro di spostamento a sinistra. 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configure single sign-on")
   
    d. Nella sezione **Single sign-on** selezionare **Abilita Single Sign-On** e quindi fare clic su **More** (Altro) per espandere la sezione.  
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configure single sign-on")
   
    e. Copiare l'URL accanto a **Users can sign in by entering their email address or they can go directly to**(Gli utenti possono accedere con l'indirizzo di posta elettronica o passare direttamente a). 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configure single sign-on")
   
    f. Nel portale di Azure classico incollare l'URL nella casella di testo **DropBox for business sign in URL** (URL di accesso a DropBox for Business). 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configure single sign-on")  
4. Nella pagina **Configura accesso Single Sign-On in Dropbox for Business** fare clic su **Scarica certificato** e quindi salvare il file di certificato nel computer.  
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configure single sign-on")
5. Nella sezione **Single Sign-On** della pagina **Autenticazione** del tenant di Dropbox for Business, seguire questa procedura: 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configure single sign-on")
   
    a. Fare clic su **Required**.
   
    b. Nella pagina **Configura accesso Single Sign-On in Dropbox for Business** del Portale di Azure classico copiare il valore di **URL pagina di accesso** e quindi incollarlo nella casella di testo **URL accesso**.

    c. Creare un file **con codifica Base 64** dal certificato scaricato. 

    > [!TIP] 
    > Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    d. Fare clic su **Scegli certificato** e quindi selezionare il **file di certificato con codifica Base 64**.

    e. Fare clic su **Salva modifiche** per completare la configurazione del tenant DropBox for Business.

1. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**. 
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in Dropbox for Business.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Nella pagina di integrazione dell'applicazione **Dropbox for Business** del Portale di Azure classico fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo **Configura provisioning utenti**.
2. Nella pagina Abilita provisioning utenti in DropBox for Business fare clic su Abilita provisioning utenti per aprire la finestra di dialogo di accesso a Dropbox per il collegamento ad Azure AD.  
   
    ![Provisioning utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "User provisioning")
3. Nella finestra di dialogo di **Sign in to Dropbox to link with Azure AD** (Accedi a Dropbox per il collegamento ad Azure AD) accedere al tenant di Dropbox for Business. 
   
    ![Provisioning utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "User provisioning")
4. Fare clic su **Allow** (Consenti) per concedere ad Azure AD l'accesso a Dropbox. 
   
    ![Provisioning utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "User provisioning")
5. Per completare la configurazione fare clic sul pulsante **Complete** (Completa).  
   
    ![Provisioning utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "User provisioning")

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Per assegnare gli utenti a Dropbox for Business, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Dropbox for Business** fare clic su **Assegna utenti**.
   
    ![Assegna utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Yes")

È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Dropbox for Business.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning facendo clic su **Dashboard** nella pagina di integrazione dell'applicazione **Dropbox for Business** del Portale di Azure classico.

![Assegna utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assign users")

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato.

![Assegna utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assign users")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


