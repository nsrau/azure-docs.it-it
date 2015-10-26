<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Mimecast Admin Console | Microsoft Azure" description="Informazioni su come utilizzare Mimecast Admin Console con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Mimecast Admin Console
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=529833).
  
In questa esercitazione viene illustrata l'integrazione di Azure e Mimecast Admin Console. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Mimecast Admin Console abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Mimecast Admin Console saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Mimecast Admin Console (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Mimecast Admin Console
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Mimecast Admin Console
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Mimecast Admin Console.

###Ecco come abilitare l'integrazione dell'applicazione per Mimecast Admin Console:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Mimecast Admin Console**.

    ![Raccolta di applicazioni](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Mimecast Admin Console**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast Admin Console")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Mimecast Admin Console con il proprio account in Azure AD utilizzando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Mimecast Admin Console** nel portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Mimecast Admin Console** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a Mimecast Admin Console** digitare l’URL utilizzato dagli utenti per accedere all’applicazione Mimecast Admin Console (ad esempio, “https://webmail-uk.mimecast.com” o “https://webmail-us.mimecast.com”), quindi fare clic su **Avanti**.

    >[AZURE.NOTE]L’URL di accesso è specifico dell’area geografica.

    ![Configura URL app](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Mimecast Admin Console** per scaricare il certificato fare clic su **Download certificato**, quindi salvare il file di certificato localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere a Mimecast Admin Console come amministratore.

6.  Passare a **Services > Application**.

    ![Servizi](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Servizi")

7.  Fare clic su **Authentication Profiles**.

    ![Profili di autenticazione](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Profili di autenticazione")

8.  Fare clic su **New Authentication Profile**.

    ![Nuovi profili di autenticazione](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "Nuovi profili di autenticazione")

9.  Nella sezione **Authentication Profile**, eseguire la procedura seguente:

    ![Profilo di autenticazione](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Profilo di autenticazione")

    1.  Nella casella di testo **Description** digitare un nome per la configurazione.
    2.  Selezionare **Enforce SAML Authentication for Mimecast Admin Console**.
    3.  Come **Provider** selezionare **Azure Active Directory**.
    4.  Nella pagina **Configura accesso Single Sign-On in Mimecast Admin Console** del portale di Azure copiare il valore **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer URL**.
    5.  Nella pagina **Configura accesso Single Sign-On in Mimecast Admin Console** del portale di Azure copiare il valore **URL accesso remoto** e incollarlo nella casella di testo **Login URL**.
    6.  Nella pagina **Configura accesso Single Sign-On in Mimecast Admin Console** del portale di Azure copiare il valore **URL accesso remoto** e incollarlo nella casella di testo **Logout URL**.  

        >[AZURE.NOTE]Il valore di Login URL e il valore Logout URL per Mimecast Admin Console sono identici.

    7.  Creare un file **con codifica Base 64** dal certificato scaricato.

        >[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    8.  Aprire il certificato con codifica Base 64 nel Blocco note, rimuovere la prima riga (“*--*“) e l’ultima riga (“*--*“), copiare il contenuto rimanente negli Appunti e incollarlo nella casella di testo **Identity Provider Certificate (Metadata)**.
    9.  Selezionare **Allow Single Sign On**.
    10. Fare clic su **Save**.

10. Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la pagina **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Mimecast Admin Console, è necessario eseguirne il provisioning in Mimecast Admin Console. Nel caso di Mimecast Admin Console, il provisioning è un’attività manuale.
  
Per poter creare gli utenti è necessario registrare un dominio.

###Per configurare il provisioning utente, eseguire la procedura seguente:

1.  Accedere a **Mimecast Admin Console** come amministratore.

2.  Passare a **Directories > Internal**.

    ![Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Directory")

3.  Fare clic su **Register New Domain**.

    ![Registra nuovo dominio](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Registra nuovo dominio")

4.  Dopo aver creato il nuovo dominio, fare clic su **New Address**.

    ![Nuovo indirizzo](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "Nuovo indirizzo")

5.  Nella finestra di dialogo del nuovo indirizzo, seguire questa procedura:

    ![Salva](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Salva")

    1.  Nelle caselle di testo corrispondenti digitare gli attributi **indirizzo di posta elettronica**, **nome globale**, **password** e **conferma password** di un account AAD valido di cui si intende eseguire il provisioning.
    2.  Fare clic su **Save**.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mimecast Admin Console o le API fornite da Mimecast Admin Console per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Mimecast Admin Console, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Mimecast Admin Console** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->