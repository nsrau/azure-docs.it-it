---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler | Microsoft Docs'
description: Informazioni su come usare Zscaler con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: 22f83af7fc19410df01350d40619e6520878bec7


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler
Questa esercitazione descrive l'integrazione di Azure e Zscaler. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Un tenant in Zscaler

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Zscaler
2. Configurazione dell'accesso Single Sign-On
3. Configurazione delle impostazioni proxy
4. Configurazione del provisioning utente
5. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scenario")

## <a name="enabling-the-application-integration-for-zscaler"></a>Abilitazione dell'integrazione dell'applicazione per Zscaler
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Zscaler.

### <a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Zscaler, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applications")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungi applicazione](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Add application")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Add an application from gallerry")

6. Nella **casella di ricerca** digitare **Zscaler**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Application gallery")

7. Nel riquadro dei risultati selezionare **Zscaler** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Zscaler tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di caricare un certificato in Zscaler.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Zscaler** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Abilita Single Sign-On](./media/active-directory-saas-zscaler-tutorial/IC769229.png "Enable single sign-on")

2. Nella pagina **How would you like users to sign on to Zscaler** (Stabilire come si desidera che gli utenti accedano a Zscaler) selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configure single sign on")

3. Nella casella di testo **Zscaler Sign In URL** (URL di accesso Zscaler) della pagina **Configura URL app** digitare l'URL di accesso ottenuto da Zscaler e quindi fare clic su **Avanti**: 
   
    > [!NOTE]
    > Se non si conosce l'URL di accesso, contattare il team di supporto Zscaler.
    > 
    > 
   
    ![Configura URL app](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configure app URL")

4. Nella pagina **Configura accesso Single Sign-On in Zscaler** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configure single sign-on")
   
    1. Fare clic su **Scarica certificato** e quindi salvare il file di il certificato in locale come **c:\\Zscaler.cer**.
    2. Copiare l' **URL richiesta di autenticazione** negli Appunti.

5. Accedere al tenant Zscaler.

6. Scegliere **Amministrazione**dal menu disponibile nella parte superiore.
   
    ![Amministrazione](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7. In **Manage Administrators & Roles** (Gestisci amministratori e ruoli) fare clic su **Manage Users & Authentication** (Gestisci utenti e autenticazione).
   
    ![Manage Administrators & Roles](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Manage Administrators & Roles")

8. Nella sezione **Choose Authentication Option for your Organization** seguire questa procedura:
   
    ![Choose Authentication Options](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Choose Authentication Options")
   
    1. Selezionare **Authenticate using SAML Single Sign-On**.
    2. Fare clic su **Configure SAML Single Sign-On Parameters**.

9. Nella pagina della finestra di dialogo **Configure SAML Single Sign-On Parameters** (Configura parametri accesso Single Sign-On SAML) procedere come descritto di seguito e quindi fare clic su **Operazione completata**:
   
    ![Carica certificato](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Upload certificate")
   
    1. Nella casella di testo **URL of the SAML Portal to which users are sent for authentication** (URL del portale di SAML a cui vengono indirizzati gli utenti per l'autenticazione) incollare il valore del campo **URL richiesta di autenticazione** dal portale di Azure classico.
   
    2. Nella casella di testo **Attribute containing Login Name** (Attributo contenente il nome di accesso) digitare **NameID**.
   
    3. Nel campo **Upload SSL Public Certificate** (Carica certificato pubblico SSL) caricare il certificato scaricato dal portale di Azure classico.
   
    4. Selezionare **Enable SAML Auto-Provisioning**.

10. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:
    
    ![Configure User Authentication](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configure User Authentication")
    
    1. Fare clic su **Save**.
    2. Fare clic su **Attiva subito**.

11. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configure single sign-on")


## <a name="configuring-proxy-settings"></a>Configurazione delle impostazioni proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni proxy in Internet Explorer

1. Avviare **Internet Explorer**.

2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.
   
    ![Opzioni Internet](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Internet Options")

3. Fare clic sulla scheda **Connessioni** .
   
    ![Connessioni](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connections")

4. Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.

5. Nella sezione del server proxy seguire questa procedura:
   
    ![Server proxy](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Proxy server")
   
    1. Selezionare Usa un server di proxy per la rete LAN.

    2. Nella casella di testo Indirizzo digitare **gateway.zscalertwo.net**.
   
    3. Nella casella di testo Porta digitare **80**.
   
    4. Selezionare **Ignora server proxy per indirizzi locali**.
   
    5. Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)**.

6. Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Zscaler, è necessario eseguirne il provisioning in Zscaler.  
Nel caso di Zscaler, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:

1. Accedere al tenant **Zscaler** .

2. Fare clic su **Administration**.

    ![Amministrazione](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3. Fare clic su **User Management**.
   
    ![User Management](./media/active-directory-saas-zscaler-tutorial/IC781036.png "User Management")

4. Nella scheda **Utenti** fare clic su **Aggiungi**.
   
    ![Add](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Add")

5. Nella sezione Add User seguire questa procedura:
   
    ![Aggiunta di un utente](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Add User")
   
    1. Digitare **ID utente**, **nome visualizzato per l'utente**, **password**, **conferma della password** e quindi selezionare **gruppi** e **reparto** per un account AAD valido di cui si vuole eseguire il provisioning.
   
    2. Fare clic su **Save**.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Per assegnare gli utenti a Zscaler, seguire questa procedura:

1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Zscaler** fare clic su **Assegna utenti**.
   
    ![Assegna utenti](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Assign users")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


