<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con xMatters OnDemand | Microsoft Azure" description="Informazioni su come usare xMatters OnDemand con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Esercitazione: Integrazione di Azure Active Directory con xMatters OnDemand
  
Questa esercitazione descrive l'integrazione di Azure e xMatters OnDemand. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di xMatters OnDemand
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a xMatters OnDemand potranno accedere all'applicazione tramite il sito aziendale di xMatters OnDemand (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione xMatters OnDemand
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione xMatters OnDemand
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per xMatters OnDemand.

###Per abilitare l'integrazione dell'applicazione per xMatters OnDemand, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **xMatters OnDemand**.

    ![Raccolta di applicazioni](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **XMatters OnDemand** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a XMatters OnDemand tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **XMatters OnDemand** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a XMatters OnDemand** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso XMatters OnDemand** usando il modello seguente "*https://\<nome-tenant>.XMattersOnDemand.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in XMatters OnDemand** per scaricare il file del certificato, fare clic su **Download certificato** e quindi salvarlo localmente come **C:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT]Inoltrare il certificato al team di supporto xMatters. Per poter completare la configurazione di Single Sign-On, è necessario che il certificato venga caricato dal team di supporto xMatters.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di XMatters OnDemand come amministratore.

6.  Sulla barra degli strumenti in alto fare clic su **Admin** e quindi su **Informazioni azienda** sulla barra di spostamento a sinistra.

    ![Admin](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7.  Nella pagina **Configurazione SAML** seguire la procedura seguente:

    ![SAML configuration](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML configuration")

    1.  Selezionare **Abilita SAML**.
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in XMatters OnDemand** del portale di Azure copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Identity Provider ID**.
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in XMatters OnDemand** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **Single Sign On URL**.
    4.  Nella finestra di dialogo **Configura accesso Single Sign-On in XMatters OnDemand** del portale di Azure copiare il valore di **URL servizio Single Sign-Out** e quindi incollarlo nella casella di testo **Single Logout URL**.
    5.  Nella parte superiore della pagina Informazioni sull’azienda fare clic su **Salva modifiche**. ![Company details](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Company details")

8.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a XMatters OnDemand, è necessario eseguirne il provisioning in XMatters OnDemand. Nel caso di XMatters OnDemand, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al tenant di **XMatters OnDemand**.

2.  Fare clic sulla scheda **Utenti**.

3.  Fare clic su **Aggiungi utente**.

    ![Users](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Users")

4.  Selezionare **Active**.

5.  Nella sezione **Aggiungi un utente** eseguire la procedura seguente:

    ![Add a User](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Add a User")

    1.  Nelle apposite caselle di testo immettere i valori di **UserID**, **First name**, **Last name** e **Site** di un account Azure AD valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Save**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da XMatters OnDemand per eseguire il provisioning degli account utente Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

###Per assegnare gli utenti a XMatters OnDemand, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **XMatters OnDemand** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->