<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con New Relic | Microsoft Azure" description="Informazioni su come utilizzare New Relic con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con New Relic
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=536553).
  
In questa esercitazione viene illustrato come configurare l’accesso Single Sign-On tra Azure Active Directory e New Relic.
  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di New Relic abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure Active Directory assegnati a New Relic saranno in grado di eseguire l’accesso Single Sign-On tramite il pannello di accesso di AAD.

1.  Abilitazione dell'integrazione dell'applicazione per New Relic
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per New Relic
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per New Relic.

###Per abilitare l'integrazione dell'applicazione per New Relic, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **New Relic**.

    ![Raccolta di applicazioni](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **New Relic**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a New Relic tramite il relativo account in Azure Active Directory utilizzando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **New Relic** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a New Relic** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso New Relic** della pagina **Configura URL app** digitare l'URL utilizzato dagli utenti per accedere all'applicazione New Relic, quindi fare clic su **Avanti**.

    L'URL dell’app è l'URL del tenant New Relic (ad esempio: **https://rpm.newrelic.com*):

    ![Configura URL app](./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in New Relic** fare clic su **Download certificato** per scaricare il file del certificato, quindi salvarlo localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di **New Relic** come amministratore.

6.  Nel menu in alto fare clic su **Impostazioni account**.

    ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Impostazioni account")

7.  Fare clic sulla scheda **Protezione e autenticazione **, quindi fare clic sulla scheda **Single Sign-On**.

    ![Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")

8.  Nella pagina della finestra di dialogo SAML eseguire la procedura seguente:

    ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Fare clic su **Scegli file** per caricare il certificato di Azure Active Directory scaricato.
    2.  Nella pagina **Configura accesso Single Sign-On in New Relic** del portale di Microsoft Azure copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **URL accesso remoto**.
    3.  Nella pagina **Configura accesso Single Sign-On in New Relic** del portale di Microsoft Azure copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **URL pagina di destinazione disconnessione**.
    4.  Fare clic su **Salva modifiche**.

9.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure Active Directory di accedere a New Relic, è necessario eseguirne il provisioning in New Relic. Nel caso di New Relic, il provisioning è un’attività manuale.

###Per eseguire il provisioning di un account utente in New Relic, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **New Relic** come amministratore.

2.  Nel menu in alto fare clic su **Impostazioni account**.

    ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Impostazioni account")

3.  Nel riquadro a sinistra **Account** fare clic su **Riepilogo**, quindi fare clic su **Aggiungi utente**.

    ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Impostazioni account")

4.  Nella finestra di dialogo **Utenti attivi** eseguire la procedura seguente:

    ![Utenti attivi](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Utenti attivi")

    1.  Nella casella di testo **Posta elettronica** digitare l'indirizzo di posta elettronica di un utente valido di Azure Active Directory di cui si desidera eseguire il provisioning.
    2.  Come **Ruolo** selezionare **Utente**.
    3.  Fare clic su **Aggiungi questo utente**.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro strumento di creazione di account utente di New Relic o le API fornite da New Relic per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a New Relic eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **New Relic** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->