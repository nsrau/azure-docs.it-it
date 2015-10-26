<properties pageTitle="Esercitazione: integrazione di Azure Active Directory con Thirdlight | Microsoft Azure" description="Informazioni su come usare Thirdlight con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Thirdlight
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=529835).
  
In questa esercitazione viene descritta l'integrazione di Azure e Thirdlight. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Thirdlight abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Thirdlight saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Thirdlight (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Thirdlight
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per Thirdlight
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Thirdlight.

###Per abilitare l'integrazione dell'applicazione per Thirdlight, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca**, digitare **Thirdlight**.

    ![Raccolta di applicazioni](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Thirdlight**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![ThirdLight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Thirdlight tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per Thirdlight richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che illustra [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Thirdlight** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a TeamSeer** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso a Thirdlight** della pagina **Configura URL app**, digitare l'URL usato dagli utenti per accedere all'applicazione Thirdlight, ad esempio: "**http://azuresso2.thirdlight.com/*"), quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Thirdlight**, per scaricare il file di metadati fare clic su **Download metadati**, quindi salvare il file di metadati in locale nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Thirdlight come amministratore.

6.  Andare a **Configurazione > Amministrazione sistema**, quindi fare clic su **SAML2**.

    ![System Administration](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Amministrazione di sistema")

7.  Nella sezione di configurazione di SAML2, eseguire la procedura seguente:

    ![Single Sign-On SAML](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "Single Sign-On SAML")

    1.  Selezionare **Attiva Single Sign-On SAML2**.
    2.  Per **Origine per i metadati IDP**, selezionare **Carica metadati IDP da XML**.
    3.  Aprire il file dei metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **XML metadati IDP**.
    4.  Fare clic su **Salva impostazioni SAML2**.

8.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Thirdlight, è necessario eseguirne il provisioning in Thirdlight. Nel caso di Thirdlight, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **Thirdlight** come amministratore.

2.  Andare alla scheda **Utenti**.

3.  Selezionare **Utenti e gruppi**.

4.  Fare clic sul pulsante **Aggiungi nuovo utente**.

5.  Immettere **nome utente, nome o descrizione, posta elettronica, scegliere un set predefinito o un gruppo di nuovi membri** di un account valido di AAD di cui si desidera eseguire il provisioning.

6.  Fare clic su **Crea**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Thirdlight per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Thirdlight, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Thirdlight** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->