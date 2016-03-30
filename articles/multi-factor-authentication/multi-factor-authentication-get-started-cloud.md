<properties 
	pageTitle="Introduzione ad Azure Multi-Factor Authentication nel cloud" 
	description="Questa è la pagina su Multi-Factor Authentication di Azure in cui viene descritto come iniziare a utilizzare questa tipologia di autenticazione di Azure nel cloud." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Introduzione ad Azure Multi-Factor Authentication nel cloud



<center>![MFA nel cloud](./media/multi-factor-authentication-get-started-cloud/cloud2.png)</center>

Dopo aver determinato che viene usata la modalità Multi-Factor Authentication nel cloud, è possibile procedere. Per un'introduzione ad Azure Multi-Factor Authentication nel cloud bastano pochi clic. Si noti che se si utilizza la modalità Multi-Factor Authentication per Office 365 o per amministratori di Azure, è possibile procedere al passaggio 3.


1. [Iscriversi per ottenere una sottoscrizione ad Azure](https://azure.microsoft.com/pricing/free-trial/)
	- Se non si dispone già di una sottoscrizione ad Azure, è necessario iscriversi. Se si è appena iniziato a usare il servizio MFA di Azure, è possibile usare una sottoscrizione di valutazione
2. [Creare un provider di Multi-Factor Authentication](#creating-an-azure-multi-factor-auth-provider) o [assegnare licenze agli utenti](#assigning-an-azure-ad-premium-or-enterprise-mobility-license-to-users)
	- È quindi necessario eseguire una di queste operazioni. Creare un provider di Multi-Factor Authentication e assegnarlo alla directory o assegnare licenze agli utenti. Le licenze sono disponibili per gli utenti che hanno Azure MFA, Azure AD Premium e/o EMS. Azure Multi-Factor Authentication è incluso in Azure Active Directory Premium. È incluso anche in Enterprise Mobility Suite. Se è disponibile un numero di licenze sufficiente per tutti gli utenti che usano l'autenticazione a più fattori, non è necessario creare un provider di autenticazione. 
3. [Attivare la modalità Multi-Factor Authentication per gli utenti](#turn-on-multi-factor-authentication-for-users)
	- Attivare Azure MFA per gli utenti tramite il portale di Office 365 o di Azure. 
4. [Inviare messaggi di posta elettronica agli utenti finali per informarli sulla modalità MFA](#send-email-to-end-users)
	- Dopo avere attivato la modalità Multi-Factor Authentication nell'account di un utente, è consigliabile inviargli un messaggio di posta elettronica per informarlo che, all'accesso successivo, dovrà completare il procedura, secondo quanto anticipato nel messaggio di posta elettronica. 



## Creazione di un provider Multi-Factor Authentication di Azure
Multi-Factor Authentication è disponibile per impostazione predefinita per gli amministratori globali che si occupano di utenti di Azure Active Directory e Office 365. Se tuttavia si vogliono sfruttare le funzionalità avanzate, è necessario acquistare la versione completa di Azure MFA.

Un provider di Azure Multi-Factor Authentication viene usato per sfruttare le funzionalità fornite dalla versione completa di Azure MFA. È destinato agli utenti che non hanno licenze tramite Azure MFA, Azure AD Premium o EMS. Azure MFA, Azure AD Premium e EMS includono le versione completa di Azure MFA per impostazione predefinita. Se si hanno licenze disponibili, non è necessario avere un provider di Azure Multi-Factor Authentication. La procedura seguente illustra come creare un provider di Azure Multi-Factor Authentication.

### Per creare un provider Multi-Factor Authentication
--------------------------------------------------------------------------------

1. Accedere al Portale di Azure come amministratore.
2. A sinistra selezionare Active Directory.
3. Nella parte superiore della pagina Active Directory, selezionare i provider Multi-Factor Authentication. Nella parte inferiore fare clic su **Nuovo**.
4. In Servizi app selezionare Provider di Autenticazione a più fattori e quindi scegliere Creazione rapida.
5. Compilare i campi seguenti, quindi selezionare Crea.
	1. Nome: il nome del provider Multi-Factor Authentication.
	2. Modello di utilizzo: modello di utilizzo del provider Multi-Factor Authentication.
		- Per autenticazione: modello di acquisto in cui è previsto l'addebito in base al numero di autenticazioni. Usato, in genere, per scenari in cui viene usato Azure Multi-Factor Authentication in un'applicazione per il consumer.
		- Per utente abilitato: modello di acquisto in cui è previsto l'addebito in base al numero di utenti abilitati. Usato, in genere, per l'accesso dei dipendenti alle applicazioni come Office 365.
	2. Directory: tenant di Azure Active Directory a cui è associato il provider Multi-Factor Authentication. Tenere presente quanto segue:
		- Non è necessaria una directory di Azure AD per creare un provider Multi-Factor Authentication. Può essere lasciata vuota se si prevede di usare solo il server Multi-Factor Authentication o l'SDK.
		- È necessario associare il provider di Azure Multi-Factor Authentication a una directory di Azure AD per sfruttare le funzionalità avanzate.
		- Azure AD Connect, AAD Sync e DirSync sono necessari unicamente se si esegue la sincronizzazione dell'ambiente Active Directory locale con una directory di Azure AD. Se si usa solo una directory di Azure AD non sincronizzata, la sincronizzazione non è necessaria.
		



5. Facendo clic su Crea, verrà creato il provider di Multi-Factor Authentication e verrà visualizzato un messaggio di conferma della creazione del provider. Fare clic su Ok.

![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-cloud/provider.png)
	 
## Assegnazione di una licenza di Azure MFA, Azure AD Premium o Enterprise Mobility Suite agli utenti

Se sono state acquistate licenze di Azure MFA, Azure AD Premium o Enterprise Mobility Suite, non è necessario creare un provider Multi-Factor Authentication. È sufficiente assegnare le licenze agli utenti e quindi abilitarli al servizio MFA.

### Per assegnare una licenza di Azure MFA, Azure AD Premium o Enterprise Mobility Suite
--------------------------------------------------------------------------------

1. Accedere al portale di Azure come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella pagina Active Directory fare doppio clic sulla directory con gli utenti da abilitare.
4. Nella parte superiore della pagina della directory selezionare **Licenses**.
5. Nella pagina Licenze selezionare Azure Multi-Factor Authentication, Active Directory Premium o Enterprise Mobility Suite e quindi fare clic su Assegna. ![Assegnare licenze](./media/multi-factor-authentication-get-started-cloud/license2.png)
6. Nella finestra di dialogo visualizzata selezionare gli utenti o i gruppi a cui assegnare le licenze, quindi fare clic sull'icona con il segno di spunta per salvare le modifiche.






## Attivare la modalità Multi-Factor Authentication per gli utenti

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

Stato | Descrizione |App interessate non basate su browser| Note 
:-------------: | :-------------: |:-------------: |:-------------: |
Disabled | Lo stato predefinito per un nuovo utente non registrato alla modalità Multi-Factor Authentication.|No|L'utente attualmente non usa la modalità Multi-Factor Authentication.
Enabled |L'utente è stato registrato alla modalità Multi-Factor Authentication.|No. Continuano a funzionare fino al completamento della registrazione.|L'utente è abilitato ma non ha completato la procedura di registrazione. All'utente verrà chiesto di completare la procedura all'accesso successivo.
Enforced|L'utente è stato registrato e ha completato la procedura di registrazione per l'utilizzo della modalità Multi-Factor Authentication.|Sì. Non funzioneranno finché non si creano e si usano password per le app. | È possibile che l'utente abbia o non abbia completato la registrazione. Se la procedura di registrazione è stata completata, l'utente userà la modalità Multi-Factor Authentication. In caso contrario, all'utente verrà chiesto di completare la procedura all'accesso successivo.

Per abilitare MFA per gli utenti, seguire questa procedura.

### Per attivare la modalità Multi-Factor Authentication
--------------------------------------------------------------------------------
1.  Accedere al portale di gestione di Azure come amministratore.
2.  A sinistra fare clic su Active Directory.
3.  In Directory fare clic sulla directory relativa all'utente che si desidera attivare.
4.  Nella parte superiore fare clic su Utenti.
5.  Nella parte inferiore della pagina fare clic sull'opzione per gestire la modalità Multi-Factor Authentication.
6.  Trovare l'utente che si desidera abilitare alla modalità Multi-Factor Authentication Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Verificare che lo stato sia disabilitato e inserire un segno di spunta nella casella accanto al nome.
7.  A destra fare clic su Abilita. Fare clic sull'opzione per abilitare la modalità Multi-Factor Authentication.
8.  Dopo aver abilitato gli utenti, è consigliabile inviare una notifica, ad esempio con un messaggio di posta elettronica. È anche opportuno informarli su come possono usare le app non basate su browser per evitare di essere bloccati.

<center>![Abilitare gli utenti](./media/multi-factor-authentication-get-started-cloud/user.png)</center>

Per modificare lo stato tramite Windows PowerShell, è possibile seguire questa procedura. È possibile modificare `$st.State` in modo che corrisponda a uno degli stati indicati in precedenza.

		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta


## Inviare un messaggio di posta elettronica agli utenti finali

Dopo aver abilitato gli utenti, si consiglia di inviare agli utenti un messaggio di posta elettronica per invitarli a fornire le proprie informazioni di contatto. Ecco un modello di messaggio di posta elettronica che è possibile usare. Include un collegamento a un video che gli utenti possono guardare.

		Subject: ACTION REQUIRED: Your password for Outlook and other apps needs updated

		Body:

		For added security, we have enabled multi-factor authentication for your account. 

		Action Required: You will need to complete the enrollment steps below to make your account secure with multi-factor authentication.  

		What to expect once MFA is enabled:

		Multi-factor authentication requires a password that you know and a phone that you have in order to sign into browser applications and to access Office 365, Azure portals.

		For Office 365 non-browser applications such as outlook, lync, a mail client on your mobile device etc, a special password called an app password is required instead of your account password to sign in. App passwords are different than your account password, and are generated during the multi-factor authentication set up process. 

		Please follow these enrollment steps to avoid interruption of your Office 365 service:

			1.  Sign in to the Office 365 Portal at http://portal.microsoftonline.com.
			2.  Follow the instructions to set up your preferred multi-factor authentication method when signing into Office 365 using a web browser. 
			3.  Create one app password for each device.
			4.  Enter the same app password in all applicable apps on that device e.g. Outlook, Mail client, Lync, Word, Powerpoint, Excel, CRM etc. 
			5.  Update your Office client applications or other mobile applications to use an app password.

		You can visit http://aka.ms/mfasetup to create app passwords or change your MFA Setting.  Please bookmark this.

		NOTE: Before entering an app password, you will need to clear the sign-in information (delete sign-in info), restart the application,   and sign-in with the username and app password. Follow the steps documented : http://technet.microsoft.com/library/dn270518.aspx#apppassword.


		Watch a video showing these steps at http://g.microsoftonline.com/1AX00en/175.

		Best Regards,
		Your Administrator

## Passaggi successivi
Dopo avere configurato l'autenticazione a più fattori nel cloud, è possibile configurare e impostare la distribuzione. Vedere [Configurazione di Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md).

<!---HONumber=AcomDC_0323_2016-->