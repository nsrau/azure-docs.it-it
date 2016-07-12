<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Tinfoil Security | Microsoft Azure"
    description="Informazioni su come usare Tinfoil Security con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="06/21/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Tinfoil Security
  
In questa esercitazione verrà illustrata l'integrazione di Azure e Tinfoil Security. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Tinfoil Security abilitata per l'accesso Single Sign-On.
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Tinfoil Security potranno accedere all'applicazione tramite il sito aziendale di Tinfoil Security (accesso avviato dal provider di identità) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Tinfoil Security
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Configura accesso Single Sign-On](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configura accesso Single Sign-On")

##Abilitazione dell'integrazione dell'applicazione per Tinfoil Security
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Tinfoil Security.

###Per abilitare l'integrazione dell'applicazione per Tinfoil Security, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Tinfoil Security**.

    ![Raccolta di applicazioni](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Tinfoil Security**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Tinfoil Security tramite il proprio account in Azure AD utilizzando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per Tinfoil Security richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Tinfoil Security** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Tinfoil Security** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app**, nella casella di testo **URL di risposta di Tinfoil Security** digitare l’URL del proprio servizio consumer di asserzione (ACS) Tinfoil Security (ad esempio: "*https://www.tinfoilsecurity.com/saml/consume*", quindi fare clic su **Avanti**.

    >[AZURE.NOTE] Dovrebbe essere possibile ottenere l'URL ACS dai metadati di Tinfoil Security (https://www.tinfoilsecurity.com/saml/metadata).

    ![Configura URL app](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Tinfoil Security**, per scaricare il file del certificato, fare clic su **Download certificato**, quindi salvarlo localmente come **C:\\Tinfoil Security.cer**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Tinfoil Security come amministratore.

6.  Nel barra degli strumenti in alto fare clic su **Account**.

    ![Dashboard](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")

7.  Fare clic su **Sicurezza**.

    ![Sicurezza](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Sicurezza")

8.  Nella pagina di configurazione **Single Sign-On** eseguire la procedura seguente:

    ![Single Sign-On](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Single Sign-On")

    1.  Selezionare **Abilita SAML**.
    2.  Fare clic su **Configurazione manuale**.
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in Tinfoil Security** del portale di Azure classico, copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **URL post SAML**.
    4.  Copiare il valore di **Identificazione personale** dal certificato esportato e incollarlo nella casella di testo **Impronta certificato SAML**.  

        >[AZURE.TIP] Per informazioni dettagliate, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    5.  Copiare l’**ID account**.
    6.  Fare clic su **Save**.

9.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configura accesso Single Sign-On")

10. Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**.

    ![Attributi](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributi")

11. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:

    ![Attributi](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributi")

    1.  Fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo**, digitare **accountid**.
    3.  Nella casella di testo **Valore attributo**, incollare il valore ID account copiato nella sezione precedente.
    4.  Fare clic su **Complete**.

12. Fare clic su **Applica modifiche**.

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Tinfoil Security, è necessario eseguirne il provisioning in Tinfoil Security. Nel caso di Tinfoil Security, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un utente, eseguire la procedura seguente:

1.  Se l'utente fa parte di un account aziendale, è necessario contattare il team di supporto Tinfoil Security per ottenere l'account utente creato.

2.  Se l'utente è un normale utente SaaS Tinfoil Security, può aggiungere un collaboratore a uno qualsiasi dei siti dell'utente. In tal modo viene attivato un processo per inviare un invito all’e-mail specificata per creare un nuovo account utente di Tinfoil Security.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento di creazione account utente Tinfoil Security o API fornita da Tinfoil Security per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Tinfoil Security, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Tinfoil Security** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0622_2016-->
