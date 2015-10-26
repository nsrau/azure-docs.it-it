<properties
   pageTitle="Esercitazione: Integrazione di Google Apps con Azure Active Directory | Microsoft Azure"
   description="Informazioni su come usare Google Apps con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora."
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/15/2015"
   ms.author="liviodlc"/>

#Esercitazione: Come integrare Google Apps ad Azure Active Directory

Questa esercitazione illustrerà come connettere l'ambiente di Google Apps ad Azure Active Directory (Azure AD). Si apprenderà come configurare l’accesso single sign-on in Google Apps, come abilitare il provisioning automatico dell’utente e come assegnare agli utenti l’accesso a Google Apps.

##Prerequisiti

1. Per accedere ad Azure Active Directory tramite il [portale di gestione di Azure](https://manage.windowsazure.com) è necessario innanzitutto disporre di una sottoscrizione Azure valida.

2. È necessario disporre di un tenant valido per [Google Apps per il lavoro](https://www.google.com/work/apps/) o [Google Apps per la didattica](https://www.google.com/edu/products/productivity-tools/). È possibile utilizzare un account di valutazione gratuita per uno di questi servizi.

##Passaggio 1: Aggiungere Google Apps a una Directory

1. Nel [portale di gestione di Azure](https://manage.windowsazure.com), nel pannello di navigazione sinistro, fare clic su **Active Directory**.

	![Selezionare Active Directory dal riquadro di spostamento a sinistra.][0]

2. Dall'elenco **Directory** selezionare la directory in cui si vuole aggiungere Google Apps.

3. Scegliere **Applicazioni** dal menu principale.

	![Fare clic su applicazioni.][1]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Fare clic su Aggiungi per aggiungere una nuova applicazione.][2]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**

	![Fare clic su Aggiungi un'applicazione dalla raccolta.][3]

6. Nella **casella di ricerca** digitare **Google Apps**. Selezionare quindi **Google Apps** dai risultati, e fare clic su **Completa** per aggiungere l'applicazione.

	![Aggiungere Google Apps.][4]

7. Ora dovrebbe essere visualizzata la pagina Quick Start per Google Apps:

	![Pagina di avvio rapido di Google Apps in Azure AD][5]

##Passaggio 2: Abilitare l'accesso Single Sign-On

1. Nella pagina di Avvio rapido per Google Apps in Azure AD fare clic sul pulsante **Configura accesso Single Sign-On**.

	![Pulsante Configura accesso Single Sign-On][6]

2. Verrà aperta una finestra di dialogo, in cui verrà visualizzata una schermata con messaggio analogo a "Stabilire come si desidera che gli utenti accedano a Google Apps". Selezionare **Single Sign-On di Azure AD**, quindi fare clic su **Avanti**.

	![Selezionare Single Sign-On di Microsoft Azure AD][7]

	> [AZURE.NOTE]Per altre informazioni sulle diverse opzioni dell'accesso Single Sign-On, [fare clic qui](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

3. Nella pagina **Configurare le impostazioni dell'app** immettere nel campo **URL di accesso** l'URL del tenant di Google Apps, usando uno dei formati seguenti: `https://mail.google.com/a/<yourdomain>`

	![Immettere l'URL del tenant][8]

4. Nella pagina **Configura accesso Single Sign-On in Google Apps** fare clic su **Download certificato** e infine salvare il certificato localmente nel computer.

	![Scaricare il certificato][9]

5. Aprire una nuova scheda nel browser e accedere a [Console di amministrazione di Google Apps](http://admin.google.com/) utilizzando l'account amministratore.

6. Fare clic su **Sicurezza**. Se non viene visualizzato il collegamento, può essere nascosto sotto il menù **Altri controlli** nella parte inferiore della schermata.

	![Fare clic su sicurezza.][10]

7. Nella pagina **Sicurezza** fare clic su **Configurare Single Sign-On (SSO).**

	![Fare clic su SSO.][11]

8. Eseguire le seguenti modifiche di configurazione:

	![Configurare SSL][12]

	- Selezionare **programma di installazione SSO con provider di identità di terze parti**.

	- In Azure AD copiare l’**URL di servizio di Single Sign-On** e incollarlo nel campo **URL della pagina d’accesso** in Google Apps.

	- In Azure AD copiare l’**URL di servizio di Single Sign-Out** e incollarlo nel campo **URL della pagina d’uscita** in Google Apps.

	- In Azure AD copiare l’**URL di Modifica password** e incollarlo nel campo **URL di Modifica password** in Google Apps.

	- In Google Apps per il **Certificato di verifica**, caricare il certificato che è stato scaricato nel passaggio #4.

	- Fare clic su **Save** per salvare le modifiche.

9. In Azure AD selezionare la casella di controllo di conferma della configurazione Single Sign-On per abilitare il certificato caricato in Google Apps. Quindi fare clic su **Next**.

	![Selezionare la casella di controllo di conferma][13]

10. Nella pagina finale della finestra di dialogo immettere un indirizzo di posta elettronica se si vogliono ricevere notifiche tramite posta elettronica relative a errori e avvisi correlati alla manutenzione di questa configurazione dell'accesso Single Sign-On.

	![Immettere l'indirizzo di posta elettronica][14]

11. Fare clic su **Completa** per chiudere la finestra di dialogo. Per testare la configurazione, vedere la sezione seguente intitolata [Assegnare utenti a Google Apps](#step-4-assign-users-to-google-apps).

##Passaggio 3: Abilitare il provisioning automatizzato degli utenti

> [AZURE.NOTE]Un'altra opzione valida per automatizzare il provisioning dell’utente in Google Apps consiste nell'utilizzare [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) che fornisce le identità di Active Directory locale in Google Apps. Al contrario, la soluzione in questa esercitazione esegue il provisioning per gli utenti e i gruppi abilitati alla posta elettronica in Google Apps Azure Active Directory (cloud).

1. Accedere alla [Console di amministrazione di Google Apps](http://admin.google.com/) con l'account amministratore e fare clic su **Sicurezza**. Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **Altri controlli** nella parte inferiore della schermata.

	![Fare clic su sicurezza.][10]

2. Nella pagina **Sicurezza** fare clic su **riferimento all'API**.

	![Fare clic su Informazioni di riferimento sulle API.][15]

3. Selezionare **Enable API access**.

	![Fare clic su Informazioni di riferimento sulle API.][16]

	> [AZURE.IMPORTANT]Per ogni utente per cui si intende eseguire il provisioning in Google Apps, si *deve* associare il loro nome utente in Azure Active Directory a un dominio personalizzato. Ad esempio, nomi utente simili a bob@contoso.onmicrosoft.com non verranno accettati da Google Apps, mentre bob@contoso.com verranno accettati. È possibile modificare il dominio di un utente esistente modificandone le proprietà in Azure AD. Le istruzioni su come impostare un dominio personalizzato per Azure Active Directory e Google Apps vengono illustrate qui di seguito.

4. Se ancora non è stato aggiunto un nome di dominio personalizzato per Azure Active Directory, attenersi alla procedura seguente:

	- Nel [portale di gestione di Azure](https://manage.windowsazure.com), nel pannello di navigazione sinistro, fare clic su **Active Directory**. Nell'elenco visualizzato, selezionare la directory. 

	- Fare clic su **Domini**dal menu principale in alto, quindi fare clic su **Aggiungere un dominio personalizzato**.

		![Aggiungere un dominio personalizzato][17]

	- Digitare il nome di dominio nel campo **Nome di dominio**. Questo deve essere lo stesso nome di dominio che si intende utilizzare per Google Apps. Quando si è pronti, fare clic sul pulsante **Aggiungi**.

		![Digitare il nome di dominio.][18]

	- Fare clic su **Avanti** per passare alla pagina di verifica. Per verificare che si è proprietari di tale dominio, è necessario modificare i record DNS del dominio in base ai valori forniti in questa pagina. È possibile verificare tramite **record MX** o **record TXT** a seconda di ciò che viene selezionato per l’opzione **Tipo di Record**. Per istruzioni più dettagliate su come verificare il nome di dominio con Windows Azure, vedere [Aggiungere il proprio nome di dominio ad Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

		![Verificare il nome di dominio][19]

	- Ripetere i passaggi precedenti per tutti i domini che si desidera aggiungere alla directory.

5. Ora che tutti i domini sono stati verificati con Azure AD, è necessario verificarli nuovamente con Google Apps. Per ogni dominio che non sia già registrato con Google Apps, procedere come segue:

	- Nella [Console di amministrazione di Google Apps](http://admin.google.com/), fare clic su **Domini**.

		![Fare clic su Domini][20]

	- Fare clic su **Aggiungere un dominio o un alias di dominio**.

		![Aggiungere un nuovo dominio][21]

	- Selezionare **Aggiungere un altro dominio**, e digitare il nome del dominio che si desidera aggiungere.

		![Digitare il nome di dominio][22]

	- Fare clic su **Continua e verifica le proprietà del dominio**. Seguire i passaggi per verificare che si è proprietari del nome di dominio. Per istruzioni dettagliate su come verificare il dominio con Google Apps, vedere [Verificare il proprietario del sito con Google Apps](https://support.google.com/webmasters/answer/35179).

	- Ripetere i passaggi precedenti per tutti i domini aggiuntivi che si desidera aggiungere a Google Apps.

	> [AZURE.WARNING]Se si modifica il dominio primario per il tenant di Google Apps, e se è già stato configurato single sign-on con Azure AD, sarà necessario ripetere il passaggio #3 in [Passaggio 2: abilitare il servizio Single Sign-On](#step-two-enable-single-sign-on).

6. Nella [Console di amministrazione di Google Apps](http://admin.google.com/) fare clic su **Ruoli amministrativi**.

	![Fare clic su Google Apps][26]

7. Determinare quale account di amministratore si desidera utilizzare per gestire il provisioning dell'utente. Per il **ruolo di amministratore** di tale account, modificare i **Privilegi** per tale ruolo. Assicurarsi che contenga tutti i **Privilegi dell’amministratore API** abilitato in modo che questo account possa essere utilizzato per il provisioning.

	![Fare clic su Google Apps][27]

	> [AZURE.NOTE]Se si sta configurando un ambiente di produzione, la procedura consigliata consiste nel creare un nuovo account di amministratore in Google Apps specificatamente per questo passaggio. Questi account devono disporre di un ruolo amministrativo associato che abbia privilegi API necessari.

8. In Azure Active Directory, fare clic su **Applicazioni** nel menu in alto e fare clic su **Google Apps**.

	![Fare clic su Google Apps][23]

9. Nella pagina di Avvio rapido per Google Apps, fare clic su **Configura provisioning utenti**.

	![Configurare il provisioning utente][24]

10. Nella finestra di dialogo visualizzata, fare clic su **Abilitare il provisioning utente** per l'autenticazione nell'Account dell’amministratore di Google Apps che si desidera utilizzare per gestire il provisioning.

	![Abilitare il provisioning][25]

11. Confermare che si desidera concedere l'autorizzazione Azure Active Directory per apportare modifiche al tenant di Google Apps.

	![Verificare le autorizzazioni.][28]

12. Fare clic su **Completa** per chiudere la finestra di dialogo.

##Passaggio 4: Assegnare utenti a Google Apps

1. Per testare la configurazione, creare prima di tutto un nuovo account di test nella directory.

2. Nella pagina di Avvio rapido di Google Apps fare clic sul pulsante **Assegna utenti**.

	![Fare clic su Assegna utenti][29]

3. Selezionare l'utente di test, quindi fare clic su **Assegna** nella parte inferiore della schermata:

 - Se il provisioning automatizzato degli utenti non è stato abilitato, verrà visualizzata la richiesta di conferma seguente:

		![Confirm the assignment.][30]

 - Se il provisioning automatizzato degli utenti è stato abilitato, verrà visualizzata una richiesta per definire il tipo di ruolo da assegnare all'utente in Google Apps. Gli utenti appena sottoposti a provisioning dovrebbero essere visualizzati nell'ambiente di Google Apps dopo alcuni minuti.

4. Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/), quindi accedere all'account di test e fare clic su **Google Apps**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png

<!---HONumber=Oct15_HO3-->