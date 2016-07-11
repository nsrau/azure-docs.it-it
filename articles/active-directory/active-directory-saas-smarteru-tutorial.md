<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con SmarterU | Microsoft Azure" 
    description="Informazioni su come usare SmarterU con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="06/29/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con SmarterU
  
Questa esercitazione descrive l'integrazione di Azure e SmarterU. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Un tenant SmarterU
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SmarterU potranno accedere all'applicazione tramite il sito aziendale di SmarterU (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per SmarterU
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per SmarterU
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per SmarterU.

###Per abilitare l'integrazione dell'applicazione per SmarterU, eseguire la procedura seguente:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-smarteru-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-smarteru-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-smarteru-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **SmarterU**.

    ![Raccolta di applicazioni](./media/active-directory-saas-smarteru-tutorial/IC777321.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **SmarterU**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a SmarterU tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **SmarterU** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a SmarterU** selezionare **Single Sign-On di Microsoft Azure AD** e fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura accesso Single Sign-On in SmarterU**, fare clic su **Scarica metadati**, quindi salvare il file di dati localmente come **c:\\SmarterUMetaData.cer**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configura accesso Single Sign-On")

4.  In un'altra finestra del browser Web accedere al sito aziendale di SmarterU come amministratore.

5.  Nella barra degli strumenti in alto fare clic su **Impostazioni account**.

    ![Impostazioni account](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Impostazioni account")

6.  Nella pagina di configurazione dell’account, eseguire la procedura seguente:

    ![Autorità esterna](./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorità esterna")

    1.  Selezionare **Attiva autorizzazione esterna**.
    2.  Nella sezione **Controllo accesso master**, selezionare la scheda **SmarterU**.
    3.  Nella sezione **Accesso predefinito utente**, selezionare la scheda **SmarterU**.
    4.  Selezionare **Attiva Okta**.
    5.  Copiare il contenuto del file dei metadati scaricato e incollarlo nella casella di testo **Metadati Okta**.
    6.  Fare clic su **Save**.

7.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a SmarterU, è necessario eseguirne il provisioning in SmarterU. Nel caso di SmarterU, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al tenant **SmarterU**.

2.  Andare a **Utenti**.

3.  Nella sezione degli utenti, eseguire la procedura seguente:

    ![Nuovo utente](./media/active-directory-saas-smarteru-tutorial/IC777329.png "Nuovo utente")

    1.  Fare clic su **+Utente**.
    2.  Digitare i valori degli attributi correlati dell'account utente Azure AD nelle caselle di testo seguenti: **Posta elettronica principale** **ID dipendente** **Password** **Verifica password** **Nome** **Cognome**.
    3.  Fare clic su **Attivo**.
    4.  Fare clic su **Save**.

>[AZURE.NOTE] È possibile usare qualsiasi altro strumento di creazione di account utentedi SmarterU o API fornita da SmarterU per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a SmarterU, eseguire la procedura seguente:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **SmarterU** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-smarteru-tutorial/IC777330.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0629_2016-->