<properties 
	pageTitle="Creare ruoli Web e di lavoro" 
	description="Guida alla creazione di ruoli Web e di lavoro PHP in un servizio cloud di Azure e configurazione del runtime PHP." 
	services="" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="2/5/2015" 
	ms.author="tomfitz"/>

#Come creare ruoli Web e di lavoro PHP

## Panoramica

In questa guida verrà descritto come creare ruoli Web o di lavoro PHP in un ambiente di sviluppo Windows, scegliere una versione specifica di PHP tra le versioni incorporate disponibili, modificare la configurazione di PHP, abilitare le estensioni e, infine, come eseguire la distribuzione in Azure. Verrà inoltre descritto come configurare un ruolo Web o di lavoro per l'uso del runtime PHP (con configurazione ed estensioni personalizzate) fornito dall'utente.


##<a name="WhatIs"></a>Cosa sono i ruoli Web e di lavoro PHP
Azure offre tre modelli di calcolo per le applicazioni in esecuzione: [Siti Web di Azure][execution model-web sites], [Macchine virtuali di Azure][execution model-vms] e [Servizi cloud di Azure][execution model-cloud services].  Tutti e tre i modelli supportano PHP. Servizi cloud, che include ruoli Web e di lavoro, fornisce la tecnologia *PaaS (Platform as a Service)*. Nell'ambito di un servizio cloud, un ruolo Web fornisce un server Web IIS (Internet Information Services) dedicato su cui ospitare applicazioni Web front-end, mentre un ruolo di lavoro consente di eseguire attività asincrone, a esecuzione prolungata o perpetue, indipendenti dall'interazione o dall'input degli utenti.

Per altre informazioni, vedere la pagina relativa alla [definizione di servizio cloud].

##<a name="DownloadSdk"></a>caricare Azure SDK per PHP

[Azure SDK per PHP] è composto da diversi componenti. In questo articolo ne verranno usati due: Azure PowerShell e gli emulatori di Azure. È possibile installare questi due componenti tramite Installazione guidata piattaforma Web Microsoft disponibile nella pagina di: [installazione di Azure PowerShell e degli emulatori di Azure][install ps and emulators].

##<a name="CreateProject"></a>Procedura: Creare un progetto di servizi cloud

Il primo passaggio nella creazione di un ruolo Web o di lavoro PHP consiste nel creare un progetto di servizio Azure, che funge da contenitore logico per i ruoli Web e di lavoro e contiene la [definizione del servizio (.csdef)] del progetto e i file di [configurazione del servizio(.cscfg)].

Per creare un nuovo progetto di servizio Azure, eseguire Azure PowerShell come amministratore e avviare il seguente comando:

	PS C:>New-AzureServiceProject myProject

Questo comando permette di creare una nuova directory (`myProject`) a cui è possibile aggiungere ruoli Web e di lavoro.

##<a name="AddRole"></a>Procedura: Aggiungere ruoli Web o di lavoro PHP

Per aggiungere un ruolo Web PHP a un progetto, eseguire il comando seguente dalla directory radice del progetto:

	PS C:\myProject> Add-AzurePHPWebRole roleName

Per un ruolo di lavoro, usare il comando seguente:

	PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE]Il parametro `roleName` è facoltativo. se omesso, il nome del ruolo verrà generato automaticamente. Il primo ruolo Web creato sarà `WebRole1`, il secondo sarà `WebRole2` e così via. Il primo ruolo di lavoro creato sarà `WorkerRole1`, il secondo sarà `WorkerRole2` e così via.

##<a name="SpecifyPHPVersion"></a>Procedura: Specificare la versione PHP incorporata

Quando si aggiunge un ruolo Web o di lavoro PHO a un progetto, i file di configurazione del progetto verranno modificati in modo che PHP venga installato su ciascuna istanza Web o di lavoro dell'applicazione alla sua distribuzione. Per visualizzare la versione di PHP che verrà installata per impostazione predefinite, eseguire il comando seguente:

	PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Il risultato del comando sopra riportato sarà simile al seguente. In questo esempio, il flag `IsDefault` è impostato su `true` per PHP 5.3.17, a indicare che questa sarà la versione di PHP installata per impostazione predefinita.

	Runtime Version		PackageUri						IsDefault
	------- ------- 	----------  					---------
   	Node 0.6.17      	http://nodertncu.blob.core...   False
   	Node 0.6.20         http://nodertncu.blob.core...   True
   	Node 0.8.4          http://nodertncu.blob.core...   False
	IISNode 0.1.21      http://nodertncu.blob.core...   True
  	Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

È possibile impostare la versione del runtime PHP su una qualsiasi delle versioni PHP elencate. Ad esempio, per impostare la versione PHP (per un ruolo denominato `roleName`) su 5.4.0 usare il comando seguente:

	PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE]È possibile che in futuro siano disponibili altre versioni PHP e che le versioni già disponibili subiscano delle modifiche.

##<a name="CustomizePHP"></a>Procedura: Personalizzare il runtime PHP incorporato

L'utente dispone del controllo completo sulla configurazione del runtime PHP che viene installato eseguendo la procedura sopra illustrata, incluse le modifiche delle impostazioni `php.ini` e l'abilitazione delle estensioni.

Per personalizzare il runtime PHP incorporato, eseguire la procedura seguente:

1. Aggiungere una nuova cartella denominata `php` alla directory `bin` del ruolo Web in uso. Per un ruolo di lavoro, aggiungerlo alla directory radice del ruolo.
2. Nella cartella `php` creare un'altra cartella denominata `ext`. Inserire tutti i file con estensione `.dll` (ad esempio `php_mongo.dll`) da abilitare in questa cartella.
3. Aggiungere un file `php.ini` alla cartella `php`. Abilitare eventuali estensioni personalizzate e impostare qualsiasi direttiva PHP in questo file. Ad esempio, se si desidera attivare `display_errors` e abilitare l'estensione `php_mongo.dll` il contenuto del file `php.ini` sarà il seguente:

		display_errors=On
		extension=php_mongo.dll

> [AZURE.NOTE]Qualsiasi impostazione non esplicitamente impostata nel file `php.ini` fornito verrà automaticamente impostata sui rispettivi valori predefiniti. Si tenga tuttavia presente che è possibile aggiungere un file `php.ini` completo.

##<a name="OwnPHP"></a>Procedura: Usare il proprio runtime PHP
In alcuni casi, invece di selezionare un runtime PHP incorporato e configurarlo come sopra descritto, può essere consigliabile fornire un proprio runtime PHP. Ad esempio, è possibile usare lo stesso runtime PHP in un ruolo Web o di lavoro usato nell'ambiente di sviluppo, in modo che sia più semplice assicurarsi che il comportamento dell'applicazione non cambi nell'ambiente di produzione.

<h3><a name="OwnPHPWebRole"></a>Configurazione di un ruolo Web per l'uso del proprio runtime PHP</h3>

Per configurare un ruolo Web per l'uso di un runtime PHP fornito dall'utente, eseguire la procedura seguente.

1. Creare un progetto di servizio Azure e aggiungere un ruolo Web PHP come descritto nelle sezioni [Procedura: Creare un progetto di servizi cloud](#CreateProject) e [Procedura: Aggiungere ruoli Web o di lavoro PHP](#AddRole) sopra riportate.
2. Creare una cartella `php` nella cartella `bin` che si trova nella directory radice del proprio ruolo Web, quindi aggiungere il proprio runtime PHP (tutti i binari, i file di configurazione, le sottocartelle, ecc.) alla cartella `php`.
3. (FACOLTATIVO) Se il proprio runtime PHP usa i [Driver Microsoft per PHP per SQL Server][sqlsrv drivers], sarà necessario configurare il proprio ruolo Web per installare [SQL Server Native Client 2012][sql native client] quando viene eseguito il provisioning. A tale scopo, aggiungere il programma di installazione `sqlncli.msi` alla cartella `bin` nella directory radice del proprio ruolo Web. È possibile scaricare il programma di installazione all'indirizzo: [sqlncli.msi x64 installer]. Lo script di avvio descritto nel passaggio successivo eseguirà il programma di installazione senza avvisi quando viene eseguito il provisioning del ruolo. Se il proprio runtime PHP non usa i Driver Microsoft per PHP per SQL Server è possibile rimuovere la seguente riga dallo script illustrato nel passaggio successivo:

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Il passaggio successivo consiste nel definire un'attività di avvio per configurare [Internet Information Services (IIS][iis.net]) per l'uso del runtime PHP nella gestione delle richieste di pagine `.php`. A tale scopo, aprire il file `setup_web.cmd` (nel file `bin` della directory radice del proprio ruolo Web) in un editor di testo e sostituirne il contenuto con lo script seguente:

		@ECHO ON
		cd "%~dp0"
		
		if "%EMULATED%"=="true" exit /b 0
		
		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
		
		SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
		SET NEW_PATH=%PATH%;%RoleRoot%\base\x86
		
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Aggiungere i file applicazione alla directory radice del proprio ruolo Web, che sarà la directory radice del server Web.

6. Pubblicare l'applicazione come descritto nella sezione [Procedura: Pubblicare l'applicazione](#Publish) riportata di seguito.

> [AZURE.NOTE]Lo script `download.ps1` (nella cartella `bin` della directory radice del proprio ruolo Web) può essere eliminato dopo aver eseguito i passaggi sopra descritti per l'uso del proprio runtime PHP.

<h3><a name="OwnPHPWorkerRole"></a>Configurazione di un ruolo di lavoro per l'uso del proprio runtime PHP</h3>

Per configurare un ruolo di lavoro per l'uso di un runtime PHP fornito dall'utente, eseguire la procedura seguente.

1. Creare un progetto di servizio Azure e aggiungere un ruolo di lavoro PHP come descritto nelle sezioni [Procedura: Creare un progetto di servizi cloud](#CreateProject) e [Procedura: Aggiungere ruoli Web o di lavoro PHP](#AddRole) sopra riportate.
2. Creare una cartella `php` nella directory radice del proprio ruolo Web, quindi aggiungere il proprio runtime PHP (tutti i binari, i file di configurazione, le sottocartelle, ecc.) alla cartella `php`.
3. (FACOLTATIVO) Se il proprio runtime PHP usa i [Driver Microsoft per PHP per SQL Server][sqlsrv drivers], sarà necessario configurare il proprio ruolo di lavoro per installare [SQL Server Native Client 2012][sql native client] quando viene eseguito il provisioning. A tale scopo, aggiungere il programma di installazione `sqlncli.msi` alla directory radice del proprio ruolo di lavoro. È possibile scaricare il programma di installazione all'indirizzo:  [sqlncli.msi x64 installer]. Lo script di avvio descritto nel passaggio successivo eseguirà il programma di installazione senza avvisi quando viene eseguito il provisioning del ruolo. Se il proprio runtime PHP non usa i Driver Microsoft per PHP per SQL Server è possibile rimuovere la seguente riga dallo script illustrato nel passaggio successivo:

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Il passaggio successivo consiste nel definire un'attività di avvio che consente di aggiungere l'eseguibile `php.exe` alla variabile di ambiente PATH del ruolo di lavoro durante il provisioning del ruolo. A tale scopo, aprire il file `setup_worker.cmd` (nella directory radice del proprio ruolo di lavoro) in un editor di testo e sostituirne il contenuto con lo script seguente:

		@echo on

		cd "%~dp0"

		echo Granting permissions for Network Service to the web root directory...
		icacls ..\ /grant "Network Service":(OI)(CI)W
		if %ERRORLEVEL% neq 0 goto error
		echo OK

		if "%EMULATED%"=="true" exit /b 0

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

		setx Path "%PATH%;%~dp0php" /M

		if %ERRORLEVEL% neq 0 goto error

		echo SUCCESS
		exit /b 0

		:error

		echo FAILED
		exit /b -1	

5. Aggiungere i file applicazione alla directory radice del proprio ruolo di lavoro,

6. Pubblicare l'applicazione come descritto nella sezione [Procedura: Pubblicare l'applicazione](#Publish) riportata di seguito.

##<a name="Emulators"></a>Procedura: Eseguire l'applicazione negli emulatori di calcolo e archiviazione

Gli emulatori di calcolo e di archiviazione di Azure forniscono un ambiente locale in cui è possibile testare l'applicazione Azure prima di distribuirla nel cloud. Vi sono alcune differenze tra gli emulatori e l'ambiente Azure. Per meglio comprenderle, vedere [Differenze tra l'emulatore di calcolo e Azure](http://msdn.microsoft.com/library/windowsazure/gg432960.aspx) e [Differenze tra l'emulatore di archiviazione e i Servizi di archiviazione Azure](http://msdn.microsoft.com/library/windowsazure/gg433135.aspx).

Si noti che per usare l'emulatore di calcolo è necessario aver installato PHP in locale. L'emulatore di calcolo userà l'installazione locale di PHP per eseguire l'applicazione.

Per eseguire il progetto negli emulatori, eseguire il comando seguente dalla directory radice del progetto:

	PS C:\MyProject> Start-AzureEmulator

L'output sarà simile al seguente:

	Creating local package...
	Starting Emulator...
	Role is running at http://127.0.0.1:81
	Started

Per visualizzare l'applicazione in esecuzione sull'emulatore, aprire un Web browser e immettere l'indirizzo locale indicato nell'output (`http://127.0.0.1:81` nell'output di esempio sopra riportato).

Per arrestare gli emulatori, eseguire il comando seguente:

	PS C:\MyProject> Stop-AzureEmulator

##<a name="Publish"></a>Procedura: Pubblicare l'applicazione

Per pubblicare l'applicazione è necessario prima importare le impostazioni di pubblicazione con il cmdlet **Import-PublishSettingsFile**, quindi sarà possibile pubblicare l'applicazione con il cmdlet **Publish-AzureServiceProject**. Informazioni dettagliate sull'uso di ciascuno dei cmdlet sono disponibili rispettivamente nelle sezioni [Procedura: Importare le impostazioni di pubblicazione] e [Procedura: Distribuire un servizio cloud in Azure].

[execution model-web sites]: /develop/net/fundamentals/compute/#WebSites
[execution model-vms]: /develop/net/fundamentals/compute/#VMachine
[execution model-cloud services]: /develop/net/fundamentals/compute/#CloudServices
[Azure SDK per PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/?LinkId=253447&clcid=0x409
[definizione di servizio cloud]: /manage/services/cloud-services/what-is-a-cloud-service/
[definizione del servizio (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configurazione del servizio(.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 installer]: http://go.microsoft.com/fwlink/?LinkID=239648
[Procedura: Importare le impostazioni di pubblicazione]: /develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
[Procedura: Distribuire un servizio cloud in Azure]: /develop/php/how-to-guides/powershell-cmdlets/#Deploy

<!--HONumber=54-->