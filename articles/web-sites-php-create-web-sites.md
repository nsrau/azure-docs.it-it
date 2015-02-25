<properties 
	pageTitle="Come creare un sito Web PHP in Siti Web di Azure" 
	description="Informazioni su come creare un sito Web PHP in Siti Web di Azure" 
	documentationCenter="php" 
	services="" 
	editor="mollybos" 
	manager="wpickett" 
	authors="tfitzmac"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

#Come creare un sito Web PHP in Siti Web di Azure

In questo articolo viene illustrato come creare un sito Web PHP in [Siti Web di Azure][waws] usando il [portale di gestione di Azure], gli [strumenti da riga di comando di Azure per Mac e Linux][xplat-tools] oppure i [cmdlet di Azure PowerShell][powershell-cmdlets].

In generale, la creazione di un sito Web PHP non è diversa da quella di QUALSIASI sito Web in Siti Web di Azure. Per impostazione predefinita, PHP è abilitato per tutti i siti Web. Per informazioni sulla configurazione di PHP o su come specificare il proprio runtime PHP personalizzato, vedere [Come configurare PHP in Siti Web di Azure][configure-php].

Per ogni opzione descritta di seguito viene illustrato come creare un sito Web in un ambiente host condiviso senza alcun costo, ma con alcune limitazioni per quanto riguarda l'uso della CPU e della larghezza di banda. Per altre informazioni, vedere la [Panoramica dei prezzi dei Siti Web di Azure][websites-pricing]. Per informazioni su come aggiornare e ridimensionare il sito Web, vedere [Come scalare siti Web][scale-websites].

> [AZURE.NOTE]
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Sommario
* [Creare un sito Web mediante il portale di gestione di Azure](#portal)
* [Creare un sito Web con gli strumenti da riga di comando per Mac e Linux](#XplatTools)
* [Creare un sito Web con i cmdlet di Azure PowerShell](#PowerShell)

<h2><a name="portal"></a>Creare un sito Web PHP mediante il portale di gestione di Azure</h2>

Per la creazione di un sito Web nel portale di gestione di Azure sono disponibili tre opzioni: **Creazione rapida**, **Crea con database** e **Da raccolta**. Nelle istruzioni seguenti viene trattata l'opzione **Creazione rapida**. Per informazioni sulle altre due opzioni, vedere [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git][website-mysql-git] e [Creazione di un sito Web WordPress dalla raccolta in Azure][wordpress-gallery].

Per creare un sito Web PHP mediante il portale di gestione di Azure, eseguire le operazioni seguenti:

1. Accedere al [portale di gestione di Azure].
1. Fare clic su **Nuovo** nella parte inferiore della pagina, quindi selezionare **Calcolo**, **Sito Web** e **Creazione rapida**. Specificare un **URL** per il sito Web e selezionare l'**Area** per il sito Web. Infine, fare clic su **Crea sito Web**.

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Creare un sito Web PHP con gli strumenti da riga di comando per Mac e Linux</h2>

Per creare un sito Web PHP con gli strumenti da riga di comando per Mac e Linux, eseguire le operazioni seguenti:

1. Installare gli strumenti da riga di comando di Azure seguendo le istruzioni riportate in [Come installare gli strumenti da riga di comando di Azure per Mac e Linux](/it-it/develop/php/how-to-guides/command-line-tools/#Download).

1. Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Come scaricare e importare impostazioni di pubblicazione](/it-it/develop/php/how-to-guides/command-line-tools/#Account).

1. Eseguire il comando seguente da un prompt dei comandi:

		azure site create MySiteName

L'URL del nuovo sito Web sarà `http://MySiteName.azurewebsites.net`.  
 
Si noti che è possibile eseguire il comando  `azure site create` con una qualsiasi delle opzioni seguenti:

* `--location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creato il sito Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
* `--hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per il sito Web.
* `--git`. L'opzione consente di usare Git per eseguire la pubblicazione nel sito Web creando repository Git sia nella directory delle applicazioni locale che nel data center del sito Web. Si noti che qualora la cartella locale sia già un repository Git, il comando aggiungerà a quello esistente un nuovo repository remoto che punterà al repository del data center del sito Web.

Per informazioni sulle altre opzioni, vedere [Come creare e gestire un sito Web di Azure](/it-it/develop/php/how-to-guides/command-line-tools/#WebSites).

<h2><a name="PowerShell"></a>Creare un sito Web PHP con i cmdlet di Azure PowerShell</h2>

Per creare un sito Web PHP con i cmdlet di Azure PowerShell, eseguire le operazioni seguenti:

1. Installare i cmdlet di Azure PowerShell seguendo le istruzioni riportate nella [Guida introduttiva ad Azure PowerShell](/it-it/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Procedura: Importare le impostazioni di pubblicazione](/it-it/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Aprire un prompt dei comandi PowerShell ed eseguire il comando seguente:

		New-AzureWebSite MySiteName

L'URL del nuovo sito Web sarà  `http://MySiteName.azurewebsites.net`.  
 
Si noti che è possibile eseguire il comando  `New-AzureWebSite` con una qualsiasi delle opzioni seguenti:

* `-Location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creato il sito Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
* `-Hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per il sito Web.
* `-Git`. L'opzione consente di usare Git per eseguire la pubblicazione nel sito Web creando repository Git sia nella directory delle applicazioni locale che nel data center del sito Web. Si noti che qualora la cartella locale sia già un repository Git, il comando aggiungerà a quello esistente un nuovo repository remoto che punterà al repository del data center del sito Web.

Per informazioni sulle altre opzioni, vedere [Procedura: Creare e gestire un sito Web di Azure](/it-it/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>Passaggi successivi</h2>

Dopo aver creato un sito Web PHP in Siti Web di Azure, è possibile eseguirvi operazioni di gestione, configurazione, monitoraggio e distribuzione, ed è inoltre possibile ridimensionarlo. Per altre informazioni, vedere i collegamenti seguenti:

* [Come configurare i siti Web](/it-it/manage/services/web-sites/how-to-configure-websites/)
* [Come configurare PHP in Siti Web di Azure][configure-php]
* [Come gestire i siti Web](/it-it/manage/services/web-sites/how-to-manage-websites/)
* [Come monitorare i siti Web](/it-it/manage/services/web-sites/how-to-monitor-websites/)
* [Come scalare siti Web](/it-it/manage/services/web-sites/how-to-scale-websites/)
* [Pubblicazione con Git](/it-it/develop/php/common-tasks/publishing-with-git/)

Per le esercitazioni complete, visitare la pagina delle [esercitazioni nel Centro per sviluppatori di PHP](/it-it/develop/php/tutorials/).

[waws]: /it-it/manage/services/web-sites/
[Portale di gestione di Azure]: http://manage.windowsazure.com/
[xplat-tools]: /it-it/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /it-it/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /it-it/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /it-it/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /it-it/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://www.windowsazure.com/it-it/pricing/details/#header-1
[scale-websites]: /it-it/manage/services/web-sites/how-to-scale-websites/


<!--HONumber=42-->
