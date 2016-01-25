<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Zscaler | Microsoft Azure" 
    description="Informazioni su come usare Sprinklr con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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

#Esercitazione: Integrazione di Azure Active Directory con Sprinklr
  
Questa esercitazione descrive l'integrazione di Azure e Sprinklr. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Un tenant Sprinklr
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Sprinklr potranno accedere all'applicazione tramite il sito aziendale di Sprinklr (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Sprinklr
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per Sprinklr
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Sprinklr.

###Per abilitare l'integrazione dell'applicazione per Sprinklr, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Sprinklr**.

    ![Raccolta di applicazioni](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Sprinklr**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Sprinklr tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Sprinklr** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Sprinklr** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso Sprinklr** usando il modello seguente "*https://\<tenant-name>.sprinklr.com*", quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Sprinklr** fare clic su **Download certificato** per scaricare il file del certificato, quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Sprinklr come amministratore.

6.  Passare a **Amministrazione > Impostazioni**.

    ![Amministrazione](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Amministrazione")

7.  Andare a **Manage Partner > Single Sign** nel riquadro sinistro.

    ![Gestisci partner](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gestisci partner")

8.  Fare clic su **+ Add Single Sign Ons**.

    ![Accessi Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Accessi Single Sign-On")

9.  Nella pagina **Single Sign On** eseguire la procedura seguente:

    ![Accessi Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Accessi Single Sign-On")

    1.  Nella casella di testo **Name** digitare un nome per la configurazione (ad esempio *WAADSSOTest*).
    2.  Selezionare **Enabled**.
    3.  Selezionare **Use new SSO Certificate**.
    4.  Creare un file **con codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    5.  Aprire il certificato con codifica base 64 nel blocco note, copiarne il contenuto negli appunti e quindi incollarlo nella casella di testo **Identity Provider Certificate**.
    6.  Nella finestra di dialogo **Configura accesso Single Sign-On in Sprinklr** del portale di Azure copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Entity Id**.
    7.  Nella finestra di dialogo **Configura accesso Single Sign-On in Sprinklr** del portale di Azure copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Identity Provider Login URL**.
    8.  Nella finestra di dialogo **Configura accesso Single Sign-On in Sprinklr** del portale di Azure copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **URL accesso provider di identità**.
    9.  In **SAML User ID Type**, selezionare **Assertion contains User”s sprinklr.com username**.
    10. In **SAML User ID Location**, selezionare **User ID is in the Name Identifier element of the Subject statement**.
    11. Chiudere **Save**.

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la pagina **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di AAD di accedere, è necessario eseguirne il provisioning nell'applicazione Sprinklr. In questa sezione viene descritto come creare gli account utente AAD in Sprinklr.

###Per eseguire il provisioning di un account utente in Sprinklr, eseguire la procedura seguente:

1.  Accedere al sito aziendale di Sprinklr come amministratore.

2.  Passare a **Amministrazione > Impostazioni**.

    ![Amministrazione](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Amministrazione")

3.  Andare a **Manage Client > Users** dal riquadro di sinistra.

    ![Impostazioni](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Impostazioni")

4.  Fare clic su **Add User**.

    ![Impostazioni](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Impostazioni")

5.  Nella finestra di dialogo **Edit User** seguire la procedura seguente:

    ![Modifica utente](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Modifica utente")

    1.  Nelle caselle di testo **Posta elettronica**, **Nome** e **Cognome**, digitare le informazioni di un account utente di Microsoft Azure di cui si desidera eseguire il provisioning.
    2.  Selezionare **Password disabled**.
    3.  Selezionare una **Language**.
    4.  Selezionare un **User Type**.
    5.  Fare clic su **Update**.

    >[AZURE.IMPORTANT]L’opzione **Password disabilitata** deve essere selezionata per consentire agli utenti di accedere tramite un provider di identità.

6.  Passare a **Role** ed eseguire la procedura seguente:

    ![Ruoli partner](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Ruoli partner")

    1.  Dall’elenco **Globale**, selezionare **ALL\_Permissions**.
    2.  Fare clic su **Update**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento di creazione di account utente di Sprinklr o API fornita da Sprinklr per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Sprinklr,eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell’applicazione **SpringCM**, fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->