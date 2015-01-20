<properties title="How to create a PHP website in Azure Websites" pageTitle="Come creare un sito Web PHP in Siti Web di Azure" metaKeywords ="Siti Web di Azure PHP"" description="Informazioni su come creare un sito Web PHP in Siti Web di Azure" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="tomfitz" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Come creare un sito Web PHP in Siti Web di Azure

In questo articolo viene illustrato come creare un sito Web PHP in [Siti Web di Azure][waws] usando il [portale di gestione di Azure], gli [strumenti da riga di comando di Azure per Mac e Linux][xplat-tools] oppure i [cmdlet di Azure PowerShell][powershell-cmdlets].

In generale, la creazione di un sito Web PHP non è diversa da quella di *qualsiasi* sito Web in Siti Web di Azure. Per impostazione predefinita, PHP è abilitato per tutti i siti Web. Per informazioni sulla configurazione di PHP o su come specificare il proprio runtime PHP personalizzato, vedere [Come configurare PHP in Siti Web di Azure][configure-php].

Per ogni opzione descritta di seguito viene illustrato come creare un sito Web in un ambiente host condiviso senza alcun costo, ma con alcune limitazioni per quanto riguarda l'uso della CPU e della larghezza di banda. Per altre informazioni, vedere la [Panoramica dei prezzi dei Siti Web di Azure][websites-pricing]. Per informazioni su come aggiornare e ridimensionare il sito Web, vedere [Come applicare la scalabilità ai siti Web][scale-websites].

> [WACOM.NOTE]
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Sommario
* [Creare un sito Web PHP mediante il portale di gestione di Azure](#portal)
* [Creare un sito Web PHP con gli strumenti da riga di comando per Mac e Linux](#XplatTools)
* [Creare un sito Web PHP con i cmdlet di Azure PowerShell](#PowerShell)

<h2><a name="portal"></a>Creare un sito Web PHP mediante il portale di gestione di Azure</h2>

Per la creazione di un sito Web nel portale di gestione di Azure sono disponibili tre opzioni: **Creazione rapida**, **Crea con database** e **Da raccolta**. Nelle istruzioni seguenti viene trattata l'opzione **Creazione rapida**. Per informazioni sulle altre due opzioni, vedere [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git][website-mysql-git] e [Creazione di un sito Web WordPress dalla raccolta in Azure][wordpress-gallery].

Per creare un sito Web PHP mediante il portale di gestione di Azure, eseguire le operazioni seguenti:

1. Accedere al [portale di gestione di Azure].
1. Fare clic su **Nuovo** nella parte inferiore della pagina, quindi selezionare **Calcolo**, **Sito Web** e **Creazione rapida**. Specificare un **URL** per il sito Web e selezionare l'**Area** per il sito Web. Infine, fare clic su **Crea sito Web**.

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Creare un sito Web PHP con gli strumenti da riga di comando per Mac e Linux</h2>

Per creare un sito Web PHP con gli strumenti da riga di comando per Mac e Linux, eseguire le operazioni seguenti:

1. Installare gli strumenti da riga di comando di Azure seguendo le istruzioni riportate in [Come installare gli strumenti da riga di comando di Azure per Mac e Linux](/it-it/develop/php/how-to-guides/command-line-tools/#Download).

1. Download and import your publish settings file by following the instructions here: [How to download and import publish settings](/it-it/develop/php/how-to-guides/command-line-tools/#Account).

1. Run the following command from a command prompt:

		azure site create MySiteName

The URL for the newly created website will be  `http://MySiteName.azurewebsites.net`.  
 
Note that you can execute the `azure site create` command with any of the following options:

* `--location [location name]`. This option allows you to specify the location of the data center in which your website is created (e.g. "West US"). If you omit this option, you will be promted to choose a location.
* `--hostname [custom host name]`. This option allows you to specify a custom hostname for your website.
* `--git`. This option allows you to use git to publish to your website by creating git repositories in both your local application directory and in your website's data center. Note that if your local folder is already a git repository, the command will add a new remote to the existing repository, pointing to the repository in your website's data center.

For information about additional options, see [How to create and manage an Azure Web Site](/it-it/develop/php/how-to-guides/command-line-tools/#WebSites).

<h2><a name="PowerShell"></a>Create a PHP website using the Azure PowerShell cmdlets</h2>

To create a PHP website using the Azure PowerShell cmdlets, do the following:

1. Install the Azure PowerShell cmdlets by following the instructions here: [Get started with Azure PowerShell](/it-it/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Download and import your publish settings file by following the instructions here: [How to: Import publish settings](/it-it/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Open a PowerShell command prompt and execute the following command:

		New-AzureWebSite MySiteName

The URL for the newly created website will be  `http://MySiteName.azurewebsites.net`.  
 
Note that you can execute the `New-AzureWebSite` command with any of the following options:

* `-Location [location name]`. This option allows you to specify the location of the data center in which your website is created (e.g. "West US"). If you omit this option, you will be promted to choose a location.
* `-Hostname [custom host name]`. This option allows you to specify a custom hostname for your website.
* `-Git`. This option allows you to use git to publish to your website by creating git repositories in both your local application directory and in your website's data center. Note that if your local folder is already a git repository, the command will add a new remote to the existing repository, pointing to the repository in your website's data center.

For information about additional options, see [How to: Create and manage an Azure Web Site](/it-it/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>Next steps</h2>

Now that you have created a PHP website in Azure Websites, you can manage, configure, monitor, deploy to, and scale your site. For more information, see the following links:

* [How to configure Web Sites](/it-it/manage/services/web-sites/how-to-configure-websites/)
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

<!--HONumber=35.2-->
