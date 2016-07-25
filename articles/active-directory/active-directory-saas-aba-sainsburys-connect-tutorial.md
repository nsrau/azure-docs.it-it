<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ABa Sainsburys Connect | Microsoft Azure" 
    description="Informazioni su come usare ABa Sainsburys Connect con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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

#Esercitazione: Integrazione di Azure Active Directory con ABa Sainsburys Connect

Questa esercitazione descrive l'integrazione di Azure e ABa Sainsburys Connect. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Aba Sainsburys Connect abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad Aba Sainsburys Connect potranno accedere all'applicazione tramite il sito aziendale di Aba Sainsburys Connect (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Aba Sainsburys Connect
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807723.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Aba Sainsburys Connect

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Aba Sainsburys Connect.

###Per abilitare l'integrazione dell'applicazione per Aba Sainsburys Connect, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Aba Sainsburys Connect**.

    ![ABa Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807724.png "ABa Sainsburys Connect")

7.  Nel riquadro dei risultati selezionare **Aba Sainsburys Connect** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![ABa Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807725.png "ABa Sainsburys Connect")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad Aba Sainsburys Connect tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Aba Sainsburys Connect** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo** Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807726.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Aba Sainsburys Connect** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807727.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configurare le impostazioni dell'app](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807728.png "Configurare le impostazioni dell'app")

    1.  Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Aba Sainsburys Connect (ad esempio: *https://myaba.co.uk/client-access/sainsburys/saml.php*).
    2.  Fare clic su **Avanti**

4.  Nella pagina **Configura accesso Single Sign-On in Aba Sainsburys Connect** fare clic su **Scarica metadati** e quindi salvare il file di metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807729.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati scaricato al team di supporto di Aba Sainsburys Connect.

    >[AZURE.NOTE] Il team di supporto di Aba Sainsburys Connect si occuperà dell'effettiva configurazione dell'accesso Single Sign-On. Al termine dell'abilitazione dell'accesso Single Sign-On per la sottoscrizione si riceverà una notifica.

6.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807730.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad Aba Sainsburys Connect, è necessario eseguirne il provisioning in Aba Sainsburys Connect. Nel caso di Aba Sainsburys Connect, gli account utente devono essere creati dal team di supporto di Aba Sainsburys Connect.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Aba Sainsburys Connect per eseguire il provisioning degli account utente di Azure Active Directory.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti ad Aba Sainsburys Connect, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Aba Sainsburys Connect** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807731.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->