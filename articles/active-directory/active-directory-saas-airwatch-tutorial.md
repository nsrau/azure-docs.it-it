<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con AirWatch | Microsoft Azure" 
    description="Informazioni su come usare AirWatch con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Esercitazione: Integrazione di Azure Active Directory con AirWatch

Questa esercitazione descrive l'integrazione di Azure e AirWatch. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione AirWatch abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad AirWatch potranno accedere all'applicazione tramite il sito aziendale di AirWatch (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per AirWatch
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##Abilitazione dell'integrazione dell'applicazione per AirWatch

Questa sezione descrive come abilitare l'integrazione dell'applicazione per AirWatch.

###Per abilitare l'integrazione dell'applicazione per AirWatch, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **AirWatch**.

    ![Raccolta di applicazioni](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **AirWatch** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad AirWatch tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **AirWatch** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a AirWatch** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** nella casella di testo **URL di accesso AirWatch** digitare l'URL usato dagli utenti per accedere all'applicazione AirWatch, ad esempio "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*", e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in AirWatch** fare clic su **Download certificato** e quindi salvare il file di certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di AirWatch come amministratore.

6.  Nel riquadro di spostamento sinistro fare clic su **Accounts** e quindi su **Administrators**.

    ![Amministratori](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Amministratori")

7.  Espandere il menu **Settings** e quindi fare clic su **Directory Services**.

    ![Impostazioni](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Impostazioni")

8.  Fare clic sulla scheda **User**, digitare il proprio nome di dominio nel campo di testo **Base DN** e quindi fare clic su **Save**.

    ![Utente](./media/active-directory-saas-airwatch-tutorial/IC791922.png "Utente")

9.  Fare clic sulla scheda **Server**.

    ![Server](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Server")

10. Eseguire la procedura seguente:

    ![Caricamento](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Caricamento")

    1.  Per **Directory Type**, selezionare **None**.
    2.  Selezionare **Use SAML For Authentication**.
    3.  Per caricare il certificato scaricato, fare clic su **Upload**.

11. Nella sezione **Request** seguire questa procedura:

    ![Richiesta](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Richiesta")

    1.  Per **Request Binding Type**, selezionare **POST**.
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in Airwatch** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **Identity Provider Single Sign On URL**.
    3.  Per **NameID format**, selezionare **Email address**.
    4.  Fare clic su **Save**.

12. Fare di nuovo clic sulla scheda **User**.

    ![Utente](./media/active-directory-saas-airwatch-tutorial/IC791926.png "Utente")

13. Nella sezione **Attribute** seguire questa procedura:

    ![Attributo](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attributo")

    1.  Nella casella di testo **Object Identifier** digitare ****http://schemas.microsoft.com/identity/claims/objectidentifier**.
2.  Nella casella di testo **Username** digitare ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
3.  Nella casella di testo **Display Name** digitare ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
4.  Nella casella di testo **First Name** digitare ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
5.  Nella casella di testo **Last Name**, digitare ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
6.  Nella casella di testo **Email**, digitare ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
7.  Fare clic su **Save**.

14. Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad AirWatch, è necessario eseguirne il provisioning in AirWatch. Nel caso di AirWatch, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di **AirWatch** come amministratore.

2.  Nel riquadro di spostamento a sinistra fare clic su **Accounts** e quindi su **Users**.

    ![Utenti](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Utenti")

3.  Dal menu **Users** scegliere **List View** e quindi fare clic su **Add > Add User**.

    ![Aggiunta di un utente](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Aggiunta di un utente")

4.  Nella finestra di dialogo **Add / Edit User** seguire questa procedura:

    ![Aggiunta di un utente](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Aggiunta di un utente")

    1.  Nelle caselle di testo **Username**, **Password**, **Confirm Password**, **First Name**, **Last Name**, **Email Address** digitare il nome utente, la password, la conferma password, il nome e il cognome di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Save**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da AirWatch per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti ad AirWatch, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **AirWatch** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->