<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Brightspace by Desire2Learn | Microsoft Azure" description="Informazioni su come usare Brightspace by Desire2Learn con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Brightspace by Desire2Learn
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=550977).

Questa esercitazione descrive l'integrazione di Azure e Brightspace by Desire2Learn. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Brightspace by Desire2Learn abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Brightspace by Desire2Learn potranno accedere all'applicazione tramite il sito aziendale di Brightspace by Desire2Learn (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Brightspace by Desire2Learn
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Brightspace by Desire2Learn

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Brightspace by Desire2Learn.

###Per abilitare l'integrazione dell'applicazione per Brightspace by Desire2Learn, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Brightspace by Desire2Learn**.

    ![Raccolta di applicazioni](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Brightspace by Desire2Learn** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Brightspace by Desire2Learn tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Brightspace by Desire2Learn** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Brightspace by Desire2Learn** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** seguire questa procedura:

    ![Configura URL app](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configura URL app")

    1.  Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione **Brightspace by Desire2Learn**, ad esempio: **https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
2.  Fare clic su **Avanti**

4.  Nella pagina **Configura accesso Single Sign-On in Brightspace by Desire2Learn** fare clic su **Download metadati** per scaricare i metadati e quindi salvarli nel computer..

    ![Configura accesso Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati scaricato al team di supporto di Brightspace by Desire2Learn.

    >[AZURE.NOTE]Il team di supporto di Brightspace by Desire2Learn si occuperà dell'effettiva configurazione dell'accesso Single Sign-On. Al termine dell'abilitazione dell'accesso Single Sign-On per la sottoscrizione si riceverà una notifica.

6.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Brightspace by Desire2Learn, è necessario eseguirne il provisioning in Brightspace by Desire2Learn. Nel caso di Brightspace by Desire2Learn, gli account utente devono essere creati dal team di supporto di Brightspace by Desire2Learn.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Brightspace by Desire2Learn per eseguire il provisioning degli account utente di Azure Active Directory.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Brightspace by Desire2Learn, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Brightspace by Desire2Learn** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Sì")

Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->