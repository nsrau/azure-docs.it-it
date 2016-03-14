<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Citrix GoToMeeting | Microsoft Azure" 
    description="Informazioni su come usare Citrix GoToMeeting con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
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
    ms.date="02/29/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Citrix GoToMeeting  
Si applica a: Azure

>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=522412).

Questa esercitazione descrive l'integrazione di Azure e Citrix GoToMeeting. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Citrix GoToMeeting

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Citrix GoToMeeting
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Configurazione](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configurazione")
##Abilitazione dell'integrazione dell'applicazione per Citrix GoToMeeting

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Citrix GoToMeeting.

###Per abilitare l'integrazione dell'applicazione per Citrix GoToMeeting, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Citrix GoToMeeting**.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  Nel riquadro dei risultati selezionare **Citrix GoToMeeting** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Citrix GoToMeeting tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 nel tenant di Citrix GoToMeeting. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Citrix GoToMeeting** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **CONFIGURA ACCESSO SINGLE SIGN-ON**.

    ![Abilitare l'autenticazione Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Abilitare l'autenticazione Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Citrix GoToMeeting** selezionare **Single Sign-On di Microsoft Azure AD**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configura accesso Single Sign-On")


3. Nella pagina **Configurare le impostazioni dell'app** fare clic su **Avanti**. <br><br>![Abilitare l'autenticazione Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Abilitare l'autenticazione Single Sign-On")

4.  Nella pagina **Configura accesso Single Sign-On in Citrix GoToMeeting** fare clic su **Download certificato** e quindi salvare il file di certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del browser accedere alla propria area del sito Citrix Organization Center ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Fare clic sulla scheda **Identity Provider** e seguire questa procedura: <br><br> ![SAML setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")

     6\.1 Selezionare **Manual**

     6\.2. Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix GoToMeeting** del portale di Azure classico copiare il valore di **URL pagina di accesso** e quindi incollarlo nella casella di testo **Sign-in page URL**.

     6\.3. Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix GoToMeeting** del portale di Azure classico copiare il valore di **URL pagina di disconnessione** e quindi incollarlo nella casella di testo **Sign-out page URL**.

     6\.4. Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix GoToMeeting** del portale di Azure classico copiare il valore di **ID entità** e incollarlo nella casella di testo **Identity provider Entity ID**.

     6\.5. Per caricare il certificato scaricato, fare clic su **Upload Certificate**.

     6\.6. Fare clic su **Save**.

6.  Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configura accesso Single Sign-On")


7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. <br><br> ![SAML setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML setup")





##Configurazione del provisioning utente

Questa sezione descrive come abilitare il provisioning degli account utente di Azure Active Directory in Citrix GoToMeeting.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Citrix GoToMeeting** del portale di Azure classico fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo **Configura provisioning utenti**.

    ![Configurare il provisioning utente.](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configurare il provisioning utente.")

2.  Nella pagina **Impostazioni e credenziali amministratore** seguire questa procedura:

    ![Configurare il provisioning utente.](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configurare il provisioning utente.")

     2\.1. Nella casella di testo **Nome utente amministratore Citrix GoToMeeting** digitare il nome utente di un amministratore.

     2\.2. Nella casella di testo **Password amministratore Citrix GoToMeeting** digitare la password dell'amministratore.

     2\.3. Fare clic su **Avanti**.

3.  Nella pagina **Conferma** fare clic sul segno di spunta per salvare la configurazione.

4.  Fare clic sul pulsante **Convalida**, per verificare la configurazione.
##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Citrix GoToMeeting, seguire questa procedura:

1.  Nel portale di Azure classico creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Citrix GoToMeeting** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Sì")

È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Dropbox for Business.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning selezionando il Dashboard in D nella pagina di integrazione dell'applicazione **Citrix GoToMeeting** del portale di Azure classico.

![Dashboard](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato:

![Integration status](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration status")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso.

Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=AcomDC_0302_2016-->