<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Boomi | Microsoft Azure" description="Informazioni su come usare Boomi con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Boomi
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=529589).

Questa esercitazione descrive l'integrazione di Azure e Boomi. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Boomi abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Boomi potranno accedere all'applicazione tramite il sito aziendale di Boomi (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Boomi
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Boomi

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Boomi.

###Per abilitare l'integrazione dell'applicazione per Boomi, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-boomi-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-boomi-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Boomi**.

    ![Raccolta di applicazioni](./media/active-directory-saas-boomi-tutorial/IC790822.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Boomi** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Boomi tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Boomi** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Boomi** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di risposta Boomi** della pagina **Configura URL app** digitare l'**URL di accesso Boomi AtomSphere** (ad esempio: "*https://platform.boomi.com/sso/AccountName/saml*”) e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Boomi** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Boomi come amministratore.

6.  Nella barra degli strumenti in alto far clic sul nome della società e quindi su **Setup**.

    ![Impostazione](./media/active-directory-saas-boomi-tutorial/IC790828.png "Impostazione")

7.  Fare clic su **SSO Options**.

    ![SSO Options](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")

8.  Nella sezione **Single Sign-On Options** seguire questa procedura:

    ![Single Sign-On Options](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")

    1.  Selezionare **Enable SAML Single Sign-On**.
    2.  Per caricare il certificato scaricato, fare clic su **Import**.
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in Boomi** del portale di Azure copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Identity Provider Login URL**.
    4.  Come **Federation Id Location** selezionare **Federation Id is in NameID element of the Subject**.
    5.  Fare clic su **Save**.

9.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Boomi, è necessario eseguirne il provisioning in Boomi. Nel caso di Boomi, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **Boomi** come amministratore.

2.  Passare a **User Management > Users**.

    ![Utenti](./media/active-directory-saas-boomi-tutorial/IC790831.png "Utenti")

3.  Fare clic su **Add user**.

    ![Aggiunta di un utente](./media/active-directory-saas-boomi-tutorial/IC790832.png "Aggiunta di un utente")

4.  Nella finestra di dialogo **Add User Roles** seguire questa procedura:

    ![Aggiunta di un utente](./media/active-directory-saas-boomi-tutorial/IC790833.png "Aggiunta di un utente")

    1.  Nelle caselle di testo **First Name**, **Last Name** e **Email** digitare nome, cognome e indirizzo di posta elettronica di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su OK.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Boomi per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Boomi, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Boomi** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-boomi-tutorial/IC790834.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-boomi-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->