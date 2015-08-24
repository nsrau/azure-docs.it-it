<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Zoom | Microsoft Azure" description="Informazioni su come usare Zoom con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Zoom
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=528083).
  
Questa esercitazione descrive l'integrazione di Azure e Zoom. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Zoom
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Zoom potranno accedere all'applicazione tramite il sito aziendale di Zoom (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione Zoom
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-zoom-tutorial/IC784693.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione Zoom
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Zoom.

###Per abilitare l'integrazione dell'applicazione per Zoom, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-zoom-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione](./media/active-directory-saas-zoom-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-zoom-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Zoom**.

    ![Raccolta di applicazioni](./media/active-directory-saas-zoom-tutorial/IC784694.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Zoom** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Zoom](./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Zoom tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Per eseguire questa procedura, è necessario creare un certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Zoom** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zoom-tutorial/IC784696.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Zoom** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zoom-tutorial/IC784697.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso Zoom** della pagina **Configura URL app** digitare l'URL usando il modello seguente "**http://company.zoom.us*" e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-zoom-tutorial/IC784698.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Zoom** fare clic su **Download certificato** e quindi salvare il file di certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zoom-tutorial/IC784699.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Zoom come amministratore.

6.  Fare clic sulla scheda **Single Sign-On**.

    ![Single Sign-On](./media/active-directory-saas-zoom-tutorial/IC784700.png "Single Sign-On")

7.  Fare clic sulla scheda **Security Control** e quindi passare alle impostazioni **Single Sign-On**.

8.  Nella sezione Single Sign-On seguire questa procedura:

    ![Single Sign-On](./media/active-directory-saas-zoom-tutorial/IC784701.png "Single Sign-On")

    1.  Nella finestra di dialogo **Configura accesso Single Sign-On in Zoom** del portale di Azure copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **Sign-in page URL**.
    2.  Nella finestra di dialogo **Configura accesso Single Sign-Out in Zoom** del portale di Azure copiare il valore di **URL servizio Single Sign-Out** e quindi incollarlo nella casella di testo **Sign-in page URL**.
    3.  Creare un file **con codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    4.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Identity provider certificate**.
    5.  Nella finestra di dialogo **Configura accesso Single Sign-On in Zoom** del portale di Azure copiare il valore di **URL autorità di certificazione** e quindi incollarlo nella casella di testo **Issuer**.
    6.  Fare clic su **Save**.

9.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zoom-tutorial/IC784702.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Zoom, è necessario eseguirne il provisioning in Zoom. Nel caso di Zoom, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di **Zoom** come amministratore.

2.  Fare clic sulla scheda **Account Management** e quindi su **User Management**.

3.  Nella sezione User Management fare clic su **Add users**.

    ![User management](./media/active-directory-saas-zoom-tutorial/IC784703.png "User management")

4.  Nella pagina **Add users** seguire questa procedura:

    ![Add users](./media/active-directory-saas-zoom-tutorial/IC784704.png "Add users")

    1.  In **User Type** selezionare **Basic**.
    2.  Nella casella di testo **Emails** digitare l'indirizzo di posta elettronica di un account Azure AD di cui si vuole eseguire il provisioning.
    3.  Fare clic su **Add**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zoom per eseguire il provisioning degli account utente Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

###Per assegnare gli utenti a Zoom, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Zoom** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-zoom-tutorial/IC784705.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-zoom-tutorial/IC767830.png "Sì")
  
Se si desidera testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->