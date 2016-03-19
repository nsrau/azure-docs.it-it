<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Greenhouse | Microsoft Azure" 
    description="Informazioni su come usare Greenhouse con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Greenhouse
  
Questa esercitazione descrive l'integrazione di Azure e Greenhouse. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione Greenhouse abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Greenhouse potranno accedere all'applicazione tramite il sito aziendale di Greenhouse (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Greenhouse
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Greenhouse
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Greenhouse.

###Per abilitare l'integrazione dell'applicazione per Greenhouse, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **greenhouse**.

    ![Raccolta di applicazioni](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Greenhouse** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Greenhouse tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Greenhouse** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Greenhouse** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso** della pagina **Configura URL app** digitare l'URL usando il modello seguente "**https://company.greenhouse.io*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Greenhouse** fare clic su **Download metadati** e quindi salvare il file di metadati in locale nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configura accesso Single Sign-On")

5.  Inoltrare il file dei metadati al team di supporto di Greenhouse.

    >[AZURE.NOTE]L’accesso Single Sign-On deve essere abilitato dal team di supporto di Greenhouse.

6.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Greenhouse, è necessario eseguirne il provisioning in Greenhouse. Nel caso di Greenhouse, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di **Greenhouse** come amministratore.

2.  Nel menu in alto fare clic su **Configure** e quindi fare clic su **Users**.

    ![Utenti](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Utenti")

3.  Fare clic su **Nuovi utenti**.

    ![New User](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "New User")

4.  Nella sezione **Aggiungi nuovo utente** eseguire la procedura seguente:

    ![Add New User](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Add New User")

    1.  Nella casella di testo **Inserire email utente** digitare l'indirizzo di posta elettronica di un account di Azure Active Directory valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Save**.
        
		>[AZURE.NOTE]I titolari dell'account Azure Active Directory riceveranno un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Greenhouse per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Greenhouse, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Greenhouse** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->