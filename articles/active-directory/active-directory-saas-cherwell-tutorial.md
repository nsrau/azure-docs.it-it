<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Cherwell | Microsoft Azure" 
    description="Informazioni su come usare Cherwell con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="06/17/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Cherwell

Questa esercitazione descrive l'integrazione di Azure e Cherwell. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Cherwell abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Cherwell potranno accedere all'applicazione tramite il sito aziendale di Cherwell (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Cherwell
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Cherwell

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Cherwell.

###Per abilitare l'integrazione dell'applicazione per Cherwell, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Cherwell**.

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  Nel riquadro dei risultati selezionare **Cherwell** e quindi fare clic su **Completa** per aggiungere l'applicazione.
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Cherwell tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Cherwell** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Cherwell** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** seguire questa procedura:

    ![Configura URL app](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configura URL app")

    1.  Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione **Cherwell**, ad esempio: **https://pictdev.cherwellondemand.com/cherwellclient*).
2.  Fare clic su **Avanti**

4.  Nella pagina **Configura accesso Single Sign-On in Cherwell** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configura accesso Single Sign-On")

    1.  Fare clic su **Download certificato** e quindi salvare il certificato nel computer locale.
    2.  Copiare il valore di **URL provider di identità**.
    3.  Copiare il valore di **URL servizio Single Sign-On**.
    4.  Fare clic su **Avanti**.

5.  Inviare il certificato scaricato il valore di **URL provider di identità** e il valore di **URL servizio Single Sign-On** al team di supporto di Cherwell.

    >[AZURE.NOTE] Il team di supporto di Cherwell si occuperà dell'effettiva configurazione dell'accesso Single Sign-On. Al termine dell'abilitazione dell'accesso Single Sign-On per la sottoscrizione si riceverà una notifica.

6.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Cherwell, è necessario eseguirne il provisioning in Cherwell. Nel caso di Cherwell, gli account utente devono essere creati dal team di supporto di Cherwell.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Cherwell per eseguire il provisioning degli account utente di Azure Active Directory.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Cherwell, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Cherwell** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0622_2016-->