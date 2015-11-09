<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Kontiki | Microsoft Azure" 
    description="Informazioni su come usare Kontiki con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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

#Esercitazione: Integrazione di Azure Active Directory con Kontiki
  
Questa esercitazione descrive l'integrazione di Azure e Kontiki. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Kontiki abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Kontiki potranno accedere all'applicazione tramite il sito aziendale di Kontiki (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Kontiki
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Kontiki
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Kontiki.

###Per abilitare l'integrazione dell'applicazione per Kontiki, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Kontiki**.

    ![Raccolta di applicazioni](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Kontiki** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Kontiki tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Kontiki** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Kontiki** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso Kontiki** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere a Kontiki, ad esempio "*https://company.mc.eval.kontiki.com/*"), e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Kontiki** fare clic su **Download metadati** e quindi salvare il file di metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati al team di supporto di Kontiki.

    >[AZURE.NOTE]La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Kontiki. Al termine della configurazione, verrà visualizzata una notifica.

6.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Non è necessaria alcuna attività di configurazione per il provisioning degli utenti in Kontiki. Quando un utente assegnato prova ad accedere a Kontiki usando il pannello di accesso, Kontiki verifica se l'utente esiste. Se l'account utente non è presente, Kontiki lo crea automaticamente.
##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Kontiki, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Kontiki** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->