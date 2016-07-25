<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Projectplace | Microsoft Azure" 
    description="Informazioni su come usare Projectplace con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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

#Esercitazione: Integrazione di Azure Active Directory con Projectplace
  
In questa esercitazione viene illustrata l'integrazione di Azure e Projectplace. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Projectplace abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Projectplace saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Projectplace (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Projectplace
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Projectplace
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Projectplace.

###Per abilitare l'integrazione dell'applicazione per Projectplace, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca**, digitare **Projectplace**.

    ![Raccolta di applicazioni](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Projectplace**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![ProjectPlace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Projectplace tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Projectplace** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Projectplace** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app**, nella casella di testo **Projectplace Sign On URL** (URL di accesso a Projectplace) digitare l'URL del tenant Projectplace, ad esempio "*http://company.projectplace.com*", quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Projectplace** fare clic su **Scarica metadati**, quindi salvare il file di metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati al team di supporto di Projectplace.

    >[AZURE.NOTE] La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Projectplace. Al termine della configurazione, viene ricevuta una notifica.

6.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Projectplace, è necessario eseguirne il provisioning in Projectplace. Nel caso di Projectplace, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **Projectplace** come amministratore.

2.  Passare a **Persone**, quindi fare clic su **Membri**.

    ![Persone](./media/active-directory-saas-projectplace-tutorial/IC790228.png "Persone")

3.  Fare clic su **Aggiungi membro**.

    ![Aggiungi membri](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Aggiungi membri")

4.  Nella sezione **Aggiungi membro**, eseguire la procedura seguente:

    ![Nuovi membri](./media/active-directory-saas-projectplace-tutorial/IC790233.png "Nuovi membri")

    1.  Nella casella di testo **Nuovi membri**, digitare l’indirizzo di posta elettronica di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
    2.  Fare clic su **Invia**.

	    >[AZURE.NOTE] Un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account, viene inviato al titolare dell'account Azure Active Directory.
    
>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Projectplace per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Projectplace, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Projectplace** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->