<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public | Microsoft Azure" 
    description="Informazioni su come usare TOPdesk - Public con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public

Questa esercitazione descrive l'integrazione di Azure e TOPdesk - Public. 
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di TOPdesk - Public abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TOPdesk - Public potranno accedere all'applicazione tramite il sito aziendale di TOPdesk - Public (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione TOPdesk - Public
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione TOPdesk - Public
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per TOPdesk - Public.

###Per abilitare l'integrazione dell'applicazione per TOPdesk - Public, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **TOPdesk - Public**.

    ![Raccolta di applicazioni](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **TOPdesk - Public** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![TOPdesk - Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk - Public")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a TOPdesk - Public tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. 
La configurazione dell'accesso Single Sign-On per TOPdesk - Public richiede il caricamento di un file con l'icona del logo. Per ottenere il file con l'icona, contattare il team di supporto di TOPdesk.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Accedere al sito aziendale di **TOPdesk - Public** come un amministratore.

2.  Nel menu **TOPdesk** fare clic su **Settings**.

    ![Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")

3.  Fare clic su **Login Settings**.

    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")

4.  Espandere il menu **Login Settings** e quindi fare clic su **General**.

    ![General](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

5.  Nell'area **Public** della sezione di configurazione **SAML login** seguire questa procedura:

    ![Technical Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")

    1.  Fare clic su **Download** per scaricare il file di metadati pubblici e quindi salvarlo in locale nel computer.
    2.  Aprire il file dei metadati e quindi individuare il nodo **AssertionConsumerService**.
![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  Copiare il valore di **AssertionConsumerService**.  

        >[AZURE.NOTE] Questo valore sarà necessario nella sezione **Configure App URL** più avanti in questa esercitazione.

6.  In un'altra finestra del Web browser accedere al portale di **Azure Active Directory** come amministratore.

7.  Nella pagina di integrazione dell'applicazione **TOPdesk - Public** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configura accesso Single Sign-On")

8.  Nella pagina **Stabilire come si desidera che gli utenti accedano a TOPdesk - Public** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configura accesso Single Sign-On")

9.  Nella pagina **Configura URL app** seguire questa procedura:

    ![Configura URL app](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configura URL app")

    1.  Nella casella di testo **URL di accesso TOPdesk - Public** digitare l'URL usato dagli utenti per accedere all'applicazione TOPdesk - Public (ad esempio,"*https://qssolutions.topdesk.net*").
    2.  Nella casella di testo **URL di risposta TOPdesk - Public** il valore di **TOPdesk - Public AssertionConsumerService URL** (ad esempio, "*https://qssolutions.topdesk.net/tas/public/login/saml*").
	3.  Fare clic su **Next**.

10. Nella pagina **Configura accesso Single Sign-On in TOPdesk - Public** per scaricare il file dei metadati, fare clic su **Download metadati** e quindi salvarlo in locale nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configura accesso Single Sign-On")

11. Per creare un file del certificato, seguire questa procedura:

    ![Certificate](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificate")

    1.  Aprire il file dei metadati scaricato.
    2.  Espandere il nodo **RoleDescriptor** con **xsi:type** uguale a **fed:ApplicationServiceType**.
    3.  Copiare il valore del nodo **X509Certificate**.
    4.  Salvare il valore copiato di **X509Certificate** in un file locale nel computer.

12. Nel sito della società TOPdesk - Public scegliere **Settings** dal menu **TOPdesk**.

    ![Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")

13. Fare clic su **Login Settings**.

    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")

14. Espandere il menu **Login Settings** e quindi fare clic su **General**.

    ![General](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

15. Nell'area **Public** fare clic su **Add**.

    ![SAML Login](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML Login")

16. Nella pagina **SAML configuration assistant** seguire questa procedura:

    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML Configuration Assistant")

    1.  Per caricare il file di metadati scaricato, in **Federation Metadata** fare clic su **Browse**.
    2.  Per caricare il file del certificato, in **Certificate (RSA)** fare clic su **Browse**.
    3.  Per caricare il file del logo ottenuto dal team di supporto di TOPdesk, in **Logo icon** fare clic su **Browse**.
    4.  Nella casella di testo **User name attribute** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
	5.  Nella casella di testo **Display name** digitare un nome per la configurazione.
    6.  Fare clic su **Save**.

17. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a TOPdesk - Public, è necessario eseguirne il provisioning in TOPdesk - Public.  
Nel caso di TOPdesk - Public, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, seguire questa procedura:

1.  Accedere al sito aziendale di **TOPdesk - Public** come amministratore.

2.  Nel menu in alto fare clic su **TOPdesk > New > Support Files > Person**.

    ![Person](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")

3.  Nella finestra di dialogo New Person seguire questa procedura:

    ![New Person](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "New Person")

    1.  Fare clic sulla scheda General.
    2.  Nella casella di testo Surname digitare il cognome di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
    3.  Selezionare un **Site** per l'account.
    4.  Fare clic su **Save**.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TOPdesk - Public per eseguire il provisioning degli account utente Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

###Per assegnare gli utenti a TOPdesk - Public, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **TOPdesk - Public** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0413_2016-->