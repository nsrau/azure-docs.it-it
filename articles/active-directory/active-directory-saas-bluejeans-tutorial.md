<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con BlueJeans | Microsoft Azure" 
    description="Informazioni su come usare BlueJeans con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure AD con BlueJeans

Questa esercitazione descrive l'integrazione di Azure e BlueJeans. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di BlueJeans abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a BlueJeans potranno accedere all'applicazione tramite il sito aziendale di BlueJeans (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per BlueJeans
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per BlueJeans

Questa sezione descrive come abilitare l'integrazione dell'applicazione per BlueJeans.

###Per abilitare l'integrazione dell'applicazione per BlueJeans, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **BlueJeans**.

    ![Raccolta di applicazioni](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **BlueJeans** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a BlueJeans tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **BlueJeans** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a BlueJeans** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** nella casella di testo **BlueJeans Sign On URL** (URL di accesso a BlueJeans) digitare l'URL usando il modello "*https://company.BlueJeans.com*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in BlueJeans** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di **BlueJeans** come amministratore.

6.  Passare a **ADMIN > Group Settings > Security**.

    ![Amministratore](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

7.  Nella sezione **Security** seguire questa procedura:

    ![SAML Single Sign On](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")

    1.  Selezionare **SAML Single Sign On**.
    2.  Selezionare **Enable automatic provisioning**.

8.  Continuare e seguire questa procedura:

    ![Certificate Path](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")

    1.  Fare clic su **Choose File** e quindi caricare il certificato scaricato.
    2.  Nella finestra di dialogo **Configure single sign-on at BlueJeans** (Configura accesso Single Sign-On in BlueJeans) del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Login URL** (URL di accesso).
    3.  Nella finestra di dialogo **Configure single sign-on at BlueJeans** (Configura accesso Single Sign-On in BlueJeans) del portale di Azure classico copiare il valore di **Modifica URL password** e quindi incollarlo nella casella di testo **Password Change URL** (URL di modifica password).
    4.  Nella finestra di dialogo **Configure single sign-on at BlueJeans** (Configura accesso Single Sign-On in BlueJeans) del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Logout URL** (URL di disconnessione).

9.  Continuare e seguire questa procedura:

    ![Save Changes](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")

    1.  Nella casella di testo **User id** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    2.  Nella casella di testo **Email** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Fare clic su **Salva modifiche**.

10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a BlueJeans, è necessario eseguirne il provisioning in BlueJeans. Nel caso di BlueJeans, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di **BlueJeans** come amministratore.

2.  Passare a **ADMIN > Manage Users > Add User**.

    ![Amministratore](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Amministratore")

    >[AZURE.IMPORTANT] La scheda **Add User** è disponibile solo se nella scheda**Security** l'opzione **Enable automatic provisioning** è deselezionata.

3.  Nella sezione **Add User** seguire questa procedura:

    ![Aggiunta di un utente](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Aggiunta di un utente")

    1.  Nelle caselle di testo **BlueJeans Username**, **Email address**, **BlueJeans Meeting ID**, **Moderator Passcode**, **Full Name** e **Company** digitare il nome utente, l'indirizzo di posta elettronica, l'ID riunione, il passcode moderatore, il nome completo e la società BlueJeans di un account ADD valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Add User**.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da BlueJeans per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti ad BlueJeans, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **BlueJeans** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->