<properties 
	pageTitle="Creare un'app Web PHP in Servizio app di Azure" 
	description="Informazioni su come creare un'app Web PHP in Servizio app di Azure" 
	documentationCenter="php" 
	services="app-service\web" 
	editor="mollybos" 
	manager="wpickett" 
	authors="tfitzmac"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

# Creazione di un'app Web PHP in Servizio app di Azure

## Informazioni generali
Questo articolo illustra come creare un'app Web PHP in [Servizio app di Azure] usando il [portale di Azure], gli [strumenti da riga di comando di Azure per Mac e Linux][xplat-tools] oppure i [cmdlet di Azure PowerShell][powershell-cmdlets].

In generale, la creazione di un'app Web PHP non è diversa da quella di *any* app Web in Servizio app di Azure. Per impostazione predefinita, PHP è abilitato per tutte le app Web. Per informazioni sulla configurazione di PHP o su come specificare il proprio runtime PHP personalizzato, vedere l'argomento relativo alla [configurazione di PHP nelle app Web di Servizio app di Azure].

Per ogni opzione descritta di seguito viene illustrato come creare un'app Web in un ambiente host condiviso senza addebito di costi, ma con alcune limitazioni per quanto riguarda l'utilizzo di CPU e larghezza di banda. Per altre informazioni, vedere [Servizio app Prezzi]. Per informazioni su come aggiornare e scalare l'app Web in Servizio app di Azure, vedere l'argomento [Scalare un'app Web in Servizio app di Azure].

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<a name="portal"></a>
## Creare un'app Web PHP mediante il portale di Azure

Per la creazione di un'app Web PHP nel portale di Azure sono disponibili tre opzioni: 

- **Creazione rapida**. Vedere [Procedura: Creare un'app Web tramite il portale di Azure](web-sites-create-deploy.md#createawebsiteportal)
- **Creazione con database**. Vedere l'argomento relativo alla [creazione di un'app Web PHP-MySQL in Servizio app di Azure e distribuzione tramite Git]. 
- **Da Marketplace**. Vedere l'argomento relativo alla [creazione di un'app Web WordPress in Servizio app di Azure].

<a name="XplatTools"></a>
## Creare un'app Web PHP con gli strumenti da riga di comando di Azure per Mac e Linux

Per creare un'app Web PHP con gli strumenti da riga di comando di Azure per Mac e Linux, effettuare le seguenti operazioni:

1. Installare gli strumenti da riga di comando di Azure seguendo le istruzioni riportate in [Come installare gli strumenti da riga di comando di Azure per Mac e Linux](xplat-cli.md#install).

1. Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Come scaricare e importare impostazioni di pubblicazione](xplat-cli.md#configure).

1. Eseguire il comando seguente da un prompt dei comandi:

		azure site create MyWebAppName

L'URL della nuova app Web sarà  `http://MyWebAppName.azurewebsites.net`.  
 
Si noti che è possibile eseguire il comando  `azure site create` con una qualsiasi delle seguenti opzioni:

* `--location [nome percorso]`. Questa opzione consente di specificare il percorso del data center in cui viene creata l'app Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
* `--hostname [nome host personalizzato]`. Questa opzione consente di specificare un nome host personalizzato per l'app Web.
* `--git`. Questa opzione consente di usare Git per pubblicare nell'app Web creando archivi Git sia nella directory delle applicazioni locale sia nel data center dell'app Web. Si noti che qualora la cartella locale sia già un archivio Git, il comando aggiungerà a quello esistente un nuovo archivio remoto che punterà all'archivio nel data center dell'app Web.

<a name="PowerShell"></a>
## Creare un'app Web PHP con i cmdlet di Azure PowerShell

Per creare un'app Web PHP con i cmdlet di Azure PowerShell, effettuare le seguenti operazioni:

1. Installare i cmdlet di Azure PowerShell seguendo le istruzioni riportate nella [guida introduttiva ad Azure PowerShell](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Procedura: Importare le impostazioni di pubblicazione](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Aprire un prompt dei comandi PowerShell ed eseguire il comando seguente:

		New-AzureWebsite MyWebAppName

L'URL della nuova app Web sarà `http://MyWebAppName.azurewebsites.net`.  
 
Si noti che è possibile eseguire il comando  `New-AzureWebsite` con una qualsiasi delle seguenti opzioni:

* `-Location [nome percorso]`. Questa opzione consente di specificare il percorso del data center in cui viene creata l'app Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
* `-Hostname [nome host personalizzato]`. Questa opzione consente di specificare un nome host personalizzato per l'app Web.
* `-Git`. Questa opzione consente di usare Git per pubblicare nell'app Web creando archivi Git sia nella directory delle applicazioni locale sia nel data center dell'app Web. Si noti che qualora la cartella locale sia già un archivio Git, il comando aggiungerà a quello esistente un nuovo archivio remoto che punterà all'archivio nel data center dell'app Web.

<a name="NextSteps"></a>
## Passaggi successivi

Dopo aver creato un'app Web PHP in Servizio app di Azure, è possibile eseguirvi operazioni di gestione, configurazione, monitoraggio e distribuzione ed è inoltre possibile scalarla. Per altre informazioni, vedere i collegamenti seguenti:

* [Configurazione delle app Web in Servizio app di Azure](web-sites-configure.md)
* [Configurazione di PHP nelle app Web di Servizio app di Azure]
* [Gestione delle app Web tramite il portale di Azure](web-sites-manage.md)
* [Monitoraggio delle app Web in Servizio app di Azure](web-sites-monitor.md)
* [Scalare un'app Web in Servizio app di Azure]
* [Distribuzione continua tramite GIT in Servizio app di Azure](web-sites-publish-source-control.md)

Per le esercitazioni complete, visitare la pagina delle [esercitazioni nel Centro per sviluppatori di PHP](/develop/php/tutorials/) .

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Servizio app di Azure e relativo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida sul passaggio dal vecchio al nuovo portale, vedere: [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[Servizio app di Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Portale di Azure]: http://go.microsoft.com/fwlink/?LinkId=529715
[xplat-tools]: xplat-cli.md
[powershell-cmdlets]: powershell-install-configure.md
[Configurazione di PHP nelle app Web di Servizio app di Azure]: web-sites-php-configure.md
[Creazione di un'app Web PHP-MySQL in Servizio app di Azure e distribuzione tramite Git]: web-sites-php-mysql-deploy-use-git.md
[creazione di un'app Web WordPress in Servizio app di Azure]: web-sites-php-web-site-gallery.md
[Servizio app Prezzi]: /pricing/details/app-service/
[Scalare un'app Web in Servizio app di Azure]: web-sites-scale.md


<!--HONumber=52-->