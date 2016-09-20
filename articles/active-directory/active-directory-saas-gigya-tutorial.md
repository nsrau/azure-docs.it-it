<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Gigya | Microsoft Azure" 
    description="Informazioni su come usare Gigya con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />  

#Esercitazione: Integrazione di Azure Active Directory con Gigya
  
Questa esercitazione descrive l'integrazione di Azure e Gigya. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione Gigya abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Gigya potranno accedere all'applicazione tramite il sito aziendale di Gigya (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Gigya
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Configura accesso Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configura accesso Single Sign-On")
##Abilitazione dell'integrazione dell'applicazione per Gigya
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Gigya.

###Per abilitare l'integrazione dell'applicazione per Gigya, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-gigya-tutorial/IC700994.png "Applicazioni")  

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-gigya-tutorial/IC749321.png "Aggiunta di un'applicazione")  

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-gigya-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Gigya**.

    ![Raccolta di applicazioni](./media/active-directory-saas-gigya-tutorial/IC789513.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Gigya** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad Gigya tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Gigya** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configura accesso Single Sign-On")  

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Gigya** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configura accesso Single Sign-On")  

3.  Nella casella di testo **URL di accesso** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*http://company.gigya.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-gigya-tutorial/IC789530.png "Configura URL app")  

4.  Nella pagina **Configura accesso Single Sign-On in Gigya** fare clic su **Download certificato** e quindi salvare il file di certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configura accesso Single Sign-On")  

5.  In un'altra finestra del Web browser accedere al sito aziendale di Sign On come amministratore.

6.  Passare a **Settings > SAML Login**, quindi fare clic sul pulsante **Add**.

    ![SAML Login](./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML Login")  

7.  Nella sezione **SAML Login** seguire questa procedura:

    ![SAML Configuration](./media/active-directory-saas-gigya-tutorial/IC789533.png "SAML Configuration")  

    1.  Nella casella di testo **Name** digitare un nome per la configurazione.
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in Gigya** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e quindi incollarlo nella casella di testo **Issuer** (Autorità di certificazione).
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in Gigya** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **Single Sign-On Service URL** (URL servizio Single Sign-On).
    4.  Nella pagina **Configura accesso Single Sign-On in Gigya** nel portale di Azure classico copiare il valore di **Formato identificatore nome** e quindi incollarlo nella casella di testo **Name ID Format** (Formato ID nome).
    5.  Creare un file con **codifica Base 64** dal certificato scaricato.
        
		>[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    6.  Aprire il certificato con codifica base 64 nel blocco note, copiarne il contenuto negli appunti e incollarlo nella casella di testo **Certificato X.509**.
    7.  Fare clic su **Salva impostazioni**.

8.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configura accesso Single Sign-On")  
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Gigya, è necessario eseguirne il provisioning in Gigya. Nel caso di Gigya, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **Gigya** come amministratore.

2.  Passare a **Admin > Manage Users** e quindi fare clic su **Invite Users**.

    ![Gestisci utenti](./media/active-directory-saas-gigya-tutorial/IC789535.png "Gestisci utenti")  

3.  Nella finestra di dialogo Invite Users seguire questa procedura:

    ![Invite Users](./media/active-directory-saas-gigya-tutorial/IC789536.png "Invite Users")  

    1.  Nella casella di testo **Email** digitare l'alias di posta elettronica di un account di Azure Active Directory valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Invite User**.
    
        >[AZURE.NOTE] Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Gigya eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Gigya, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella **pagina di integrazione dell'applicazione Gigya** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-gigya-tutorial/IC789537.png "Assegna utenti")  

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-gigya-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0907_2016-->