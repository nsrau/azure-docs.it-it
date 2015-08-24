<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con SuccessFactors | Microsoft Azure" description="Informazioni su come usare SuccessFactors con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con SuccessFactors
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=529792).
  
L'obiettivo di questa esercitazione è mostrare l'integrazione di Azure e SuccessFactors in**SP avviato modalità Single Sign-On**. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di SuccessFactors abilitata al Single Sign-On nella modalità avviata da SP
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SuccessFactors potranno accedere all'applicazione tramite il sito aziendale di SuccessFactors (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione SuccessFactors
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-successfactors-tutorial/IC791135.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione SuccessFactors
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per SuccessFactors.

###Per abilitare l'integrazione dell'applicazione per SuccessFactors, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-successfactors-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-successfactors-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-successfactors-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca**, digitare **SuccessFactors**.

    ![Raccolta di applicazioni](./media/active-directory-saas-successfactors-tutorial/IC791136.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **SuccessFactors**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a SuccessFactors tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.
  
Per configurare l'accesso Single Sign-On, è necessario contattare il team di supporto di SuccessFactors.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **SuccessFactors** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a SuccessFactors** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** eseguire la procedura seguente, quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-successfactors-tutorial/IC791140.png "Configura URL app")

    1.  Nella casella di testo **URL di accesso a SuccessFactors** digitare l'URL utilizzato dagli utenti per accedere all'applicazione SuccessFactors (ad esempio: "**https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*").
2.  Nella casella di testo **URL di risposta SuccessFactors** digitare ****https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**.

        >[AZURE.NOTE]Questo valore è solo un segnaposto temporaneo. Ottenere il valore effettivo del team di supporto di SuccessFactors. Più avanti in questa esercitazione sono disponibili le istruzioni su come contattare il team di SuccessFactors. Nel contesto della conversazione, viene fornito l'URL di risposta SuccessFactors effettivo.

4.  Nella pagina **Configura accesso Single Sign-On in SuccessFactors** per scaricare il file del certificato, fare clic su **Download certificato**, quindi salvare il certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Configura accesso Single Sign-On")

5.  Per configurare l’accesso Single Sign-On basato su SAML, contattare il team di supporto di SuccessFactors e fornire i seguenti elementi:

    1.  Il certificato scaricato
    2.  L'URL di accesso remoto
    3.  L'URL di disconnessione remota

    >[AZURE.IMPORTANT]Chiedere al team di supporto di SuccessFactors di impostare il parametro NameId Format su "*Unspecified*".

    Il team di supporto di Successfactors invierà il corretto**URL di risposta Successfactors** necessario per la finestra di dialogo **Configura URL app**.

6.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a SuccessFactors, è necessario eseguirne il provisioning in SuccessFactors. Nel caso di SuccessFactors, il provisioning è un'attività manuale.
  
Per creare utenti in SuccessFactors, è necessario contattare il team di supporto di SuccessFactors.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a SuccessFactors, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell’applicazione **SuccessFactors ** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-successfactors-tutorial/IC791143.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-successfactors-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->