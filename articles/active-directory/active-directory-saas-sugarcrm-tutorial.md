<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con SugarCRM | Microsoft Azure" description="Informazioni su come usare SugarCRM con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con SugarCRM
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=534768).
  
In questa esercitazione verrà descritta l'integrazione di Azure e Sugar CRM. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Sugar CRM abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Sugar CRM potranno accedere all'applicazione tramite il sito aziendale di Sugar CRM (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione Sugar CRM
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione Sugar CRM
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Sugar CRM.

###Per abilitare l'integrazione dell'applicazione per Sugar CRM, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca**, digitare **Sugar CRM**.

    ![Raccolta di applicazioni](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Sugar CRM**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Sugar CRM tramite il proprio account in Azure AD utilizzando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare un file di certificato codificato in base 64 sul proprio tenant Sugar CRM. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Sugar CRM** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Sugar CRM** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso Sugar CRM** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Sugar CRM (ad esempio, **http://company.sugarondemand.com*"), quindi fare clic su **Avanti**:

    ![Configura URL app](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Sugar CRM** per scaricare il file del certificato, fare clic su **Download certificato**, quindi salvare il certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del browser Web accedere al sito aziendale di Sugar CRM come amministratore.

6.  Passare alla pagina **Admin**.

    ![Amministratore](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Amministratore")

7.  Nella sezione **Amministrazione**, fare clic su **Gestione password**.

    ![Amministrazione](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Amministrazione")

8.  Selezionare **Abilita autenticazione SAML**.

    ![Amministrazione](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Amministrazione")

9.  Nella sezione **Autenticazione SAML**, eseguire la procedura seguente:

    ![Autenticazione SAML](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "Autenticazione SAML")

    1.  Nella finestra di dialogo **Configura accesso Single Sign-On in Sugar CRM** del portale di Azure copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **URL di accesso**.
    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in Sugar CRM** del portale di Azure copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **URL SLO**.
    3.  Creare un file **con codifica Base 64** dal certificato scaricato.

        >[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificate X.509**.
    5.  Fare clic su **Salva**.

10. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Sugar CRM** del portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Sugar CRM, è necessario eseguirne il provisioning in Sugar CRM. Nel caso di Sugar CRM, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **Sugar CRM** come amministratore.

2.  Passare alla pagina **Admin**.

    ![Amministratore](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Amministratore")

3.  Nella sezione **Amministrazione**, fare clic su **Gestione utenti**.

    ![Amministrazione](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Amministrazione")

4.  Andare a **Utenti > Crea nuovo utente**.

    ![Creare nuovo utente](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Creare nuovo utente")

5.  Nella scheda **Profilo utente** eseguire la procedura seguente:

    ![Nuovo utente](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "Nuovo utente")

    1.  Digitare il nome utente, il cognome e l’indirizzo e-mail di utente di Azure Active Directory valido nelle caselle di testo correlate.

6.  Per **Stato** selezionare **Attivo**.

7.  Nella scheda Password, eseguire la procedura seguente:

    ![Nuovo utente](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "Nuovo utente")

    1.  Digitare la password nella casella di controllo correlata.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento di creazione di account utente Sugar CRM o qualsiasi API fornita da Sugar CRM per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Sugar CRM, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione di **Sugar CRM**, fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->