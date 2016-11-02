<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Bime | Microsoft Azure" 
    description="Informazioni su come usare Bime con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Bime

Questa esercitazione descrive l'integrazione di Azure e Bime. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Bime

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Bime potranno accedere all'applicazione tramite il sito aziendale di Bime (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Bime
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-bime-tutorial/IC775552.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Bime

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Bime.

###Per abilitare l'integrazione dell'applicazione per Bime, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-bime-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-bime-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-bime-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Bime**.

    ![Raccolta di applicazioni](./media/active-directory-saas-bime-tutorial/IC775553.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Bime** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Bime](./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Bime tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per Bime richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Bime** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC771709.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Bime** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC775555.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso Bime** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<nome-tenant>.Bimeapp.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-bime-tutorial/IC775556.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Bime** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente come **C:\\Bime.cer**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC775557.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Bime come amministratore.

6.  Nella barra degli strumenti fare clic su **Admin** e quindi su **Account**.

    ![Amministratore](./media/active-directory-saas-bime-tutorial/IC775558.png "Amministratore")

7.  Nella pagina di configurazione dell’account, eseguire la procedura seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC775559.png "Configura accesso Single Sign-On")

    1.  Selezionare **Enable SAML authentication**.
    2.  Nella finestra di dialogo **Configure Single Sign-On at Bime** (Configura accesso Single Sign-On in Bime) del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **URL accesso remoto**.
    3.  Copiare il valore **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Impronta digitale del certificato**.

        >[AZURE.TIP] Per informazioni dettagliate, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

    4.  Fare clic su **Save**.

8.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC775560.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Bime, è necessario eseguirne il provisioning in Bime. Nel caso di Bime, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Accedere al tenant di **Bime**.

2.  Nella barra degli strumenti fare clic su **Admin** e quindi su **Users**.

    ![Amministratore](./media/active-directory-saas-bime-tutorial/IC775561.png "Amministratore")

3.  Nella **Lista utenti** fare clic su **Aggiungi nuovo utente** ("+").

    ![Utenti](./media/active-directory-saas-bime-tutorial/IC775562.png "Utenti")

4.  Nella finestra di dialogo **Dettagli utente** seguire questa procedura:

    ![User Details](./media/active-directory-saas-bime-tutorial/IC775563.png "User Details")

    1.  Nelle caselle di testo First Name, Last Name, Login, Email digitare nome, cognome, accesso e indirizzo di posta elettronica di un account AAD valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su Save.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Bime per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Bime, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Bime** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-bime-tutorial/IC775564.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-bime-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->