<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con BambooHR | Microsoft Azure" description="Informazioni su come usare BambooHR con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con BambooHR
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=529837).

Questa esercitazione descrive l'integrazione di Azure e BambooHR. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di BambooHR abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a BambooHR potranno accedere all'applicazione tramite il sito aziendale di BambooHR (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per BambooHR
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per BambooHR

Questa sezione descrive come abilitare l'integrazione dell'applicazione per BambooHR.

###Per abilitare l'integrazione dell'applicazione per BambooHR, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **BambooHR**.

    ![Raccolta di applicazioni](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **BambooHR** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a BambooHR tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **BambooHR** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Scenario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a BambooHR** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso BambooHR** della pagina **Configura URL app**, digitare l'URL usato dagli utenti per accedere all'applicazione BambooHR, ad esempio: "*https://company.bamboohr.com), e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in BambooHR** fare clic su **Download certificato** e quindi salvare il file di certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di BambooHR come amministratore.

6.  Nella pagina iniziale seguire questa procedura:

    ![Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Single Sign-On")

    1.  Fare clic su **Apps**.
    2.  Scegliere **Single Sign-On** dal menu Apps a sinistra.
    3.  Fare clic su **SAML Single Sign-On**.

7.  Nella sezione **SAML Single Sign-On** seguire questa procedura:

    ![Single Sign-On SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Single Sign-On SAML")

    1.  Nella finestra di dialogo **Configura accesso Single Sign-On in BambooHR** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **SSO Login URL**.
2.  Creare un file **con codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    3.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **X.509 Certificate**
    4.  Fare clic su **Save**.

8.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a BambooHR, è necessario eseguirne il provisioning in BambooHR. Nel caso di BambooHR, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di **BambooHR** come amministratore.

2.  Nel barra degli strumenti in alto fare clic su **Settings**.

    ![Impostazione](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Impostazione")

3.  Fare clic su **Overview**.

4.  Nel riquadro di spostamento sinistro passare a **Security > Users**.

5.  Nelle caselle di testo corrispondenti digitare il nome utente, la password e l'indirizzo di posta elettronica di un account AAD valido di cui si vuole eseguire il provisioning.

6.  Fare clic su **Save**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da BambooHR per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a BambooHR, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **BambooHR** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->