<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Envoy | Microsoft Azure" 
    description="Informazioni su come usare Envoy con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Envoy
  
Questa esercitazione descrive l'integrazione di Azure ed Envoy. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Envoy
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Envoy potranno accedere all'applicazione tramite il sito aziendale di Envoy (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Envoy
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Envoy
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Envoy.

###Per abilitare l'integrazione dell'applicazione per Envoy, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-envoy-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-envoy-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Envoy**.

    ![Raccolta di applicazioni](./media/active-directory-saas-envoy-tutorial/IC776760.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Envoy** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Envoy tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per Envoy richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Envoy** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Abilita Single Sign-On](./media/active-directory-saas-envoy-tutorial/IC776778.png "Abilitare l'autenticazione Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Envoy** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso Envoy** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<nome-tenant>.Envoy.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Envoy** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente come **C:\\Envoy.cer**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Envoy come amministratore.

6.  Nel barra degli strumenti in alto fare clic su **Impostazioni**.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  Fare clic su **Azienda**.

    ![Company](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")

8.  Fare clic su **SAML**.

    ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  Nella sezione di configurazione **SAML Authentication** seguire questa procedura:

    ![SAML Authentication](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML Authentication")

    >[AZURE.NOTE] Il valore dell'ID della sede centrale viene generato automaticamente dall'applicazione.

    1.  Copiare il valore di **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Impronta digitale**.

        >[AZURE.TIP] Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    2.  Nella finestra di dialogo **Configure single sign-on at Envoy** (Configura accesso Single Sign-On in Envoy) del portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Identity Provider HTTP SAML URL** (URL del provider di identità HTTP SAML).
    3.  Fare clic su **Salva modifiche**.

10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Non è necessaria alcuna attività di configurazione per il provisioning degli utenti in Envoy. Quando un utente assegnato prova ad accedere a Envoy usando il pannello di accesso, Envoy verifica se l'utente esiste. Se l'account utente non è presente, Envoy lo crea automaticamente.
##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Envoy, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Envoy** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-envoy-tutorial/IC776787.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-envoy-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->