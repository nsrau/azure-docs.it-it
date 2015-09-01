<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Central Desktop | Microsoft Azure" description="Informazioni su come usare Central Desktop con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Central Desktop
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=522411).

Questa esercitazione descrive l'integrazione di Azure e Central Desktop. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Central Desktop abilitata per l'accesso Single Sign-On e tenant di Central Desktop

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Central Desktop
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Central Desktop

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Central Desktop.

###Per abilitare l'integrazione dell'applicazione per Central Desktop, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Central Desktop**.

    ![Raccolta di applicazioni](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Central Desktop** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Central Desktop tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 nel tenant di Central Desktop.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

Nase

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Central Desktop** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Central Desktop** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** seguire questa procedura e quindi fare clic su **Avanti**:

    -   Nella casella di testo **URL accesso Central Desktop** digitare l'URL del tenant di Central Desktop (ad esempio: **http://contoso.centraldesktop.com*).
    -   Nella casella di testo URL di risposta Central Desktop digitare l'URL AssertionConsumerService Central Desktop (ad esempio: https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE]È possibile ottenere il valore dai metadati di Central Desktop (ad esempio: **http://contoso.centraldesktop.com*).

    ![Configura URL app](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Central Desktop** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configura accesso Single Sign-On")

5.  Accedere al tenant **Central Desktop**.

6.  Passare a **Settings**, fare clic su **Advanced** e quindi su **Single Sign On**.

    ![Setup - Advanced](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")

7.  Nella finestra di dialogo **Single Sign-On Settings** seguire questa procedura:

    ![Single Sign On Settings](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")

    1.  Selezionare **Enable SAML v2 Single Sign On**.
    2.  Nella pagina **Configura accesso Single Sign-On in Central Desktop** del portale di Azure AD copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **SSO URL**.
    3.  Nella pagina **Configura accesso Single Sign-On in Central Desktop** del portale di Azure AD copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **SSO Login URL**.
    4.  Nella pagina **Configura accesso Single Sign-On in Central Desktop** del portale di Azure AD copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **SSO Logout URL**.

8.  Nella sezione **Message Signature Verification Method** seguire questa procedura:

    ![Message Signature Verification Method](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")

    1.  Selezionare **Certificate**.
    2.  Nell'elenco **SSO Certificate** selezionare **RSH SHA256**.
    3.  Creare un file di testo dal certificato scaricato, copiare il contenuto del file di testo e quindi incollarlo nel campo **SSO Certificate**.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    4.  Selezionare **Display a link to your SAMLv2 login page**.

9.  Fare clic su **Update**.

10. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di AAD di accedere, è necessario eseguirne il provisioning nell'applicazione Central Desktop. Questa sezione descrive come creare account utente di AAD in Central Desktop.

###Per eseguire il provisioning degli account utente in Central Desktop:

1.  Accedere al tenant di Central Desktop.

2.  Passare a **People > Internal Members**.

3.  Fare clic su **Add Internal Members**.

    ![People](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")

4.  Nella casella di testo **Email Address of New Members** digitare un account AAD di cui si vuole eseguire il provisioning e quindi fare clic su **Next**.

    ![Email Addresses of New Members](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")

5.  Fare clic su **Add Internal member(s)**.

    ![Add Internal Member](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")

    >[AZURE.NOTE]Gli utenti aggiunti riceveranno un messaggio di posta elettronica che include un collegamento di conferma su cui dovranno fare clic per attivare l'account.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Central Desktop per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Central Desktop, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Central Desktop** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sì")

Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!-------HONumber=August15_HO7-->