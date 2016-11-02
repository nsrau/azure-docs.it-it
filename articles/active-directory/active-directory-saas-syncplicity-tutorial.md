<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Syncplicity | Microsoft Azure" 
    description="Informazioni su come usare Syncplicity con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Syncplicity
  
L'obiettivo di questa esercitazione è di illustrare come impostare il single sign-on tra Azure Active Directory (Azure AD) e Syncplicity.
  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant Syncplicity
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Syncplicity saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Syncplicity (accesso avviato dal provider di servizi) o tramite il pannello di accesso ad Azure AD.

1.  Abilitazione dell'integrazione dell'applicazione per Syncplicity
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per Syncplicity
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Syncplicity.

###Per abilitare l'integrazione dell'applicazione per Syncplicity, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca**, digitare **Syncplicity**.

    ![Raccolta di applicazioni Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Raccolta di applicazioni Syncplicity")

7.  Nel riquadro dei risultati selezionare **Syncplicity**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Syncplicity tramite il relativo account in Azure Active Directory utilizzando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nel portale di Azure classico, nella pagina di integrazione dell’applicazione **Syncplicity**, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura Accesso Single Sign On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Syncplicity** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Single Sign-On di Microsoft Azure AD")

3.  Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a Syncplicity**, digitare l’URL utilizzato dagli utenti per accedere all’applicazione Syncplicity e fare clic su **Avanti**.

    L'URL dell’app è l'URL del tenant Syncplicity (ad esempio: *http://company.Syncplicity.com*):

    ![Configura URL app](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configura URL app")

4.  Nella pagina **Configura Single Sign-On per Syncplicity**, per scaricare il certificato, fare clic su **Scarica certificato** e salvare il file del certificato localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configura accesso Single Sign-On")

5.  Accedere al tenant **Syncplicity**.

6.  Nel menu nella parte superiore, fare clic su **admin**, selezionare **settings**, quindi fare clic su **Custom domain and single sign-on**.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7.  Nella pagina finestra di dialogo **Single Sign-On (SSO)**, eseguire la procedura seguente:

    ![Accesso Single Sign-On (SSO)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Accesso Single Sign-On (SSO)")

    1.  Nella casella di testo **Custom Domain**, digitare il nome del dominio.
    2.  Selezionare **Enabled** come **Single Sign-On Status**.
    3.  Nel portale di Azure classico, nella pagina **Configura accesso Single Sign-On in Syncplicity**, copiare il valore **ID entità** e incollarlo nella casella di testo **ID entità**.
    4.  Nella pagina **Configura accesso Single Sign-On in Syncplicity** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **URL pagina di accesso**.
    5.  Nel portale di Azure classico, nella pagina **Configura accesso Single Sign-On in Syncplicity**, copiare il valore **URL disconnessione remota** e incollarlo nella casella di testo **URL pagina di disconnessione**.
    6.  In **Certificato Provider di identità**fare clic su **Scegli file**, quindi caricare il certificato scaricato dal portale di Azure classico.
    7.  Fare clic su **Salva modifiche**.

8.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Conferma](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Conferma")

##Configurazione del provisioning utente
  
Per essere in grado di effettuare l’accesso, è necessario effettuare il provisioning degli utenti AAD all’applicazione Syncplicity. In questa sezione viene descritto come creare gli account utente AAD in Syncplicity.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al tenant **Syncplicity** (ad esempio: *https://company.Syncplicity.com*).

2.  Fare clic su **Admin** e selezionare **user accounts**.

3.  Fare clic su **Add a user**.

    ![Gestisci utenti](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Gestisci utenti")

4.  Digitare l’**indirizzo di posta elettronica** di un account AAD di cui si desidera effettuare il provisioning, selezionare **Utente** come **ruolo**, quindi fare clic su **Avanti**.

    ![Informazioni account](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Informazioni account")

    >[AZURE.NOTE] Il titolare dell’account AAD riceverà un messaggio di posta elettronica con un collegamento per confermare e attivare l'account.

5.  Selezionare, nell’azienda, un gruppo di cui il nuovo utente dovrebbe diventare membro, quindi fare clic su**Avanti**.

    ![Appartenenza al gruppo](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Appartenenza al gruppo")

    >[AZURE.NOTE] Se non sono elencati gruppi, fare clic su **Avanti**.

6.  Selezionare le cartelle che si desidera inserire nel controllo di Syncplicity nel computer dell'utente, quindi fare clic su **Avanti**.

    ![Cartella Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Cartella Syncplicity")

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento di creazione account utente o API fornita da Syncplicity per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Syncplicity, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina dell’integrazione dell’applicazione **Syncplicity**, fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0914_2016-->