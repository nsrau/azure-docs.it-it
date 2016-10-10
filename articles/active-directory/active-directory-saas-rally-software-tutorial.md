<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Rally Software | Microsoft Azure" 
    description="Informazioni su come usare Rally Software con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Rally Software
  
In questa esercitazione viene illustrata l'integrazione di Azure e Rally Software. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant Rally Software
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Rally Software
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Rally Software
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Rally Software.

###Per abilitare l'integrazione dell'applicazione per Rally Software, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **rally**.

    ![Raccolta di applicazioni](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Rally Software**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Rally Software tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML. Per eseguire questa procedura, è necessario caricare un certificato in Rally Software.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Rally Software** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Rally** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Single Sign-On di Microsoft Azure AD")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL del tenant Rally Software** usando il modello seguente "*https://\<tenant-name>.rally.com*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Rally** fare clic su Scarica metadati, quindi salvare il file nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configura accesso Single Sign-On")

5.  Accedere al tenant **Rally Software**.

6.  Nella barra degli strumenti in alto fare clic su **Setup** (Configurazione), quindi selezionare **Subscription** (Sottoscrizione).

    ![Subscription](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Subscription")

7.  Scegliere il pulsante **Action** (Azione) nella barra degli strumenti in alto a destra e selezionare **Edit Subscription** (Modifica sottoscrizione).

8.  Nella finestra di dialogo **Subscription** (Sottoscrizione) eseguire la procedura seguente, quindi fare clic su **Save & Close** (Salva e chiudi):

    ![Autenticazione](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticazione")

    1.  Selezionare **Autenticazione Rally o SSO** dall'elenco a discesa Autenticazione
    2.  Nella finestra di dialogo **Configure single sign-on at Rally Software** (Configura accesso Single Sign-On in Rally Software) del portale di Azure classico copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Identity Provider URL** (URL provider di identità).
    3.  Nella finestra di dialogo **Configure single sign-on at Rally Software** (Configura accesso Single Sign-On in Rally Software) del portale di Azure classico copiare il valore **URL disconnessione remota**.

9.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere, è necessario eseguirne il privisioning nell’applicazione Rally Software utilizzando i relativi nomi utente di Azure Active Directory.

###Per configurare il provisioning utente, seguire questa procedura:

1.  Accedere al tenant Rally Software.

2.  Passare a **Impostazione > UTENTI** e quindi fare clic su **+ Aggiungi nuovo**.

    ![Utenti](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Utenti")

3.  Digitare il nome nella casella di testo Nuovo utente, quindi fare clic su**Aggiungi con dettagli**.

4.  Nella sezione **Crea utente**, eseguire la procedura seguente:

    ![Crea utente](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Crea utente")

    1.  Nella casella di testo **Nome utente**, digitare il nome dell'utente di Azure AD di cui si desidera eseguire il provisioning.
    2.  Nella casella di testo **Indirizzo di posta elettronica**, digitare l’indirizzo di posta elettronica dell'utente di Azure AD di cui si desidera eseguire il provisioning.
    3.  Fare clic su **Salva e chiudi**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Rally Software per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Rally Software, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Rally Software** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0928_2016-->