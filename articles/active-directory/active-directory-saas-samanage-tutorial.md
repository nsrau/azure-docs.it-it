<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Samanage | Microsoft Azure" description="Informazioni su come usare Samanage con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Samanage
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=522516).
  
In questa esercitazione viene illustrata l'integrazione di Azure e Samanage. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant Samanage
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Samanage saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Samanage (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Samanage
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-samanage-tutorial/IC771705.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Samanage
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Samanage.

###Per abilitare l'integrazione dell'applicazione per Samanage, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-samanage-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-samanage-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-samanage-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Samanage**.

    ![Raccolta di applicazioni](./media/active-directory-saas-samanage-tutorial/IC771707.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Samanage**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Samanage](./media/active-directory-saas-samanage-tutorial/IC771708.png "Samanage")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Samanage tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Samanage** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/IC771709.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Samanage** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-samanage-tutorial/IC771710.png "Single Sign-On di Microsoft Azure AD")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso Samanage** usando il modello seguente "*https://\<tenant-name>.samanage.com*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-samanage-tutorial/IC771711.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Samanage** fare clic su **Scarica certificato**, quindi salvare il file di certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/IC777613.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Samanage come amministratore.

6.  Fare clic su **Dashboard** e selezionare **Impostazione** nel riquadro di spostamento a sinistra.

    ![Dashboard](./media/active-directory-saas-samanage-tutorial/IC771712.png "Dashboard")

7.  Fare clic su **Single Sign-On**.

    ![Single Sign-On](./media/active-directory-saas-samanage-tutorial/IC771713.png "Single Sign-On")

8.  Nella finestra di dialogo **Accedi usando SAML** eseguire la procedura seguente, quindi fare clic su **Salva e chiudi**:

    1.  Fare clic su **Abilita Single Sign-On con SAML**. 
        ![Accesso tramite SAML](./media/active-directory-saas-samanage-tutorial/IC771719.png "Accesso tramite SAML")
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in Samanage** del portale di Azure copiare il valore di **ID provider di identità** e incollarlo nella casella di testo **URL provider di identità**.
        ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/IC771720.png "Configura accesso Single Sign-On")
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in Samanage** del portale di Azure copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **URL accesso**.
    4.  Nella finestra di dialogo **Configura accesso Single Sign-On in Samanage** del portale di Azure copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **URL disconnessione**.
    5.  Creare un file **con codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    6.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato X.509**
    7.  Fare clic su **Crea utenti se non presenti in Samanage**.
        ![Aggiornamento](./media/active-directory-saas-samanage-tutorial/IC771722.png "Aggiornamento")
    8.  Fare clic su **Aggiorna**.

9.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-samanage-tutorial/IC771723.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Samanage, è necessario eseguirne il provisioning in Samanage. Nel caso di Samanage, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al tenant **Samanage**.

2.  Passare a **Dashboard > Impostazione**.

    ![Impostazione](./media/active-directory-saas-samanage-tutorial/IC771724.png "Impostazione")

3.  Fare clic sulla scheda **Utenti**.

    ![Utenti](./media/active-directory-saas-samanage-tutorial/IC771725.png "Utenti")

4.  Fare clic su **Nuovo utente**.

    ![New User](./media/active-directory-saas-samanage-tutorial/IC771726.png "Nuovo utente")

5.  Digitare l’**indirizzo di posta elettronica** e il **nome** dell’account Azure AD di cui si desidera eseguire il provisioning e fare clic su **Crea utente**.

    >[AZURE.NOTE]Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.

    ![Crea utente](./media/active-directory-saas-samanage-tutorial/IC771727.png "Crea utente")

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Samanage per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Samanage, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Samanage** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-samanage-tutorial/IC771728.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-samanage-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->