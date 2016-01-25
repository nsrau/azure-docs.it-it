<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con SimpleNexus| Microsoft Azure" 
    description="Informazioni su come usare SimpleNexus con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Esercitazione: Integrazione di Azure Active Directory con SimpleNexus
  
Questa esercitazione illustra l'integrazione di Azure e SimpleNexus. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Una sottoscrizione di SimpleNexus abilitata per l'accesso
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SimpleNexus potranno accedere all'applicazione tramite il sito aziendale di SimpleNexus (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per SimpleNexus
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per SimpleNexus
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per SimpleNexus.

###Per abilitare l'integrazione dell'applicazione per SimpleNexus, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **simple nexus**.

    ![Raccolta di applicazioni](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **SimpleNexus**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Simple Nexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Simple Nexus")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a SimpleNexus tramite il loro account in Azure AD utilizzando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **SimpleNexus** nel portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a SimpleNexus** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso a SimpleNexus** della pagina **Configura URL app** digitare l'URL usando il modello seguente "**https://simplenexus.com/CompanyName\_login*", quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in SimpleNexus** fare clic su **Scarica metadati** e inoltrare il file di metadati al team di supporto di SimpleNexus.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configura accesso Single Sign-On")

    >[AZURE.NOTE]L'accesso Single Sign-On deve essere abilitato dal team di supporto di SimpleNexus.

5.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere ad SimpleNexus, è necessario eseguirne il provisioning in SimpleNexus. Nel caso di SimpleNexus, il provisioning è un'attività manuale eseguita dall'amministratore tenant.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento di creazione di account utente di SimpleNexus o API fornita da SimpleNexus per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a SimpleNexus, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina dell’integrazione dell’applicazione **SimpleNexus** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->