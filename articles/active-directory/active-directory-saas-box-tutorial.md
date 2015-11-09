<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Box | Microsoft Azure" 
    description="Informazioni su come usare Box con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />




#Esercitazione: Integrazione di Azure Active Directory con Box


  
Questa esercitazione descrive l'integrazione di Azure e Box. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di test in Box
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Box potranno accedere all'applicazione tramite il sito aziendale di Box (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Box
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-box-tutorial/IC769537.png "Scenario")



##Abilitazione dell'integrazione dell'applicazione per Box
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Box.

###Per abilitare l'integrazione dell'applicazione per Box, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-box-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-box-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-box-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Box**.

    ![Raccolta di applicazioni](./media/active-directory-saas-box-tutorial/IC701023.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Box** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Box tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. <br> Come parte di questa procedura, verrà richiesto di caricare metadati in Box.com.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Box** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-box-tutorial/IC769538.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Box** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-box-tutorial/IC769539.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL tenant Box** della pagina **Configura URL app** digitare l'URL del tenant Box (ad esempio: https://<mydomainname>.box.com) e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-box-tutorial/IC669826.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Box** per scaricare il file dei metadati fare clic su **Download metadati** e quindi salvarlo in locale nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-box-tutorial/IC669824.png "Configura accesso Single Sign-On")

5.  Inoltrare il file dei metadati al team di supporto di Box. La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto.

6.  Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-box-tutorial/IC769540.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in Box.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1. Nella pagina di integrazione dell'applicazione **Box** del portale di gestione di Azure fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo **Configura provisioning utenti**. <br> <br> ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769541.png "Abilita provisioning utenti automatico")

2. Nella finestra di dialogo **Abilita provisioning utenti in Box** fare clic su **Abilita provisioning utenti**. <br><br> ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769544.png "Abilita provisioning utenti automatico")

3. Nella pagina **Entrare per garantire l’accesso a Box** specificare le credenziali richieste e fare clic su **Autorizza**. <br><br> ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769546.png "Abilita provisioning utenti automatico")


4. Fare clic su **Garantire l’accesso a Box** per autorizzare l'operazione e tornare al portale di gestione di Azure. <br><br> ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769549.png "Abilita provisioning utenti automatico")

5. Per completare la configurazione fare clic sul pulsante Completa. <br><br> ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769551.png "Abilita provisioning utenti automatico")



##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Box, seguire questa procedura:

1. Nel portale di Azure AD creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Box** fare clic su **Assegna utenti**. <br><br> ![Assegna utenti](./media/active-directory-saas-box-tutorial/IC769552.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione. <br><br> ![Sì](./media/active-directory-saas-box-tutorial/IC767830.png "Sì")
  

È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Box.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning selezionando il Dashboard nella pagina di integrazione dell'applicazione Box del portale di gestione di Azure.

<br><br> ![Dashboard](./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato:

<br><br> ![Integration status](./media/active-directory-saas-box-tutorial/IC769555.png "Integration status")


Nel tenant Box gli utenti sincronizzati sono visualizzati nella sezione **Utenti gestiti** di **Admin Console**.

<br><br> ![Integration status](./media/active-directory-saas-box-tutorial/IC769556.png "Integration status")


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!---HONumber=Nov15_HO1-->