<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con FreshService | Microsoft Azure" description="Informazioni su come usare FreshService con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con FreshService
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=529827).
  
Questa esercitazione descrive l'integrazione di Azure e FreshService. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di FreshService abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a FreshService potranno eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per FreshService
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per FreshService
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per FreshService.

###Per abilitare l'integrazione dell'applicazione per FreshService, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **FreshService**.

    ![Raccolta di applicazioni](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **FreshService** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Freshservice](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a FreshService tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per FreshService richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **FreshService** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a FreshService** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso FreshService** della pagina **Configura URL app**, digitare l'URL usato dagli utenti per accedere all'applicazione FreshService, ad esempio: "**http://democompany.freshservice.com/*"), e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in FreshService** per scaricare il file del certificato, fare clic su **Download certificato** e quindi salvarlo localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di FreshService come amministratore.

6.  Nel menu in alto fare clic su **Admin**.

    ![Amministratore](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Amministratore")

7.  In **Customer Portal** fare clic su **Security**.

    ![Sicurezza](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Sicurezza")

8.  Nella sezione **Security** seguire questa procedura:

    ![Single Sign On](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign On")

    1.  Impostare **Single Sign On** su ON.
    2.  Selezionare **SAML SSO**.
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in FreshService** del portale di Azure copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **SAML Login URL**.
    4.  Nella finestra di dialogo **Configura accesso Single Sign-On in FreshService** del portale di Azure copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Logout URL**.
    5.  Copiare il valore di **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Security Certificate Fingerprint**.
    
        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

9.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a FreshService, è necessario eseguirne il provisioning in FreshService. Nel caso di FreshService, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di **FreshService** come amministratore.

2.  Nel menu in alto fare clic su **Admin**.

    ![Amministratore](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Amministratore")

3.  Nella sezione **User Management** fare clic su **Requesters**.

    ![Requesters](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Requesters")

4.  Fare clic su **New Requester**.

    ![New Requesters](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")

5.  Nella sezione **New Requester** seguire questa procedura:

    ![New Requester](./media/active-directory-saas-freshservice-tutorial/IC790820.png "New Requester")

    1.  Nelle caselle di testo **First name** e **Email** immettere il nome e l'indirizzo di posta elettronica di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Save**.

    >[AZURE.NOTE]Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da FreshService per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a FreshService, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **FreshService** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->