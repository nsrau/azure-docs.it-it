<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Dropbox for Business | Microsoft Azure" 
    description="Informazioni su come usare Dropbox for Business con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Dropbox for Business
  
Questa esercitazione descrive l'integrazione di Azure e Dropbox for Business.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di test in Dropbox for Business
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Dropbox for Business potranno accedere all'applicazione tramite il sito aziendale di Dropbox for Business (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Dropbox for Business
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario")



##Abilitazione dell'integrazione dell'applicazione per Dropbox for Business
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Dropbox for Business.

###Per abilitare l'integrazione dell'applicazione per Dropbox for Business, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Dropbox for Business**.

    ![Raccolta di applicazioni](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Dropbox for Business** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Dropbox for Business tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 nel tenant di Dropbox for Business. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Dropbox for Business** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Dropbox for Business** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configure App URL** seguire questa procedura:

     3.1. Accedere al tenant Dropbox for Business. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configura accesso Single Sign-On")

     3.2. Nel riquadro di spostamento a sinistra fare clic su **Admin Console**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configura accesso Single Sign-On")

     3.3. In **Admin Console** fare clic su **Authentication** nel riquadro di spostamento a sinistra. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configura accesso Single Sign-On")

     3.4. Nella sezione **Single sign-on** selezionare **Enable single sign-on** e quindi fare clic su **More** per espandere la sezione. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configura accesso Single Sign-On")

     3.5. Copiare l'URL accanto a **Users can sign in by entering their email address or they can go directly to**. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configura accesso Single Sign-On")

     3.6. Nella casella di testo dell'URL **DropBox for business sign in** nel portale di Azure incollare l'URL copiato in precedenza. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configura accesso Single Sign-On")



4. Nella pagina **Configura accesso Single Sign-On in Dropbox for Business** fare clic su **Scarica certificato** e quindi salvare il file di certificato nel computer. <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configura accesso Single Sign-On")


5. Nella sezione **Single sign-on** della pagina **Authentication** del tenant Dropbox for Business, seguire questa procedura: <br><br> ![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configura accesso Single Sign-On")

     5.1. Fare clic su **Required**.

     5.2. Nella finestra di dialogo **Configura accesso Single Sign-On in Dropbox for Business** del portale di Azure copiare il valore di **URL pagina di accesso** e quindi incollarlo nella casella di testo **Sign in URL**.


     5.3. Creare un file con **codifica Base 64** dal certificato scaricato.  

     >[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).


     5.4. Fare clic su **Choose certificate** e quindi selezionare il **file di certificato con codifica Base 64**.


     5.5. Fare clic su **Save changes** per completare la configurazione del tenant DropBox for Business.


6. Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**. <br><br>![Configura accesso Single Sign-On](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configura accesso Single Sign-On")





##Configurazione del provisioning utente
  
Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in Dropbox for Business.


### Per configurare il provisioning utente, eseguire la procedura seguente:

1. Nella pagina di integrazione dell'applicazione **Dropbox for Business** del portale di gestione di Azure fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo **Configura provisioning utenti**.

2. Nella pagina Abilita provisioning utenti in DropBox for Business fare clic su Abilita provisioning utenti per aprire la finestra di dialogo di accesso a Dropbox per il collegamento ad Azure AD. <br><br> ![Provisioning utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Provisioning utenti")

3. Nella finestra di dialogo di **accesso a Dropbox per il collegamento ad Azure AD** accedere al tenant Dropbox for Business. <br><br> ![Provisioning utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Provisioning utenti")



4. Fare clic su **Allow** per concedere ad Azure AD l'accesso a Dropbox. <br><br> ![Provisioning utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Provisioning utenti")



5. Per completare la configurazione fare clic sul pulsante **Completa**. <br><br> ![Provisioning utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Provisioning utenti")




##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Dropbox for Business, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Dropbox for Business** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Sì")
  


È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Dropbox for Business.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning selezionando il **Dashboard** nella pagina di integrazione dell'applicazione **Dropbox for Business** del portale di gestione di Azure.

<br><br> ![Assegna utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assegna utenti")


Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato.

<br><br> ![Assegna utenti](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assegna utenti")


Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso.
Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0121_2016-->
