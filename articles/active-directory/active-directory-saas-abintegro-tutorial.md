<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Abintegro | Microsoft Azure" 
    description="Informazioni su come usare Abintegro con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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

#Esercitazione: Integrazione di Azure Active Directory con Abintegro

Questa esercitazione descrive l'integrazione di Azure e Abintegro. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione Abintegro abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad Abintegro potranno accedere all'applicazione tramite il sito aziendale di Abintegro (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Abintegro
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Abintegro

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Abintegro.

###Per abilitare l'integrazione dell'applicazione per Abintegro, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-abintegro-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **abintegro**.

    ![Raccolta di applicazioni](./media/active-directory-saas-abintegro-tutorial/IC790077.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Abintegro** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad Abintegro tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Abintegro** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Abintegro** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso a Abintegro** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere ad Abintegro, ad esempio "*`https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`, e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Abintegro** fare clic su **Scarica metadati** e quindi salvare il file di metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati al team di supporto di Abintegro.

    >[AZURE.NOTE]La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Abintegro. Al termine della configurazione, verrà visualizzata una notifica.

6.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Abintegro. Quando un utente assegnato tenta di accedere ad Abintegro usando il pannello di accesso, Abintegro verifica se l'utente esiste. Se l'account utente non è presente, Abintegro lo crea automaticamente.
##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti ad Abintegro, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Abintegro** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-abintegro-tutorial/IC790084.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-abintegro-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->