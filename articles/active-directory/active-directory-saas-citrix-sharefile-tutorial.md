<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Azure" description="Informazioni su come usare Citrix ShareFile con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=523754).

Questa esercitazione descrive l'integrazione di Azure e Citrix ShareFile. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Citrix ShareFile

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Citrix ShareFile potranno accedere all'applicazione tramite il sito aziendale di Citrix ShareFile (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Citrix ShareFile
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Citrix ShareFile

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Citrix ShareFile.

###Per abilitare l'integrazione dell'applicazione per Citrix ShareFile, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Citrix ShareFile**.

    ![Raccolta di applicazioni](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Citrix ShareFile** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Citrix ShareFile tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Citrix ShareFile** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Abilitare l'autenticazione Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Abilitare l'autenticazione Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Citrix ShareFile** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso Citrix ShareFile** della pagina **Configura URL app** digitare l'URL usando il modello seguente `https://<tenant-name>.shareFile.com` e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Citrix ShareFile** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di **Citrix ShareFile** come amministratore.

6.  Nel barra degli strumenti in alto fare clic su **Admin**.

7.  Nel riquadro di spostamento sinistro selezionare **Configure Single Sign-On**.

    ![Account Administration](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Account Administration")

8.  In **Basic Settings** della finestra di dialogo **Single Sign-On/ SAML 2.0 Configuration** seguire questa procedura:

    ![Single sign-on](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Single sign-on")

    1.  Fare clic su **Enable SAML**.
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix ShareFile** del portale di Azure copiare il valore di **ID entità** e incollarlo nella casella di testo **Your IDP Issuer/ Entity ID**.
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix ShareFile** del portale di Azure copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Login URL**.
    4.  Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix ShareFile** del portale di Azure copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Logout URL**.
    5.  Fare clic su **Change** accanto al campo **X.509 Certificate** e quindi caricare il certificato scaricato dal portale di Azure AD. ![Basic Settings](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Basic Settings")

9.  Fare clic su **Save** nel portale di gestione di Citrix ShareFile.

10. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Citrix ShareFile, è necessario eseguirne il provisioning in Citrix ShareFile. Nel caso di Citrix ShareFile, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al tenant di **Citrix ShareFile**.

2.  Fare clic su **Manage Users > Manage Users Home > + Create Employee**.

    ![Create Employee](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Create Employee")

3.  Nelle caselle di testo **Email**, **First name** e **Last name** digitare indirizzo di posta elettronica, nome e cognome di un account Azure AD valido di cui si vuole eseguire il provisioning.

    ![Basic Information](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Basic Information")

4.  Fare clic su **Add User**.

    >[AZURE.NOTE]Il titolare dell'account AAD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Citrix ShareFile per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Citrix ShareFile, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Citrix ShareFile** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->