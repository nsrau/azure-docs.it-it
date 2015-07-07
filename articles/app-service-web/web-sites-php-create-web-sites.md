<properties
	pageTitle="Creare un'app Web PHP nel servizio app di Azure"
	description="Informazioni su come creare un'app Web PHP nel servizio app di Azure"
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
	ms.topic="hero-article"
	ms.date="04/29/2015"
	ms.author="tomfitz"/>

# Creare un'app Web PHP nel servizio app di Azure

## Panoramica
Questo articolo illustra come creare un'app Web PHP nel [servizio app di Azure] usando il [portale di anteprima di Azure](https://portal.azure.com), l'[interfaccia della riga di comando di Azure][Azure CLI] o i [cmdlet di Azure PowerShell][powershell-cmdlets].

In generale, la creazione di un'app Web PHP non è diversa da quella di *app Web* nel servizio app di Azure. Per impostazione predefinita, PHP è abilitato per tutte le app Web. Per informazioni sulla configurazione di PHP o su come specificare il proprio runtime PHP personalizzato, vedere [Configurare PHP in App Web del servizio app di Azure].

Per ogni opzione descritta di seguito viene illustrato come creare un'app Web in un ambiente host condiviso senza addebito di costi, ma con alcune limitazioni per quanto riguarda l'utilizzo di CPU e larghezza di banda. Per altre informazioni, vedere [Prezzi del servizio app]. Per informazioni su come aggiornare e scalare l'app Web nel servizio app, vedere [Scalare un'app Web nel servizio app di Azure].

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un'app Web PHP usando il portale di anteprima di Azure

Per la creazione di un'app Web PHP nel portale di anteprima di Azure sono disponibili tre opzioni:

- **Creazione rapida**: vedere [Procedura: Creare un'app Web usando il portale di anteprima di Azure](../web-sites-create-deploy.md#createawebsiteportal)
- **Creazione con database**: vedere [Creare un'app Web PHP-MySQL nel servizio app di Azure e distribuirla tramite Git]
- **Da Marketplace**: vedere [Creare un'app Web WordPress nel servizio app di Azure]

## Creare un'app Web PHP usando l'interfaccia della riga di comando di Azure

Per creare un'app Web PHP con l'interfaccia della riga di comando di Azure, eseguire le operazioni seguenti:

1. Installare l'interfaccia della riga di comando di Azure seguendo le istruzioni riportate in [Come installare l'interfaccia della riga di comando di Azure](../xplat-cli.md#install).

1. Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Come scaricare e importare impostazioni di pubblicazione](../xplat-cli.md#configure).

1. Eseguire il comando seguente da un prompt dei comandi:

		azure site create MyWebAppName

L'URL della nuova app Web è `http://MyWebAppName.azurewebsites.net`.

Si noti che è possibile eseguire il comando `azure site create` con una qualsiasi delle opzioni seguenti:

* `--location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creata l'app Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
* `--hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per l'app Web.
* `--git`. Questa opzione consente di usare Git per pubblicare nell'app Web creando archivi Git sia nella directory delle applicazioni locale sia nel data center dell'app Web. Si noti che qualora la cartella locale sia già un archivio Git, il comando aggiungerà a quello esistente un nuovo archivio remoto che punterà all'archivio nel data center dell'app Web.

## Creare un'app Web PHP con i cmdlet di Azure PowerShell

Per creare un'app Web PHP con i cmdlet di Azure PowerShell, effettuare le seguenti operazioni:

1. Installare i cmdlet di Azure PowerShell seguendo le istruzioni riportate nella [guida introduttiva ad Azure PowerShell](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Procedura: Importare impostazioni di pubblicazione](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Aprire un prompt dei comandi PowerShell ed eseguire il comando seguente:

		New-AzureWebsite MyWebAppName

L'URL della nuova app Web è `http://MyWebAppName.azurewebsites.net`.

Si noti che è possibile eseguire il comando `New-AzureWebsite` con una qualsiasi delle opzioni seguenti:

* `-Location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creata l'app Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
* `-Hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per l'app Web.
* `-Git`. Questa opzione consente di usare Git per pubblicare nell'app Web creando archivi Git sia nella directory delle applicazioni locale sia nel data center dell'app Web. Si noti che qualora la cartella locale sia già un archivio Git, il comando aggiungerà a quello esistente un nuovo archivio remoto che punterà all'archivio nel data center dell'app Web.

## Passaggi successivi

Dopo aver creato un'app Web PHP nel servizio app di Azure, è possibile eseguirvi operazioni di gestione, configurazione, monitoraggio e distribuzione ed è inoltre possibile scalarla. Per altre informazioni, vedere i collegamenti seguenti:

* [Configurare app Web nel servizio app di Azure](web-sites-configure.md)
* [Configurare PHP in App Web del servizio app di Azure]
* [Gestire app Web usando il portale di Azure](web-sites-manage.md)
* [Eseguire il monitoraggio delle app Web nel servizio app di Azure](web-sites-monitor.md)
* [Scalare un'app Web nel servizio app di Azure]
* [Distribuzione continua tramite GIT nel servizio app di Azure](web-sites-publish-source-control.md)

Per le esercitazioni complete, visitare la pagina delle [esercitazioni nel Centro per sviluppatori di PHP](/develop/php/tutorials/).

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al portale di anteprima, vedere [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[servizio app di Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Portal]: http://go.microsoft.com/fwlink/?LinkId=529715
[Azure CLI]: ../xplat-cli.md
[powershell-cmdlets]: ../powershell-install-configure.md
[Configurare PHP in App Web del servizio app di Azure]: web-sites-php-configure.md
[Creare un'app Web PHP-MySQL nel servizio app di Azure e distribuirla tramite Git]: web-sites-php-mysql-deploy-use-git.md
[Creare un'app Web WordPress nel servizio app di Azure]: web-sites-php-web-site-gallery.md
[Prezzi del servizio app]: /pricing/details/app-service/
[Scalare un'app Web nel servizio app di Azure]: web-sites-scale.md
 

<!---HONumber=62-->