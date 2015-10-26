<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise | Microsoft Azure" description="Informazioni su come utilizzare Mozy Enterprise con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=524186).
  
In questa esercitazione viene illustrata l'integrazione di Azure e Mozy Enterprise. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant Mozy Enterprise
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Mozy Enterprise saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Mozy Enterprise (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Mozy Enterprise
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Mozy Enterprise
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Mozy Enterprise.

###Per abilitare l'integrazione dell'applicazione per Mozy Enterprise eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **mozy enterprise**.

    ![Raccolta di applicazioni](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Mozy Enterprise**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Mozy Enterprise tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare un file di certificato codificato Base 64 sul tenant Mozy Enterprise. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Mozy Enterprise** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Mozy Enterprise** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso a Mozy Enterprise** utilizzando il modello seguente "*https://\<tenant-name>.Mozyenterprise.com*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Mozy Enterprise** fare clic su **Download certificato** per scaricare il certificato, quindi salvare il file di certificato sul computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Mozy Enterprise come amministratore.

6.  Nella sezione **Configurazione** fare clic su **Criterio di autenticazione**.

    ![Criteri di autenticazione](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Criteri di autenticazione")

7.  Nella sezione **Criterio di autenticazione** eseguire la procedura seguente:

    ![Criteri di autenticazione](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Criteri di autenticazione")

    1.  Selezionare **Servizio directory** come **Provider**.
    2.  Selezionare **Utilizzare LDAP Push**.
    3.  Fare clic sulla scheda **Autenticazione SAML**.
    4.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Mozy Enterprise** del portale di Azure copiare il valore di **URL richiesta di autenticazione** e incollarlo nella casella di testo **URL di autenticazione**.
    5.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Mozy Enterprise** del portale di Azure copiare il valore di **ID provider di identità** e incollarlo nella casella di testo **Endpoint SAML**.
    6.  Creare un file **con codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    7.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificato SAML**.
    8.  Selezionare **Abilita SSO per consentire agli amministratori di accedere con le proprie credenziali di rete**.
    9.  Fare clic su **Salva modifiche**.

8.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Mozy Enterprise** del portale di Azure selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Completa**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Mozy Enterprise, è necessario eseguirne il provisioning in Mozy Enterprise. Nel caso di Mozy Enterprise, il provisioning è un’attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al tenant **Mozy Enterprise**.

2.  Fare clic su **Utenti**, quindi fare clic su **Aggiungi nuovo utente**.

    ![Utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Utenti")

    >[AZURE.NOTE]L’opzione **Aggiungi nuovo utente** viene visualizzata solo se **Mozy** è selezionato come provider in **Criteri di autenticazione**. Se è configurata l'autenticazione SAML gli utenti vengono aggiunti automaticamente al primo accesso tramite Single Sign-On.

3.  Nella finestra di dialogo Nuovo utente eseguire la procedura seguente:

    ![Aggiungi utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Aggiungi utenti")

    1.  Dall’elenco **Scegli gruppo** selezionare un gruppo.
    2.  Dall’elenco **Tipo di utente** selezionare un tipo.
    3.  Nella casella di testo **Nome utente** digitare il nome dell'utente di Azure AD.
    4.  Nella casella di testo **Indirizzo di posta elettronica** digitare l’indirizzo di posta elettronica dell'utente di Azure AD.
    5.  Selezionare **Invia messaggio di istruzioni all’utente**.
    6.  Fare clic su **Aggiungi utente**.

    >[AZURE.NOTE]Dopo aver creato l'utente, verrà inviato un messaggio di posta elettronica all'utente di Azure AD con un collegamento da selezionare per confermare l'account e attivarlo.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mozy Enterprise o le API fornite da Mozy Enterprise per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
 
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Mozy Enterprise, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell’applicazione di **Mozy Enterprise** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->