<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Overdrive Books | Microsoft Azure" description="Informazioni su come utilizzare Overdrive Books con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Overdrive Books
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=527956).
  
In questa esercitazione viene illustrata l'integrazione di Azure e OverDrive Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Overdrive Books abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a OverDrive saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di OverDrive (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione OverDrive
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione OverDrive
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per OverDrive.

###Per abilitare l'integrazione dell'applicazione per OverDrive eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **OverDrive**.

    ![Raccolta di applicazioni](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **OverDrive**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a OverDrive tramite il proprio account in Azure AD utilizzando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **OverDrive** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Attiva Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Abilitare l'autenticazione Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a OverDrive** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a OverDrive** digitare l’URL utilizzando il seguente modello "**http://mslibrarytest.libraryreserve.com*", quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in OverDrive** scaricare il file di metadati e inviarlo al team di supporto di OverDrive.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configura accesso Single Sign-On")

    >[AZURE.NOTE]Il team di supporto di OverDrive configura l’accesso Single Sign-On per l’utente e invia una notifica al termine della configurazione.

5.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Non è richiesta alcuna operazione per configurare il provisioning degli utenti in OverDrive. Quando un utente assegnato tenta di accedere a OverDrive, se necessario viene creato automaticamente un account di OverDrive.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro strumento di creazione di account utente di OverDrive o le API fornite da OverDrive per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a OverDrive, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **OverDrive** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->