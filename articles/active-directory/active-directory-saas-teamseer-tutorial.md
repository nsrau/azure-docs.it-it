<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con TeamSeer | Microsoft Azure" description="Informazioni su come usare TeamSeer con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con TeamSeer
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=526800).
  
In questa esercitazione verrà illustrata l'integrazione di Azure e TeamSeer Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant TeamSeer
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TeamSeer potranno accedere all'applicazione tramite il sito aziendale di TeamSeer (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per TeamSeer
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per TeamSeer
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per TeamSeer.

###Per abilitare l'integrazione dell'applicazione per TeamSeer, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **TeamSeer**.

    ![Raccolta di applicazioni](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **TeamSeer**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a TeamSeer tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **TeamSeer** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a TeamSeer** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso TeamSeer** della pagina **Configura URL app** digitare l'URL usando il modello seguente "**http://www.teamseer.com/companyid*", quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in TeamSeer** per scaricare il file del certificato, fare clic su **Download certificato**, quindi salvarlo nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di TeamSeer come amministratore.

6.  Andare ad **Amministratore risorse umane**.

    ![Amministrazione risorse umane](./media/active-directory-saas-teamseer-tutorial/IC789634.png "Amministrazione risorse umane")

7.  Fare clic su **Configura**.

    ![Impostazione](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Impostazione")

8.  Fare clic su **Configura dettagli del provider SAML**.

    ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/IC789636.png "Impostazioni SAML")

9.  Nella sezione dei dettagli del provider SAML, eseguire la procedura seguente:

    ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/IC789637.png "Impostazioni SAML")

    1.  Nella finestra di dialogo **Configura accesso Single Sign-On in TeamSeer** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **URL**.
    2.  Creare un file **con codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    3.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificate pubblico IDP**.

10. Per completare la configurazione del provider SAML eseguire la procedura seguente:

    ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/IC789638.png "Impostazioni SAML")

    1.  In **Test indirizzi e-mail**, digitare l’indirizzo e-mail dell’utente test.
    2.  Nella casella di testo **Autorità di certificazione** digitare l'URL del provider di servizi.
    3.  Fare clic su **Salva**.

11. Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a TeamSeer, è necessario eseguirne il provisioning in TeamSeer. Nel caso di TeamSeer, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **TeamSeer** come amministratore.

2.  Eseguire la procedura seguente:

    ![Amministrazione risorse umane](./media/active-directory-saas-teamseer-tutorial/IC789640.png "Amministrazione risorse umane")

    1.  Andare ad **Amministratore risorse umane > Utenti**.
    2.  Fare clic su **Esegui procedura guidata nuovo utente**.

3.  Nella sezione **Dettagli utente** eseguire la procedura seguente:

    ![Dettagli utente](./media/active-directory-saas-teamseer-tutorial/IC789641.png "User Details")

    1.  Digitare il **Nome**, **Surname**, **Cognome (indirizzo e-mail)** di un account AAD valido di cui si desidera eseguire il provisioning nelle caselle correlate.
    2.  Fare clic su **Avanti**.

4.  Seguire le istruzioni visualizzate per l'aggiunta di un nuovo utente e fare clic su **Fine**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TeamSeer per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a TeamSeer, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell’applicazione **TeamSeer ** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->