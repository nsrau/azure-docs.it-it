<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con e-Builder | Microsoft Azure" 
    description="Informazioni su come usare e-Builder con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con e-Builder
  
Questa esercitazione descrive l'integrazione di Azure ed e-Builder. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di e-Builder
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a e-Builder potranno accedere all'applicazione tramite il sito aziendale di e-Builder (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per e-Builder
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-e-builder-tutorial/IC777378.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per e-Builder
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per e-Builder.

###Per abilitare l'integrazione dell'applicazione per e-Builder, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-e-builder-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-e-builder-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-e-builder-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **e-Builder**.

    ![Raccolta di applicazioni](./media/active-directory-saas-e-builder-tutorial/IC777379.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **e-Builder** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![e-Builder](./media/active-directory-saas-e-builder-tutorial/IC777380.png "e-Builder")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a e-Builder tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **e-Builder** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a e-Builder** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso e-Builder** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<nome-tenant>.e-Builder.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in e-Builder** fare clic su **Download metadati** per scaricare i metadati e salvare il file di dati localmente come **c:\\e-BuilderMetaData.xml**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configura accesso Single Sign-On")

5.  Inoltrare il file dei metadati al team di supporto di e-Builder. La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto.

6.  Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in e-Builder. Quando un utente assegnato prova ad accedere a e-Builder usando il pannello di accesso, e-Builder verifica se l'utente esiste. Se l'account utente non è presente, e-Builder lo crea automaticamente.
##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a e-Builder, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell’applicazione **e-Builder** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-e-builder-tutorial/IC777386.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-e-builder-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->