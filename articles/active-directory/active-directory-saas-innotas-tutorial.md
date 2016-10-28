<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Innotas | Microsoft Azure"
    description="Informazioni su come usare Innotas con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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

#Esercitazione: Integrazione di Azure Active Directory con Innotas
  
Questa esercitazione descrive l'integrazione di Azure e Innotas. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Innotas
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Innotas potranno accedere all'applicazione tramite il sito aziendale di Innotas (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Innotas
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-innotas-tutorial/IC777331.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Innotas
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Innotas.

###Per abilitare l'integrazione dell'applicazione per Innotas, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-innotas-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-innotas-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-innotas-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Innotas**.

    ![Raccolta di applicazioni](./media/active-directory-saas-innotas-tutorial/IC777332.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Innotas** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Innotas](./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Innotas tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Innotas** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-innotas-tutorial/IC777334.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Innotas** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-innotas-tutorial/IC777335.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso Innotas** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<nome-tenant>.Innotas.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-innotas-tutorial/IC777336.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Innotas** fare clic su **Download metadati** per scaricare i metadati e salvare il file di dati localmente come **c:\\InnotasMetaData.xml**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-innotas-tutorial/IC777337.png "Configura accesso Single Sign-On")

5.  Inoltrare il file dei metadati al team di supporto di Innotas. La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto.

6.  Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-innotas-tutorial/IC777338.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Non è necessaria alcuna attività di configurazione per il provisioning degli utenti in Innotas. Quando un utente assegnato prova ad accedere a Innotas usando il pannello di accesso, Innotas verifica se l'utente esiste. Se l'account utente non è presente, Innotas lo crea automaticamente.
##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Innotas seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Innotas** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-innotas-tutorial/IC777339.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-innotas-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->