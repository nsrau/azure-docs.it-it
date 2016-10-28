<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Jitbit Helpdesk | Microsoft Azure" 
    description="Informazioni su come usare Jitbit Helpdesk con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Jitbit Helpdesk
  
Questa esercitazione descrive l'integrazione di Azure e Jitbit Helpdesk. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Jitbit Helpdesk
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Jitbit Helpdesk potranno accedere all'applicazione tramite il sito aziendale di Jitbit Helpdesk (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Jitbit Helpdesk
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Jitbit Helpdesk
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Jitbit Helpdesk.

###Per abilitare l'integrazione dell'applicazione per Jitbit Helpdesk, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Jitbit Helpdesk**.

    ![Raccolta di applicazioni](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Jitbit Helpdesk** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Jitbit Helpdesk tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Per eseguire questa procedura, è necessario creare un certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Per poter configurare l'accesso Single Sign-On nel tenant di Jitbit Helpdesk, è necessario innanzitutto contattare il supporto tecnico Jitbit Helpdesk per abilitare questa funzionalità.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Jitbit Helpdesk** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Jitbit Helpdesk** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso Jitbit Helpdesk** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<tenant-name>.Jitbit.com*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Jitbit Helpdesk** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente come **C:\\Jitbit Helpdesk.cer**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Jitbit Helpdesk come amministratore.

6.  Nel barra degli strumenti in alto fare clic su **Administration**.

    ![Amministrazione](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Amministrazione")

7.  Fare clic su **General settings**.

    ![Users, companies and permissions](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")

8.  Nella sezione di configurazione **Authentication settings** seguire questa procedura:

    ![Authentication settings](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Authentication settings")

    1.  Selezionare **Enable SAML 2.0 single sign on** per l'accesso Single Sign-On con **OneLogin**.
    2.  Nella finestra di dialogo **Configure single sign-on at Jitbit Helpdesk** (Configura accesso Single Sign-On in Jitbit Helpdesk) del portale di Azure classico copiare il valore di **Endpoint avviato da provider di servizi** e incollarlo nella casella di testo **EndPoint URL** (URL endpoint).
    3.  Creare un file **con codifica Base 64** dal certificato scaricato.
        
		>[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **X.509 Certificate**.
    5.  Fare clic su **Salva modifiche**.

9.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Jitbit Helpdesk, è necessario eseguirne il provisioning in Jitbit Helpdesk. Nel caso di Jitbit Helpdesk, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al tenant di **Jitbit Helpdesk**.

2.  Scegliere **Administration** dal menu disponibile nella parte superiore.

    ![Amministrazione](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Amministrazione")

3.  Fare clic su **Users, companies and permissions**.

    ![Users, companies and permissions](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")

4.  Fare clic su **Add User**.

    ![Add user](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Add user")

5.  Nella sezione Create immettere i dati dell'account Azure AD di cui si vuole eseguire il provisioning nelle caselle di testo seguenti: **Username**, **Email**, **First Name**, **Last Name**.

    ![Creazione](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Creazione")

6.  Fare clic su **Crea**.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Jitbit Helpdesk per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Jitbit Helpdesk, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Jitbit Helpdesk** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->