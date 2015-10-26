<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Directions on Microsoft | Microsoft Azure" description="Informazioni su come usare Directions on Microsoft con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Directions on Microsoft
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=533728).

Questa esercitazione descrive l'integrazione di Azure Active Directory e Directions on Microsoft. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Directions on Microsoft

Se ancora non si ha una sottoscrizione federata di Directions on Microsoft, inviare una richiesta all'indirizzo "*service@DirectionsOnMicrosoft.com*”.

Al termine dell'esercitazione, gli utenti di Azure Active Directory assegnati a Directions on Microsoft potranno eseguire l'accesso Single Sign-On all'applicazione usando Single Sign-On.

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Directions on Microsoft
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Directions on Microsoft

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Directions on Microsoft.

###Per abilitare l'integrazione dell'applicazione per Directions on Microsoft, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Directions on Microsoft**.

    ![Raccolta di applicazioni](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Directions on Microsoft** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Scenario](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scenario")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Directions on Microsoft tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Directions on Microsoft** nel portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Abilitazione dell'accesso Single Sign-On](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Abilitazione dell'accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Directions on Microsoft** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Single Sign-On di Microsoft Azure AD")

3.  Nella pagina **Configura URL app** digitare ****https://www.directionsonmicrosoft.com/user/login** nella casella di testo URL di accesso e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Directions on Microsoft** fare clic su **Download metadati** e quindi salvare il file di metadati localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati al team di supporto di Directions on Microsoft (**service@DirectionsOnMicrosoft.com*). Per consentire al team di supporto di Directions on Microsoft di trovare l'appartenenza al sito federato, includere nel messaggio di posta elettronica le informazioni relative alla società.

    >[AZURE.NOTE]L'accesso Single Sign-On per Directions on Microsoft deve essere abilitato dal team di supporto di Directions on Microsoft. Quando l'accesso Single Sign-On verrà abilitato, si riceverà una notifica.

6.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Directions on Microsoft. Quando un utente assegnato prova ad accedere a Directions on Microsoft usando il pannello di accesso, Directions on Microsoft verifica se l'utente esiste. Se l'account utente non è presente, Directions on Microsoft lo crea automaticamente.
##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Directions on Microsoft seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Directions on Microsoft** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Sì")

<!---HONumber=Oct15_HO3-->