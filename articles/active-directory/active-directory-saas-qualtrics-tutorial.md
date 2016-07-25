<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Qualtrics | Microsoft Azure" 
    description="Informazioni su come usare Qualtrics con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="07/07/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Qualtrics
  
In questa esercitazione viene illustrata l'integrazione di Azure e Qualtrics. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Qualtrics abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Qualtrics saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Qualtrics (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Qualtrics
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Qualtrics
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Qualtrics.

###Per abilitare l'integrazione dell'applicazione per Qualtrics, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Qualtrics**.

    ![Raccolta di applicazioni](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Qualtrics**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Qualtrics tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Qualtrics** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Qualtrics** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **Qualtrics Sign On URL** (URL di accesso a Qualtrics) ad esempio "*https://ssotest2ut1.qualtrics.com*", quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Qualtrics** fare clic su **Scarica metadati**, quindi salvare il file di metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati al team di supporto di Qualtrics.

    >[AZURE.NOTE]La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Qualtrics. Al termine della configurazione, viene ricevuta una notifica.

6.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Qualtrics. Quando un utente assegnato tenta di accedere a Qualtrics usando il pannello di accesso, Qualtrics verifica se l'utente esiste. Se l’account utente non è disponibile, viene creato automaticamente da Qualtrics.
##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Qualtrics, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Qualtrics** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->