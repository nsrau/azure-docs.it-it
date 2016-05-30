<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Salesforce | Microsoft Azure"
    description="Informazioni su come usare Salesforce con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#Esercitazione: Come eseguire l'integrazione di Salesforce con Azure Active Directory

Questa esercitazione illustrerà come connettere l'ambiente di Salesforce ad Azure Active Directory. Verrà illustrato come configurare l'accesso Single Sign-On per Salesforce, come abilitare il provisioning automatizzato degli utenti e come assegnare gli utenti in modo che possano accedere a Salesforce.

##Prerequisiti

1. Per accedere ad Azure Active Directory tramite il [portale di Azure classico ](https://manage.windowsazure.com), prima di tutto è necessario avere una sottoscrizione di Azure valida.

2. È necessario avere un tenant valido in [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Se si usa un account **di valutazione** di Salesforce.com, non sarà possibile configurare il provisioning automatizzato degli utenti. Gli account di valutazione non hanno l'accesso API necessario, che viene abilitato solo dopo l'acquisto.
> 
> Per aggirare questa limitazione, è possibile usare un [account gratuito per sviluppatori](https://developer.salesforce.com/signup) per completare l'esercitazione.

Se si usa un ambiente Salesforce Sandbox, vedere l'[Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

##Esercitazioni video

È possibile seguire questa esercitazione tramite i video seguenti.

**Esercitazione video - Parte 1: Come abilitare l'accesso Single Sign-On**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Esercitazione video - Parte 2: Come automatizzare il provisioning degli utenti**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##Passaggio 1: Aggiungere Salesforce alla directory

1. Nel [portale di Azure classico](https://manage.windowsazure.com) fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Selezionare Active Directory dal riquadro di spostamento a sinistra.][0]

2. Dall'elenco **Directory** selezionare la directory in cui si vuole aggiungere Salesforce.

3. Scegliere **Applicazioni** dal menu principale.

	![Fare clic su applicazioni.][1]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Fare clic su Aggiungi per aggiungere una nuova applicazione.][2]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Fare clic su Aggiungi un'applicazione dalla raccolta.][3]

6. Nella **casella di ricerca** digitare **Salesforce**. Selezionare quindi **Salesforce** dai risultati, quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Aggiungere Salesforce.][4]

7. Dovrebbe essere visualizzata la pagina Avvio rapido per Salesforce:

	![Pagina di Avvio rapido di Salesforce in Azure AD][5]

##Passaggio 2: Abilitare l'accesso Single Sign-On

1. Prima di potere configurare l'accesso Single Sign-On, è necessario configurare e distribuire un dominio personalizzato per l'ambiente di Salesforce. Per istruzioni su a questa procedura, vedere la pagina relativa alla [configurazione di un nome di dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Nella pagina Avvio rapido di Salesforce in Azure AD fare clic sul pulsante **Configura accesso Single Sign-On**.

	![Pulsante Configura accesso Single Sign-On][6]

3. Verrà aperta una finestra di dialogo, in cui verrà visualizzata una schermata con messaggio analogo a "Stabilire come si desidera che gli utenti accedano a Salesforce". Selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

	![Selezionare Single Sign-On di Microsoft Azure AD.][7]

	> [AZURE.NOTE] Per altre informazioni sulle diverse opzioni dell'accesso Single Sign-On, [fare clic qui](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

4. Nella pagina **Configurare le impostazioni applicazione** specificare l'**URL di accesso**, immettendo l'URL del dominio di Salesforce nel formato seguente:
 - Account aziendale: `https://<domain>.my.salesforce.com`
 - Account sviluppatore: `https://<domain>-dev-ed.my.salesforce.com` 

	![Immettere l'URL di accesso.][8]

5. Nella pagina **Configura accesso Single Sign-On in Salesforce** fare clic su **Download certificato** e infine salvare il certificato localmente nel computer.

	![Download certificato][9]

6. Aprire una nuova scheda del browser e accedere all'account di amministratore di Salesforce.

7. Nel pannello di navigazione **Administrator** fare clic su **Security Controls** per espandere la sezione correlata. Fare quindi clic su **Single Sign-On Settings**.

	![Fare clic su Single Sign-On Settings in Security Controls.][10]

8. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

	![Fare clic su Edit.][11]

	> [AZURE.NOTE] Se non si è in grado di abilitare le impostazioni dell'accesso Single Sign-On per l'account Salesforce, potrebbe essere necessario contattare il supporto tecnico di Salesforce e richiedere l'abilitazione di questa funzionalità.

9. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

	![Selezionare SAML Enabled.][12]

10. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New**.

	![Selezionare SAML Enabled.][13]

11. Nella pagina **SAML Single Sign-On Setting Edit** effettuare le configurazioni seguenti:

	![Schermata delle configurazioni da apportare.][14]

 - Nel campo **Name** specificare un nome descrittivo per la configurazione. Se si specifica un valore per **Name**, verrà popolata automaticamente la casella di testo **API Name**.

 - In Azure AD copiare il valore **URL autorità di certificazione** e quindi incollarlo nel campo **Issuer** in Salesforce.

 - Nella casella di testo **Entity Id** immettere il nome di dominio di Salesforce nel formato seguente:
     - Account aziendale: `https://<domain>.my.salesforce.com`
     - Account sviluppatore: `https://<domain>-dev-ed.my.salesforce.com`

 - Fare clic su **Browse** o **Choose File** per aprire la finestra di dialogo **Choose File to Upload**, selezionare il certificato Salesforce, quindi fare clic su **Open** per caricarlo.

 - In **SAML Identity Type** selezionare **Assertion contains User's salesforce.com username**.

 - In **SAML Identity Location** selezionare **Identity is in the NameIdentifier element of the Subject statement**.

 - In Azure AD copiare il valore di **URL accesso remoto**, quindi incollarlo nel campo **Identity Provider Login URL** in Salesforce.

 - Per **Service Provider Initiated Request Binding**, selezionare **HTTP Redirect**.

 - Fare infine clic su **Save** per applicare le impostazioni di SAML Single Sign-On.

12. Nel pannello di navigazione di sinistra in Salesforce fare clic su **Domain Management** per espandere la sezione correlata, quindi fare clic su **My Domain**.

	![Fare clic su My Domain.][15]

13. Scorrere verso il basso fino alla sezione **Authentication Configuration**, quindi fare clic su **Edit**.

	![Fare clic su Edit.][16]

14. Nella sezione **Authentication Service** selezionare il nome descrittivo della configurazione SAML Single Sign-On, quindi fare clic su **Save**.

	![Selezionare la configurazione SSO.][17]

	> [AZURE.NOTE] Se vengono selezionati più servizi di autenticazione, quando un utente tenta di avviare l'accesso Single Sign-On all'ambiente di Salesforce, verrà richiesta la selezione del servizio di autenticazione da usare per l'accesso. Se non si vuole visualizzare la richiesta, è consigliabile **lasciare deselezionati tutti gli altri servizi di autenticazione**.

15. In Azure AD selezionare la casella di controllo di conferma della configurazione Single Sign-On per abilitare il certificato caricato in Salesforce. Quindi fare clic su **Avanti**.

	![Selezionare la casella di controllo di conferma.][18]

16. Nella pagina finale della finestra di dialogo immettere un indirizzo di posta elettronica se si vogliono ricevere notifiche tramite posta elettronica relative a errori e avvisi correlati alla manutenzione di questa configurazione dell'accesso Single Sign-On.

	![Immettere l'indirizzo di posta elettronica.][19]

17. Fare clic su **Completa** per chiudere la finestra di dialogo. Per testare la configurazione, vedere la sezione seguente intitolata [Assegnare utenti a Salesforce](#step-4-assign-users-to-salesforce).

##Passaggio 3: Abilitare il provisioning automatizzato degli utenti

1. Nella pagina di Avvio rapido di Azure AD per Salesforce fare clic sul pulsante **Configura provisioning utenti**.

	![Fare clic sul pulsante Configura provisioning utenti.][20]

2. Nella finestra di dialogo **Configura provisioning utenti** immettere il nome utente e la password dell'amministratore di Salesforce.

	![Immettere il nome utente o la password dell'amministratore.][21]

	> [AZURE.NOTE] Se si sta configurando un ambiente di produzione, è consigliabile creare un nuovo account di amministratore in Salesforce specifico per questo passaggio. A questo account deve essere assegnato il profilo **System Administrator** in Salesforce.

3. Per ottenere il token di sicurezza di Salesforce, aprire una nuova scheda e accedere allo stesso account di amministratore di Salesforce. Nell'angolo superiore destro della pagina fare clic sul proprio nome e quindi su **My Settings**.

	![Fare clic sul nome e quindi su My Settings.][22]

4. Nel pannello di navigazione sinistro fare clic su **Personal** per espandere la sezione corrispondente, quindi fare clic su **Reset My Security Token**.

	![Fare clic sul nome e quindi su My Settings.][23]

5. Nella pagina **Reset My Security Token** fare clic sul pulsante **Reset Security Token**.

	![Leggere gli avvisi.][24]

6. Controllare la casella di posta elettronica associata a questo account di amministratore. Cercare un messaggio di posta elettronica da Salesforce.com contenente il nuovo token di sicurezza.

7. Copiare il token, passare alla finestra di Azure AD e incollarlo nel campo **Token di sicurezza utente**. Quindi fare clic su **Avanti**.

	![Incollare il token di sicurezza.][25]

8. Nella pagina di conferma è possibile scegliere di ricevere notifiche tramite posta elettronica in caso di errori di provisioning. Fare clic su **Completa** per chiudere la finestra di dialogo.

	![Immettere l'indirizzo di posta elettronica per ricevere notifiche.][26]

##Passaggio 4: Assegnare utenti a Salesforce

1. Per testare la configurazione, creare prima di tutto un nuovo account di test nella directory.

2. Nella pagina di Avvio rapido di Salesforce fare clic sul pulsante **Assegna utenti**.

	![Fare clic su Assegna utenti.][27]

3. Selezionare l'utente di test e quindi fare clic su **Assegna** nella parte inferiore della schermata:

 - Se il provisioning automatizzato degli utenti non è stato abilitato, verrà visualizzata la richiesta di conferma seguente:

		![Confirm the assignment.][28]

 - Se il provisioning automatizzato degli utenti è stato abilitato, verrà visualizzata una richiesta per definire il tipo di profilo di Salesforce da assegnare all'utente. Gli utenti appena sottoposti a provisioning dovrebbero essere visualizzati nell'ambiente di Salesforce dopo alcuni minuti.

		![Confirm the assignment.][29]

		> [AZURE.IMPORTANT] If you are provisioning to a Salesforce **developer** environment, you will have a very limited number of licenses available for each profile. Therefore, it's best to provision users to the **Chatter Free User** profile, which has 4,999 licenses available.

4. Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/), quindi accedere all'account di test e fare clic su **Salesforce**.

##Articoli correlati

- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
- [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png

<!---HONumber=AcomDC_0518_2016-->