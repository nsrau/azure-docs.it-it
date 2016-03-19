<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Freshdesk | Microsoft Azure" 
    description="Informazioni su come usare Freshdesk con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Freshdesk
  
Questa esercitazione descrive l'integrazione di Azure e Freshdesk. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Freshdesk
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Freshdesk potranno accedere all'applicazione tramite il sito aziendale di Freshdesk (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Freshdesk
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Freshdesk
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Freshdesk.

###Per abilitare l'integrazione dell'applicazione per Freshdesk, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Freshdesk**.

    ![Raccolta di applicazioni](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Freshdesk** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Freshdesk tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per Freshdesk richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Freshdesk** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Freshdesk** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso Freshdesk** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<tenant-name>.Freshdesk.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Freshdesk** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente come **C:\\Freshdesk.cer**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Freshdesk come amministratore.

6.  Nel menu in alto fare clic su **Admin**.

    ![Amministratore](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Amministratore")

7.  Nella scheda **General Settings** fare clic su **Security**.

    ![Sicurezza](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sicurezza")

8.  Nella sezione **Security** seguire questa procedura:

    ![Single Sign On](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign On")

    1.  Per **Single Sign On (SSO)**, selezionare **On**.
    2.  Selezionare **SAML SSO**.
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in Freshdesk** del portale di Azure copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **SAML Login URL**.
    4.  Nella finestra di dialogo **Configura accesso Single Sign-On in Freshdesk** del portale di Azure copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Logout URL**.
    5.  Copiare il valore di **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Security Certificate Fingerprint**.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    6.  Fare clic su **Save**.

9.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Freshdesk, è necessario eseguirne il provisioning in Freshdesk. Nel caso di Freshdesk, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al tenant di **Freshdesk**.

2.  Nel menu in alto fare clic su **Admin**.

    ![Amministratore](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Amministratore")

3.  Nella scheda **General Settings** fare clic su **Agents**.

    ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4.  Fare clic su **New Agent**.

    ![New Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")

5.  Nella finestra di dialogo Agent Information seguire questa procedura:

    ![Agent Information](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")

    1.  Nella casella di testo **Full Name**, digitare il nome dell'account Azure AD di cui si vuole eseguire il provisioning.
    2.  Nella casella di testo **Email**, digitare l’indirizzo di posta elettronica dell'account Azure AD di cui si vuole eseguire il provisioning.
    3.  Nella casella di testo **Title**, digitare il titolo dell'account Azure AD di cui si vuole eseguire il provisioning.
    4.  Selezionare **Agents role** e quindi fare clic su **Assign**.
    5.  Fare clic su **Save**.
    
        >[AZURE.NOTE]Il titolare dell'account AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Freshdesk per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Freshdesk, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Freshdesk** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->