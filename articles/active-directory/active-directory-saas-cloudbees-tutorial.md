<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con CloudBees | Microsoft Azure" description="Informazioni su come usare CloudBees con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con CloudBees
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=528737).

Questa esercitazione descrive l'integrazione di Azure e CloudBees. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di CloudBees

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a CloudBees potranno accedere all'applicazione tramite il sito aziendale di CloudBees (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/azure/dn308586.aspx).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per CloudBees
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-cloudbees-tutorial/IC790415.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per CloudBees

Questa sezione descrive come abilitare l'integrazione dell'applicazione per CloudBees.

###Per abilitare l'integrazione dell'applicazione per CloudBees, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-cloudbees-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-cloudbees-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-cloudbees-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-cloudbees-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **CloudBees**.

    ![Raccolta di applicazioni](./media/active-directory-saas-cloudbees-tutorial/IC790416.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **CloudBees** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Cloudbees](./media/active-directory-saas-cloudbees-tutorial/IC790417.png "Cloudbees")

##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a CloudBees tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per CloudBees richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **CloudBees** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790418.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a CloudBees** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790419.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso CloudBees** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere a CloudBees, ad esempio "**https://grandcentral.cloudbees.com/login*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-cloudbees-tutorial/IC790420.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in CloudBees** fare clic su **Download certificato** per scaricare il file del certificato, quindi salvarlo localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790421.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di CloudBees come amministratore.

6.  Nel menu in alto fare clic su **Settings**.

    ![Impostazioni](./media/active-directory-saas-cloudbees-tutorial/IC790422.png "Impostazioni")

7.  Fare clic sulla scheda **SSO integration**.

8.  Nella sezione **Set Up SAML 2.0 Single Sign On** seguire questa procedura:

    ![Configura Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790423.png "Configura Single Sign-On")

    1.  Nella pagina **Configura accesso Single Sign-On in CloudBees** del portale di Azure AD copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **URL accesso remoto**.
    2.  Copiare il valore di **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Existing certificate fingerprint**.
    
        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

    3.  Nella casella di testo **Authentication domains** digitare il dominio della società.
    4.  Fare clic su **Save**.

9.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790424.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a **CloudBees**, è necessario eseguirne il provisioning in **CloudBees**. Nel caso di **CloudBees**, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente in CloudBees, seguire questa procedura:

1.  Accedere al sito aziendale di **CloudBees** come amministratore.

2.  Passare a **Edit Users**.

    ![Utenti](./media/active-directory-saas-cloudbees-tutorial/IC790429.png "Utenti")

3.  Fare clic su **Aggiungi**.

    ![Aggiungi](./media/active-directory-saas-cloudbees-tutorial/IC790430.png "Aggiungi")

4.  Nella sezione **Add User** seguire questa procedura:

    ![Aggiunta di un utente](./media/active-directory-saas-cloudbees-tutorial/IC790431.png "Aggiunta di un utente")

    1.  Nelle caselle di testo **Email**, **First name**, **Last name** immettere l'indirizzo di posta elettronica, il nome e il cognome di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Add User**.

        >[AZURE.NOTE]Il proprietario dell'account riceverà un messaggio di posta elettronica con le istruzioni per l'accesso.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da CloudBees per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a CloudBees, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **CloudBees** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-cloudbees-tutorial/IC790432.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-cloudbees-tutorial/IC767830.png "Sì")

Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/azure/dn308586.aspx).

<!---HONumber=August15_HO7-->