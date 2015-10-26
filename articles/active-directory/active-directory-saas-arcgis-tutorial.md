<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con ArcGIS | Microsoft Azure" description="Informazioni su come usare ArcGIS con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con ArcGIS
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=526915).

Questa esercitazione descrive l'integrazione di Azure e ArcGIS. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione ArcGIS abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad ArcGIS potranno accedere all'applicazione tramite il sito aziendale di ArcGIS (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per ArcGIS
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per ArcGIS

Questa sezione descrive come abilitare l'integrazione dell'applicazione per ArcGIS.

###Per abilitare l'integrazione dell'applicazione per ArcGIS, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **ArcGIS**.

    ![Raccolta di applicazioni](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **ArcGIS** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad ArcGIS tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **ArcGIS** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a ArcGIS** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso ArcGIS** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere usando il modello seguente "**https://company.maps.arcgis.com*", quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in ArcGIS** fare clic su **Scarica metadati** e quindi salvare il file di metadati in locale nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di ArcGIS come amministratore.

6.  Fare clic su **Edit Settings**.

    ![Edit Settings](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Edit Settings")

7.  Fare clic su **Security**.

    ![Sicurezza](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Sicurezza")

8.  In **Enterprise Logins** fare clic su **Set Identity Provider**.

    ![Enterprise Logins](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")

9.  Nella pagina di configurazione **Set Identity Provider** seguire questa procedura:

    ![Set Identity Provider](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")

    1.  Nella casella di testo Name digitare il nome della propria organizzazione.
    2.  Per **Metadata for the Enterprise Identity Provider will be supplied using**, selezionare **A File**.
    3.  Per caricare il file di metadati scaricato, fare clic su **Choose file**.
    4.  Fare clic su **Set Identity Provider**.

10. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad ArcGIS, è necessario eseguirne il provisioning in ArcGIS.  
Nel caso di ArcGIS, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Accedere al tenant **ArcGIS**.

2.  Fare clic su **Invite Members**.

    ![Invita membri](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")

3.  Selezionare **Aggiungi membri automaticamente senza inviare un'e-mail** e quindi fare clic su **Next**.

    ![Add Members Automatically](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")

4.  Nella finestra di dialogo **Members** seguire questa procedura:

    ![Add and review](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")

    1.  Nelle caselle di testo **First Name**, **Last Name** e **Email** digitare nome, cognome e indirizzo di posta elettronica di un account AAD valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Add And Review**.

5.  Verificare i dati immessi e quindi fare clic su **Add Members**.

    ![Add member](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ArcGIS per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti

Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti ad ArcGIS seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **ArcGIS** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->