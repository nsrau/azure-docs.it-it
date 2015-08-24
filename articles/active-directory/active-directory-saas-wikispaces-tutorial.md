<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Wikispaces | Microsoft Azure" description="Informazioni su come usare Wikispaces con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Wikispaces
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=528443).
  
Questa esercitazione descrive l'integrazione di Azure e Wikispaces. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Wikispaces abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Wikispaces potranno accedere all'applicazione tramite il sito aziendale di Wikispaces (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione Wikispaces
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione Wikispaces
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Wikispaces.

###Per abilitare l'integrazione dell'applicazione per Wikispaces, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Wikispaces**.

    ![Raccolta di applicazioni](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Wikispaces** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Wikispaces tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Wikispaces** nel portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Wikispaces** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso Wikispaces** della pagina **Configura URL app** digitare l'URL usando il modello seguente "**http://company.wikispaces.net*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Wikispaces** fare clic su **Download metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configura accesso Single Sign-On")

5.  Inviare il file dei metadati al team di supporto di Wikispaces.

    >[AZURE.NOTE]La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Wikispaces. Al termine della configurazione, verrà visualizzata una notifica.

6.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Wikispaces, è necessario eseguirne il provisioning in Wikispaces. Nel caso di Wikispaces, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di **Wikispaces** come amministratore.

2.  Passare a **Members**.

    ![Members](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Members")

3.  Fare clic su **Invite People**.

    ![Invite People](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invite People")

4.  Nella sezione **Invite People** seguire questa procedura:

    ![Invite People](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invite People")

    1.  Nelle apposite caselle di testo immettere i valori di **Usernames o Email Address** di un account Azure AD valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Send**.  

        >[AZURE.NOTE]Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Wikispaces per eseguire il provisioning degli account utente Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

###Per assegnare gli utenti a Wikispaces, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Wikispaces** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Sì")
  
Se si desidera testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->