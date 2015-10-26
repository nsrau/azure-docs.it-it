<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Work.com | Microsoft Azure" description="Informazioni su come usare Work.com con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Work.com
  
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=529836).
  
Questa esercitazione descrive l'integrazione di Azure e Work.com. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Work.com abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Work.com potranno accedere all'applicazione tramite il sito aziendale di Work.com (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione Work.com
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione Work.com
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Work.com.

###Per abilitare l'integrazione dell'applicazione per Work.com, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-work-com-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-work-com-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-work-com-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Work.com**.

    ![Raccolta di applicazioni](./media/active-directory-saas-work-com-tutorial/IC794106.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Work.com** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Work.com tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare un certificato in Work.com.com.

>[AZURE.NOTE]Per configurare l'accesso Single Sign-On, è necessario configurare un nome di dominio personalizzato Work.com. È necessario definire almeno un nome di dominio, testare il nome di dominio e distribuirlo in tutta l'organizzazione.

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Accedere al tenant di Work.com come amministratore.

2.  Passare a **Setup**.

    ![Setup](./media/active-directory-saas-work-com-tutorial/IC794108.png "Impostazione")

3.  Nella sezione **Administer** del pannello di navigazione a sinistra fare clic su **Domain Management** per espandere la sezione correlata e quindi su **My Domain** per aprire la pagina **My Domain**.

    ![My Domain](./media/active-directory-saas-work-com-tutorial/IC767825.png "My Domain")

4.  Per verificare che il dominio sia stato configurato correttamente, assicurarsi che sia stato eseguito il "**passaggio 4 relativo alla distribuzione agli utenti**" e quindi verificare le "**impostazioni personali del dominio**".

    ![Domain Deployed to User](./media/active-directory-saas-work-com-tutorial/IC784377.png "Domain Deployed to User")

5.  In una finestra diversa del Web browser accedere al portale di Azure.

6.  Nella pagina di integrazione dell'applicazione **Work.com ** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configura accesso Single Sign-On")

7.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Work.com** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configura accesso Single Sign-On")

8.  Nella casella di testo **URL di accesso Work.com** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Work.com (ad esempio, **http://company.my.salesforce.com*”)) e quindi fare clic su **Avanti**:

    ![Configura URL app](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configura URL app")

9.  Nella pagina **Configura accesso Single Sign-On in Work.com** per scaricare il file del certificato, fare clic su **Download certificato** e quindi salvarlo localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configura accesso Single Sign-On")

10. Accedere al tenant di Work.com.

11. Passare a **Setup**.

    ![Impostazione](./media/active-directory-saas-work-com-tutorial/IC794108.png "Impostazione")

12. Espandere il menu **Security Controls** e quindi fare clic su **Single Sign-On Settings**.

    ![Impostazioni Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794113.png "Single Sign-On Settings")

13. Nella finestra di dialogo **Single Sign-On Settings** seguire questa procedura:

    ![SAML Enabled](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML Enabled")

    1.  Selezionare **SAML Enabled**.
    2.  Fare clic su **New**.

14. Nella sezione **SAML Single Sign-On Setting** seguire questa procedura:

    ![SAML Single Sign-On Setting](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML Single Sign-On Setting")

    1.  Nella casella di testo **Name** digitare un nome per la configurazione.  

        >[AZURE.NOTE]Se si specifica un valore per **Name**, verrà popolata automaticamente la casella di testo **API Name**.

    2.  Nella finestra di dialogo **Configura accesso Single Sign-On in Work.com** del portale di Azure copiare il valore di **URL autorità di certificazione** e quindi incollarlo nella casella di testo **Issuer**.
    3.  Per caricare il certificato scaricato, fare clic su **Browse**.
    4.  Nella casella di testo **Entity Id** digitare ****https://salesforce-work.com**.
5.  In **SAML Identity Type** selezionare **Assertion contains the Federation ID from the User object**.
    6.  In **SAML Identity Location** selezionare **Identity is in the NameIdentifier element of the Subject statement**.
    7.  Nella finestra di dialogo **Configura accesso Single Sign-On in Work.com** del portale di Azure copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Identity Provider Login URL**.
    8.  Nella finestra di dialogo **Configura accesso Single Sign-On in Work.com** del portale di Azure copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Identity Provider Logout URL**.
    9.  In **Service Provider Initiated Request Binding** selezionare **HTTP Post**.
    10. Fare clic su **Save**.

15. Nel pannello di navigazione a sinistra del portale di Work.com fare clic su **Domain Management** per espandere la sezione correlata e quindi fare clic su **My Domain** per aprire la pagina **My Domain**.

    ![My Domain](./media/active-directory-saas-work-com-tutorial/IC794115.png "My Domain")

16. Nella sezione **Login Page Branding** della pagina **My Domain** fare clic su **Edit**.

    ![Login Page Branding](./media/active-directory-saas-work-com-tutorial/IC767826.png "Login Page Branding")

17. Nella sezione **Authentication Service** della pagina **Login Page Branding** viene visualizzato il nome delle **impostazioni SAML SSO**. Selezionarlo e quindi fare clic su **Save**.

    ![Login Page Branding](./media/active-directory-saas-work-com-tutorial/IC784366.png "Login Page Branding")

18. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configura accesso Single Sign-On")

##Configurazione del provisioning utente
  
Per consentire l'accesso agli utenti di Azure Active Directory, è necessario che eseguano il provisioning a Work.com. Nel caso di Work.com, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, seguire questa procedura:

1.  Accedere al sito aziendale di Work.com come amministratore.

2.  Passare a **Setup**.

    ![Impostazione](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

3.  Passare a **Manage Users > Users**.

    ![Manage Users](./media/active-directory-saas-work-com-tutorial/IC784369.png "Manage Users")

4.  Fare clic su **New User**.

    ![All Users](./media/active-directory-saas-work-com-tutorial/IC794117.png "All Users")

5.  Nella sezione User Edit seguire questa procedura:

    ![User Edit](./media/active-directory-saas-work-com-tutorial/IC794118.png "User Edit")

    1.  Nelle apposite caselle di testo immettere gli attributi **Last Name**, **Alias**, **Email**, **Username** e **Nickname** di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
    2.  Selezionare **Role**, **User License** e **Profile**.
    3.  Fare clic su **Save**.  

        >[AZURE.NOTE]Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Work.com per eseguire il provisioning degli account utente Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

###Per assegnare gli utenti a Work.com, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione Work.com fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-work-com-tutorial/IC794119.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-work-com-tutorial/IC767830.png "Sì")
  
È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Work.com.
  
Se si desidera testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->