<properties pageTitle="Esercitazione: integrazione di Azure Active Directory con Lucidchart | Microsoft Azure" description="Informazioni su come utilizzare Lucidchart con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Lucidchart
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=532346).
  
In questa esercitazione viene illustrata l'integrazione di Azure e Lucidchart. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Lucidchart abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Lucidchart saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Lucidchart (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Lucidchart
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Lucidchart
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Lucidchart.

###Per abilitare l'integrazione dell'applicazione per Lucidchart, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Lucidchart**.

    ![Raccolta di applicazioni](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Lucidchart**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Lucidchart tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Lucidchart** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Lucidchart** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a Lucidchart** digitare l'URL utilizzato dagli utenti per accedere all’applicazione Lucidchart (ad esempio: "**https://chart2.office.lucidchart.com/saml/sso/azure*")), quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Lucidchart** fare clic su **Scarica metadati**, quindi salvare il file di dati localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Lucidchart come amministratore.

6.  Nel menu in alto fare clic su **Team**.

    ![Team](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")

7.  Fare clic su **Applicazione > Gestisci SAML**.

    ![Gestisci SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gestisci SAML")

8.  Nella pagina **Impostazioni di autenticazione SAML** eseguire la procedura seguente:

    1.  Selezionare **Abilita autenticazione SAML**, quindi fare clic su **Facoltativo**.![Impostazioni di autenticazione SAML](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Impostazioni di autenticazione SAML")
    2.  Nella casella di testo **Dominio**, digitare il dominio e fare clic su **Cambia certificato**.![Cambia certificato](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Cambia certificato")
    3.  Aprire il file dei metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **Carica metadati**.![Carica metadati](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Carica metadati")
    4.  Selezionare **Aggiungi automaticamente nuovo utente al team**, quindi fare clic su **Salva modifiche**.![Salva modifiche](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Save Changes")

9.  Selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Non è richiesta alcuna operazione per configurare il provisioning degli utenti in Lucidchart. Quando un utente assegnato tenta di accedere a Lucidchart utilizzando il pannello di accesso, Lucidchart controlla se l'utente esiste. Se l’account utente non è ancora disponibile, viene creato automaticamente da Lucidchart.
##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Lucidchart eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Lucidchart** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->