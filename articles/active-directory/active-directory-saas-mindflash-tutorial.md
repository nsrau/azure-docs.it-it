<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Mindflash | Microsoft Azure" description="Informazioni su come utilizzare Mindflash con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Mindflash
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=528416).
  
In questa esercitazione viene illustrata l'integrazione di Azure e Mindflash. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Mindflash abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Mindflash saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Mindflash (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Mindflash
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-mindflash-tutorial/IC787132.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Mindflash
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Mindflash.

###Per abilitare l'integrazione dell'applicazione per Mindflash, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-mindflash-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-mindflash-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-mindflash-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Mindflash**.

    ![Raccolta di applicazioni](./media/active-directory-saas-mindflash-tutorial/IC787133.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Mindflash**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Mindflash tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Mindflash** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Mindflash** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso** utilizzando il modello seguente "**http://company.mindflash.com*", quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Mindflash** fare clic su **Scarica metadati**, quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configura accesso Single Sign-On")

5.  Inviare il file dei metadati al team di supporto di Mindflash.

    >[AZURE.NOTE]La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Mindflash. Al termine della configurazione, viene ricevuta una notifica.

6.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Mindflash, è necessario eseguirne il provisioning in Mindflash. Nel caso di Mindflash, il provisioning è un’attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **Mindflash** come amministratore.

2.  Passare alla pagina **Gestisci utenti**.

    ![Manage Users](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Gestisci utenti")

3.  Fare clic su **Aggiungi utenti**, quindi fare clic su **Nuovo**.

4.  Nella sezione **Aggiungi nuovi utenti** eseguire la procedura seguente:

    ![Aggiungi nuovi utenti](./media/active-directory-saas-mindflash-tutorial/IC787141.png "Aggiungi nuovi utenti")

    1.  Digitare il **nome**, il **cognome** e l’**indirizzo di posta elettronica** di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
    2.  Fare clic su **Aggiungi**.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mindflash o le API fornite da Mindflash per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Mindflash eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Mindflash** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-mindflash-tutorial/IC787142.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->