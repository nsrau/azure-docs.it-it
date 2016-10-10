<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con RightAnswers | Microsoft Azure" 
    description="Informazioni su come usare RightAnswers con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con RightAnswers
  
In questa esercitazione viene illustrata l'integrazione di Azure e RightAnswers. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di RightAnswers abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a RightAnswers saranno in grado di eseguire l’accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per RightAnswers
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per RightAnswers
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per RightAnswers.

###Per abilitare l'integrazione dell'applicazione per RightAnswers, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **RightAnswers**.

    ![Raccolta di applicazioni](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **RightAnswers**, quindi fare clic su **Completa** per aggiungere l'applicazione.
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a RightAnswers tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **RightAnswers** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a RightAnswers** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configurare le impostazioni dell'app** digitare nella casella di testo **URL di accesso** l'URL usato dagli utenti per accedere all'applicazione RightAnswers, ad esempio *https://fortify.rightanswers.com/portal/ss/*, quindi fare clic su **Avanti**.

    ![Configurare le impostazioni dell'app](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configurare le impostazioni dell'app")

4.  Nella pagina **Configura accesso Single Sign-On in RightAnswers** fare clic su **Scarica metadati** per scaricare il file di metadati e salvare il file di metadati localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati scaricato al team di supporto di RightAnswers.

    >[AZURE.NOTE] La configurazione effettiva dell'accesso Single Sign-On deve essere eseguita dal team di supporto di RightAnswers. Una volta completata l'abilitazione dell'accesso Single Sign-On per la sottoscrizione, si riceverà una notifica.

6.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a RightAnswers, è necessario eseguirne il provisioning in RightAnswers. Nel caso di RightAnswers, il provisioning è un'attività automatica. Non è necessario eseguire alcuna operazione.
  
Se necessario, gli utenti vengono creati automaticamente durante il primo tentativo di accesso Single Sign-On.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da RightAnswers per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a RightAnswers, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **RightAnswers** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0928_2016-->