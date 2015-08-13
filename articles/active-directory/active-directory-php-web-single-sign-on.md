<properties 
	pageTitle="Single Sign-On con Azure Active Directory (PHP)" 
	description="Informazioni su come creare un'applicazione Web PHP che usa Single Sign-On con Azure Active Directory." 
	services="active-directory" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="tomfitz"/>

# Single Sign-On Web con PHP e Azure Active Directory

##<a name="introduction"></a>Introduzione

In questa esercitazione viene illustrato agli sviluppatori PHP come usare Azure Active Directory per abilitare Single Sign-On per gli utenti di Office 365. Si apprenderà come:

* Eseguire il provisioning dell'applicazione Web nel tenant del cliente
* Proteggere l'applicazione mediante WS-Federation

###Prerequisiti
Per questa procedura dettagliata sono necessari i prerequisiti seguenti nell'ambiente di sviluppo:

* [Codice di esempio PHP per Azure Active Directory]
* [Eclipse PDT 3.0.x All In Ones]
* PHP 5.3.1 tramite Installazione guidata piattaforma Web
* Internet Information Services (IIS) 7.5 con SSL abilitato
* Windows PowerShell
* [Cmdlet di Office 365 PowerShell]

## Passaggio 1: Creare un'applicazione PHP
In questo passaggio viene descritto come creare una semplice applicazione PHP che rappresenterà una risorsa protetta. L'accesso a questa risorsa verrà concesso tramite autenticazione federata gestita dal servizio token di sicurezza della società, descritto più avanti nell'esercitazione.

1. Aprire una nuova istanza di Eclipse.
2. Scegliere **New** dal menu **File** e quindi fare clic su **New PHP Project**. 
3. Nella finestra di dialogo **New PHP Project** assegnare al progetto il nome *phpSample*, quindi fare clic su **Finish**.
4. Nel menu **PHP Explorer** a sinistra fare clic con il pulsante destro del mouse su *phpProject*, scegliere **New**, quindi fare clic su **PHP File**.
5. Nella finestra di dialogo **New PHP File** assegnare al file il nome **index.php**, quindi fare clic su **Finish**.
6. Sostituire il markup generato con il codice seguente, quindi salvare il file **index.php**:

		<!DOCTYPE>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index</title>
		</head>
		<body>
			##Index Page
		</body>
		</html> 

7. Aprire **Gestione IIS** immettendo *inetmgr* al prompt Esegui e premendo INVIO.

8. In Gestione IIS espandere la cartella **Sites** nel menu di sinistra, fare clic con il pulsante destro del mouse su **Sito Web predefinito**, quindi scegliere **Aggiungi applicazione**.

9. Nella finestra di dialogo **Aggiungi applicazione** impostare il valore di **Alias** su *phpSample* e l'opzione **Percorso fisico** sul percorso del file in cui è stato creato il progetto PHP.

10. In Eclipse scegliere **Run** dal menu **Run**.

11. Nel menu **Run PHP Web Application** fare clic su **OK**.

12. Verrà aperta una pagina **index.php** in una nuova scheda di Eclipse. Nella pagina dovrebbe essere semplicemente visualizzato il testo: *Index Page*.

## Passaggio 2: Eseguire il provisioning dell'applicazione nel tenant della directory della società
In questo passaggio vengono descritti il provisioning dell'applicazione PHP nel tenant di un cliente e la configurazione di Single Sign-On. Queste attività devono essere eseguite da un amministratore di Azure Active Directory del cliente. Dopo avere completato questo passaggio, i dipendenti della società potranno eseguire l'autenticazione all'applicazione Web usando gli account Office 365.

Il processo di provisioning inizia con la creazione di una nuova entità servizio per l'applicazione. In Azure Active Directory le entità servizio vengono usate per registrare e autenticare le applicazioni per la directory.

1. Se necessario, scaricare e installare i cmdlet di Office 365 PowerShell.
2. Nel menu **Start** eseguire la console **Modulo di Microsoft Azure Active Directory per Windows PowerShell**. Questa console offre un ambiente da riga di comando per la configurazione di attributi relativi al tenant di Office 365, ad esempio la creazione e la modifica di entità servizio.
3. Per importare il modulo **MSOnlineExtended** richiesto, digitare il comando seguente e premere INVIO:

		Import-Module MSOnlineExtended -Force
4. Per connettersi alla directory di Office 365, è necessario fornire le credenziali di amministratore della società. Digitare il comando seguente e premere INVIO, quindi immettere le credenziali quando richiesto:

		Connect-MsolService
5. A questo punto è possibile creare una nuova entità servizio per l'applicazione. Digitare il comando seguente e premere INVIO:

		New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
In questo passaggio verranno visualizzate informazioni simili alle seguenti:

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample PHP Website
		ServicePrincipalNames : {phpSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
> [AZURE.NOTE]È consigliabile salvare questo output, in particolare la chiave simmetrica generata. Questa chiave viene visualizzata solo durante la creazione dell'entità servizio e non sarà recuperabile in seguito. Gli altri valori sono necessari per utilizzare l'API Graph per leggere e scrivere informazioni nella directory.

6. Con l'ultimo passaggio viene impostato l'URL di risposta per l'applicazione, ossia l'indirizzo a cui vengono inviate le risposte in seguito ai tentativi di autenticazione. Digitare i comandi seguenti e premere INVIO:

		$replyUrl = New-MsolServicePrincipalAddresses –Address "https://localhost/phpSample" 

		Set-MsolServicePrincipal –AppPrincipalId "7829c758-2bef-43df-a685-717089474505" –Addresses $replyUrl 
	
A questo punto, il provisioning dell'applicazione Web è stato completato nella directory ed è possibile usarla per consentire ai dipendenti della società l'accesso Single Sign-On al Web.

## Passaggio 3: Proteggere l'applicazione mediante WS-Federation per l'accesso dei dipendenti
In questo passaggio viene illustrato come aggiungere l'accesso federato usando Windows Identity Foundation (WIF) e le librerie simpleSAML.php scaricate come parte del pacchetto di codice di esempio nei prerequisiti. Verrà inoltre aggiunta una pagina di accesso e verranno configurati i criteri di attendibilità tra l'applicazione e il tenant della directory.

1. In Eclipse fare clic con il pulsante destro del mouse sul progetto **phpSample**, scegliere **New** e quindi fare clic su **File**. 

2. Nella finestra di dialogo **New File** assegnare al file il nome **federation.ini** e quindi fare clic su **Finish**.

3. Nel nuovo file **federation.ini** immettere le informazioni seguenti, specificando i valori con le informazioni salvate nel passaggio 2 durante la creazione dell'entità servizio:

		federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
		federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
		federation.trustedissuers.friendlyname=Fabrikam
		federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
		federation.realm=spn:7829c758-2bef-43df-a685-717089474505
		federation.reply=https://localhost/phpSample/index.php 


> [AZURE.NOTE]I valori **audienceuris** e **realm** devono essere preceduti da "spn:".

4. In Eclipse fare clic con il pulsante destro del mouse sul progetto **phpSample**, scegliere **New** e quindi fare clic su **PHP File**.

5. Nella finestra di dialogo **New PHP File** assegnare al file il nome **secureResource.php**, quindi fare clic su **Finish**.

6. Nel nuovo file **secureResource.php** immettere il codice seguente, sostituendo il percorso **c:\phpLibraries** con il percorso radice in cui è stato scaricato il codice di esempio. Il percorso radice dovrebbe includere il file **simpleSAML.php** e la cartella **federation**:

		<?php
		ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
		require_once ('federation/FederatedLoginManager.php');
		session_start();
		$token = $_POST['wresult'];
		$loginManager = new FederatedLoginManager();
		if (!$loginManager->isAuthenticated()) {
			if (isset ($token)) {
				try {
					$loginManager->authenticate($token);
				} catch (Exception $e) {
					print_r($e->getMessage());
				}  
			} else {
				$returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
				header('Pragma: no-cache');
				header('Cache-Control: no-cache, must-revalidate');
				header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
				exit();
			}
		}
		?> 

7. Aprire la pagina **index.php** e aggiornarne il contenuto per proteggerla, quindi salvarla:

		<?php
		require_once (dirname(__FILE__) . '/secureResource.php');
		?>
		<!DOCTYPE html>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index Page</title>
		</head>
		<body>
			<h2>Index Page</h2>
			<h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
			<h4>Claim list:</h4>
			<ul>
				<?php
				foreach ($loginManager->getClaims() as $claim) {
					print_r('<li>' . $claim->toString() . '</li>');
				}
				?>  
			</ul>
		</body>
		</html> 

8. Scegliere **Run** dal menu **Run**. Si verrà reindirizzati automaticamente alla pagina del provider di identità di Office 365, dove è possibile effettuare l'accesso usando le credenziali del tenant della directory. Ad esempio: *john.doe@fabrikam.onmicrosoft.com*.

## Riepilogo
In questa esercitazione è stato illustrato come creare e configurare un'applicazione PHP a singolo tenant che usa le funzionalità Single Sign-On di Azure Active Directory.

Un esempio che mostra l'uso di Azure Active Directory e Single Sign-On per i siti Web PHP è disponibile all'indirizzo <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP>.


[Step 1: Create a PHP Application]: #createapp
[Step 2: Provision the Application in a Company's Directory Tenant]: #provisionapp
[Step 3: Protect the Application Using WS-Federation for Employee Sign In]: #protectapp
[Summary]: #summary
[Introduction]: #introduction
[Developing Multi-Tenant Cloud Applications with Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/download/details.aspx?id=17331
[Cmdlet di Office 365 PowerShell]: http://msdn.microsoft.com/library/azure/jj151815.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/download/details.aspx?id=4211
[Eclipse PDT 3.0.x All In Ones]: http://www.eclipse.org/pdt/downloads/
[Codice di esempio PHP per Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP
 

<!----HONumber=July15_HO4-->