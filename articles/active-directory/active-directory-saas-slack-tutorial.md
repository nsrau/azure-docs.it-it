<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Slack | Microsoft Azure" 
    description="Informazioni su come usare Slack con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>  
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/19/2016" 
    ms.author="jeedes" />  

#Esercitazione: Integrazione di Azure Active Directory con Slack
  
Questa esercitazione descrive l'integrazione di Azure e Slack. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Slack abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Slack potranno accedere all'applicazione tramite il sito aziendale di Slack (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell’applicazione per Slack
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-slack-tutorial/IC794980.png "Scenario")

##Abilitazione dell'integrazione dell’applicazione per Slack
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Slack.

###Per abilitare l'integrazione dell'applicazione per Slack, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-slack-tutorial/IC700994.png "Applicazioni")  

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-slack-tutorial/IC749321.png "Aggiunta di un'applicazione")  

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-slack-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca**, digitare **Slack**.

    ![Raccolta di applicazioni](./media/active-directory-saas-slack-tutorial/IC794981.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Slack**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Scenario](./media/active-directory-saas-slack-tutorial/IC796925.png "Scenario")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Slack tramite il loro account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Slack** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/IC794982.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Slack** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/IC794983.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso Slack** della pagina **Configura URL app** digitare l'URL del tenant Slack, ad esempio "*https://azuread.slack.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-slack-tutorial/IC794984.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Slack** fare clic su **Download certificato** per scaricare il file del certificato e quindi salvarlo nel computer locale.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/IC794985.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del browser Web accedere al sito aziendale di Slack come amministratore.

6.  Andare a **Microsoft Azure AD > Impostazioni team**.

    ![Impostazioni team](./media/active-directory-saas-slack-tutorial/IC794986.png "Impostazioni team")

7.  Nella sezione **Impostazioni team** fare clic sulla scheda **Autenticazione**, quindi fare clic su **Cambia impostazioni**.

    ![Impostazioni team](./media/active-directory-saas-slack-tutorial/IC794987.png "Impostazioni team")

8.  Nella finestra di dialogo **Impostazioni di autenticazione SAML**, eseguire la procedura seguente:

    ![Impostazioni SAML](./media/active-directory-saas-slack-tutorial/IC794988.png "Impostazioni SAML")

    1.  Nella finestra di dialogo **Configura accesso Single Sign-On in Slack** del portale di Azure classico, copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **SAML 2.0 Endpoint (HTTP)** (Endpoint SAML 2.0 - HTTP).
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in Slack** del portale di Azure classico, copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Identity Provider Issuer** (Autorità di certificazione del provider di identità).
    3.  Creare un file **con codifica Base 64** dal certificato scaricato.
    
        >[AZURE.TIP] Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato pubblico**.
    5.  Deselezionare **Consenti agli utenti di modificare l'indirizzo di posta elettronica**.
    6.  Selezionare **Consenti agli utenti di scegliere il proprio nome utente**.
    7.  In **L’autenticazione per il team deve essere utilizzata da** selezionare**Facoltativo**.
    8.  Fare clic su **Salva configurazione**.

9.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-slack-tutorial/IC794989.png "Configura accesso Single Sign-On")  

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Slack, è necessario eseguirne il provisioning in Slack.
  
Non è necessaria alcuna attività di configurazione per il provisioning degli utenti in Slack. Quando un utente assegnato tenta di accedere a Slack, se necessario viene creato automaticamente un account Slack.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Slack, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Slack ** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-slack-tutorial/IC794990.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-slack-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0921_2016-->