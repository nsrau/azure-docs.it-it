<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con ClickTime | Microsoft Azure" description="Informazioni su come usare ClickTime con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con ClickTime
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=%20524782).

Questa esercitazione descrive l'integrazione di Azure e ClickTime. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di ClickTime

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a ClickTime potranno accedere all'applicazione tramite il sito aziendale di ClickTime (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per ClickTime
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-clicktime-tutorial/IC777274.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per ClickTime

Questa sezione descrive come abilitare l'integrazione dell'applicazione per ClickTime.

###Per abilitare l'integrazione dell'applicazione per ClickTime, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-clicktime-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-clicktime-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-clicktime-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **ClickTime**.

    ![Raccolta di applicazioni](./media/active-directory-saas-clicktime-tutorial/IC777275.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **ClickTime** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a ClickTime tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 nel tenant di ClickTime. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT]Per poter configurare l'accesso Single Sign-On nel tenant di ClickTime, è necessario contattare prima di tutto il supporto tecnico ClickTime per abilitare questa funzionalità.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **ClickTime** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Abilitare l'autenticazione Single Sign-On](./media/active-directory-saas-clicktime-tutorial/IC777277.png "Abilitare l'autenticazione Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a ClickTime** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-clicktime-tutorial/IC777278.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura accesso Single Sign-On in ClickTime** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-clicktime-tutorial/IC777279.png "Configura accesso Single Sign-On")

4.  In un'altra finestra del Web browser accedere al sito aziendale di ClickTime come amministratore.

5.  Nella barra degli strumenti in alto fare clic su **Preferences** e quindi su **Security Settings**.

6.  Nella sezione di configurazione **Single Sign-On Preferences** seguire questa procedura:

    ![Security Settings](./media/active-directory-saas-clicktime-tutorial/IC777280.png "Security Settings")

    1.  Selezionare **Allow** sign-in using Single Sign-On (SSO) with **OneLogin**.
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in ClickTime** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **Identity Provider Endpoint**.
    3.  Creare un file con **codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    4.  Aprire il certificato con codifica Base 64 nel **Blocco note**, copiarne il contenuto e quindi incollarlo nella casella di testo **X.509 Certificate**.
    5.  Fare clic su **Save**.

7.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-clicktime-tutorial/IC777281.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a ClickTime, è necessario eseguirne il provisioning in ClickTime. Nel caso di ClickTime, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al tenant **ClickTime**.

2.  Nel barra degli strumenti in alto fare clic su **Company** e quindi su **People**.

    ![People](./media/active-directory-saas-clicktime-tutorial/IC777282.png "People")

3.  Fare clic su **Add Person**.

    ![Add Person](./media/active-directory-saas-clicktime-tutorial/IC777283.png "Add Person")

4.  Nella sezione New Person seguire questa procedura:

    ![People](./media/active-directory-saas-clicktime-tutorial/IC777284.png "People")

    1.  Nella casella di testo **email address** digitare l'indirizzo di posta elettronica dell'account Azure AD.
    2.  Nella casella di testo **full name** digitare il nome dell'account Azure AD.  

        >[AZURE.NOTE]È anche possibile impostare altre proprietà dell'oggetto new person.

    3.  Fare clic su **Save**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ClickTime per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a ClickTime, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell’applicazione **ClickTime** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-clicktime-tutorial/IC777285.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-clicktime-tutorial/IC767830.png "Sì")

Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->