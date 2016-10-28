<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Zscaler ZSCloud| Microsoft Azure"
    description="Informazioni su come usare Zscaler ZSCloud con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />


#Esercitazione: Integrazione di Azure Active Directory con Zscaler ZSCloud
  
Questa esercitazione descrive l'integrazione di Azure e Zscaler ZSCloud. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di ZScaler ZSCloud abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Zscaler ZSCloud potranno accedere all'applicazione tramite il sito aziendale di Zscaler ZSCloud (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Zscaler ZSCloud
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione delle impostazioni proxy
4.  Configurazione del provisioning utente
5.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800275.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per Zscaler ZSCloud
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Zscaler ZSCloud.

###Per abilitare l'integrazione dell'applicazione per Zscaler ZSCloud, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Zscaler ZSCloud**.

    ![Raccolta di applicazioni](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800276.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Zscaler ZSCloud** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Zscaler ZSCloud](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800277.png "Zscaler ZSCloud")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Zscaler ZSCloud tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 sul proprio tenant Zscaler ZSCloud. Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **ZScaler ZSCloud** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800278.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Zscaler ZSCloud** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800279.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso Zscaler ZSCloud** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Zscaler ZSCloud e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800280.png "Configura URL app")

    >[AZURE.NOTE] Se necessario, è possibile ottenere il valore effettivo per l'ambiente in uso dal team di supporto Zscaler ZSCloud.

4.  Nella pagina **Configura accesso Single Sign-On in Zscaler ZSCloud** fare clic su **Scarica certificato** e quindi salvare il file di certificato sul computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800281.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Zscaler ZSCloud come amministratore.

6.  Scegliere **Amministrazione** dal menu disponibile nella parte superiore.

    ![Amministrazione](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800206.png "Amministrazione")

7.  In **Manage Administrators & Roles** fare clic su **Manage Users & Authentication**.

    ![Manage Users & Authentication](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800207.png "Manage Users & Authentication")

8.  Nella sezione **Choose Authentication Options for your Organization** seguire questa procedura:

    ![Autenticazione](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800208.png "Autenticazione")

    1.  Selezionare **Authenticate using SAML Single Sign-On**.
    2.  Fare clic su **Configure SAML Single Sign-On Parameters**.

9.  Nella pagina della finestra di dialogo **Configure SAML Single Sign-On Parameters** procedere come descritto di seguito e quindi fare clic su **Done**:

    ![Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800209.png "Single Sign-On")

    1.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in ZScaler ZSCloud** del portale di Azure classico copiare il valore **URL richiesta di autenticazione** e quindi incollarlo nella casella di testo **URL of the SAML Portal to which users are sent for authentication** (URL del portale di SAML a cui vengono indirizzati gli utenti per l'autenticazione).
    2.  Nella casella di testo **Attribute containing Login Name** digitare **NameID**.
    3.  Per caricare il certificato scaricato fare clic su **Zscaler pem**.
    4.  Selezionare **Enable SAML Auto-Provisioning**.

10. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:

    ![Amministrazione](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800210.png "Amministrazione")

    1.  Fare clic su **Save**.
    2.  Fare clic su **Activate Now**.

11. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in ZScaler ZSCloud** del portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800282.png "Configura accesso Single Sign-On")

##Configurazione delle impostazioni proxy

###Per configurare le impostazioni proxy in Internet Explorer

1.  Avviare **Internet Explorer**.

2.  Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.

    ![Opzioni Internet](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769492.png "Opzioni Internet")

3.  Fare clic sulla scheda **Connessioni**.

    ![Connessioni](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769493.png "Connessioni")

4.  Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.

5.  Nella sezione del server proxy seguire questa procedura:

    ![Server proxy](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769494.png "Server proxy")

    1.  Selezionare Usa un server di proxy per la rete LAN.
    2.  Nella casella di testo Indirizzo digitare **gateway.zscalerone.net**.
    3.  Nella casella di testo Porta digitare **80**.
    4.  Selezionare **Ignora server proxy per indirizzi locali**.
    5.  Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)**.

6.  Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Zscaler ZSCloud, è necessario eseguirne il provisioning in Zscaler ZSCloud. Nel caso di Zscaler ZSCloud, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, seguire questa procedura:

1.  Accedere al tenant **Zscaler**.

2.  Fare clic su **Administration**.

    ![Amministrazione](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781035.png "Amministrazione")

3.  Fare clic su **User Management**.

    ![Add](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Add")

4.  Nella scheda **Users** fare clic su **Add**.

    ![Add](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Aggiungi")

5.  Nella sezione Add User seguire questa procedura:

    ![Aggiunta di un utente](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781038.png "Aggiunta di un utente")

    1.  Digitare **UserID**, **User Display Name**, **Password** e **Confirm Password** e quindi selezionare **Groups** e **Department** di un account Azure AD valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Save**.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zscaler ZSCloud per eseguire il provisioning degli account utente Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Zscaler ZSCloud, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell’applicazione **Zscaler ZSCloud** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800283.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-zscaler-zscloud-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0817_2016-->