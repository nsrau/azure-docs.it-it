<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con AnswerHub | Microsoft Azure" 
    description="Informazioni su come usare AnswerHub con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="07/19/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con AnswerHub

Questa esercitazione descrive l'integrazione di Azure e [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software). Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad AnswerHub potranno accedere all'applicazione tramite il sito aziendale di AnswerHub (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per AnswerHub
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per AnswerHub

Questa sezione descrive come abilitare l'integrazione dell'applicazione per AnswerHub.

###Per abilitare l'integrazione dell'applicazione per AnswerHub, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **AnswerHub**.

    ![Raccolta di applicazioni](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **AnswerHub** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad AnswerHub tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **AnswerHub** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a AnswerHub** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso AnswerHub** della pagina **Configura URL app** digitare l'URL usando il modello "*https://company.answerhub.com*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in AnswerHub** fare clic su **Download certificato** e infine salvare il file di certificato localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di AnswerHub come amministratore.
    >[AZURE.NOTE] Per ottenere assistenza nella configurazione di AnswerHub, contattare il [team di supporto di AnswerHub](mailto:success@answerhub.com.).








6.  Passare a **Administration**.

7.  Fare clic sulla scheda **User and Group**.

8.  Nel riquadro di spostamento a sinistra, passare alla sezione **Social Settings** e fare clic su **SAML Setup**.

9.  Fare clic sulla scheda **IDP Config**.

10. Nella scheda **IDP Config** seguire questa procedura:

    ![SAML Setup](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")

    1.  Nella finestra di dialogo **Configura accesso Single Sign-On in AnswerHub** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **IDP Login URL** (URL di accesso IdP).
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in AnswerHub** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **IDP Logout URL** (URL disconnessione IdP).
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in AnswerHub** del portale di Azure classico copiare il valore di **Formato identificatore nome** e incollarlo nella casella di testo **IDP Name Identifier Format** (Formato identificatore nome IdP).
    4.  Fare clic su **Keys and Certificates**.

11. Nella scheda Keys and Certificates seguire questa procedura:

    ![Keys and Certificates](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")

    1.  Creare un file **con codifica Base 64** dal certificato scaricato.

		>[AZURE.TIP] Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    2.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **IDP Public Key (x509 Format)**.
    3.  Fare clic su **Save**.

12. Nella scheda **IDP Config** fare clic su **Save**.

13. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad AnswerHub, è necessario eseguirne il provisioning in AnswerHub. Nel caso di AnswerHub, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **AnswerHub** come amministratore.

2.  Passare a **Administration**.

3.  Fare clic sulla scheda **Users & Groups**.

4.  Nel riquadro di spostamento a sinistra fare clic su **Create or import users** nella sezione **Manage users**.

    ![Users & Groups](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")

5.  Nelle caselle di testo **Email address**, **Username** e **Password** digitare l'indirizzo di posta elettronica, il nome utente e la password di un account utente Azure Active Directory valido di cui si vuole eseguire il provisioning e quindi fare clic su **Save**.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da AnswerHub per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti ad AnswerHub, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **AnswerHub** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0720_2016-->