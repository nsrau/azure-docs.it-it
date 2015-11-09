<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con UserVoice | Microsoft Azure" 
    description="Informazioni su come usare UserVoice con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Esercitazione: Integrazione di Azure Active Directory con UserVoice
  
Questa esercitazione descrive l'integrazione di Azure e UserVoice. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di UserVoice
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a UserVoice potranno accedere all'applicazione tramite il sito aziendale di UserVoice (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione UserVoice
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione UserVoice
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per UserVoice.

###Per abilitare l'integrazione dell'applicazione per UserVoice, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **UserVoice**.

    ![Raccolta di applicazioni](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **UserVoice** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a UserVoice tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per UserVoice richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **UserVoice** nel portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a UserVoice** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso UserVoice** usando il modello seguente "*https://\<nome-tenant>.UserVoice.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in UserVoice** per scaricare il file del certificato, fare clic su **Download certificato** e quindi salvarlo localmente come **C:\\UserVoice.cer**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di UserVoice come amministratore.

6.  Sulla barra degli strumenti in alto fare clic su Settings, quindi selezionare Web portal dal menu.

    ![Impostazioni](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Settings")

7.  Nella sezione **Autenticazione utente** della scheda **portale Web** fare clic su **Modifica** per aprire la finestra di dialogo **Modifica autenticazione utente**.

    ![Web portal](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web portal")

8.  Nella finestra di dialogo **Modifica autenticazione utente** seguire questa procedura:

    ![Edit user authentication](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Edit user authentication")

    1.  Fare clic su **Single Sign-On (SSO)**.
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in UserVoice** del portale di Azure copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **SSO Remote Sign-In**.
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in UserVoice** del portale di Azure copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **SSO Remote Sign-Out**.
    4.  Copiare il valore **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Impronta digitale del certificato SHA1 corrente**.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

    5.  Fare clic su **Salva le impostazioni di autenticazione**.

9.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a UserVoice, è necessario eseguirne il provisioning in UserVoice. Nel caso di UserVoice, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al tenant di **UserVoice**.

2.  Passare a **Impostazioni**.

    ![Impostazioni](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Settings")

3.  Fare clic su **Generale**.

4.  Fare clic su **Agenti e autorizzazioni**.

    ![Agents and permissions](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents and permissions")

5.  Fare clic su **Aggiungi amministratori**.

    ![Add admins](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Add admins")

6.  Nella finestra di dialogo **Invita amministratori** seguire questa procedura:

    ![Invite admins](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invite admins")

    1.  Nella casella di testo Emails digitare l'indirizzo di posta elettronica dell'account di cui si vuole eseguire il provisioning e quindi fare clic su **Aggiungi**.
    2.  Fare clic su **Invita**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da UserVoice per eseguire il provisioning degli account utente Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

###Per assegnare gli utenti a UserVoice, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **UserVoice** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->