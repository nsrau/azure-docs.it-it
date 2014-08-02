<properties title="How to create a PHP web site in Azure Web Sites" pageTitle="How to create a PHP web site in Azure Web Sites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP web site in Azure Web Sites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="bjsmith" authors="" />

Come creare un sito Web PHP in Siti Web di Azure
================================================

In questo articolo viene illustrato come creare un sito Web PHP in [Siti Web di Azure](/en-us/manage/services/web-sites/) utilizzando il [portale di gestione di Azure](http://manage.windowsazure.com/), gli [strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/php/how-to-guides/command-line-tools/) oppure i [cmdlet di Azure PowerShell](/en-us/develop/php/how-to-guides/powershell-cmdlets/).

In generale, la creazione di un sito Web PHP non è diversa da quella di *qualsiasi* sito Web in Siti Web di Azure. Per impostazione predefinita, PHP è abilitato per tutti i siti Web. Per informazioni sulla configurazione di PHP o su come specificare il proprio runtime PHP personalizzato, vedere [Come configurare PHP in Siti Web di Azure](/en-us/develop/php/common-tasks/configure-php-web-site/).

Per ogni opzione descritta di seguito viene illustrato come creare un sito Web in un ambiente host condiviso senza addebito di costi, ma con alcune limitazioni per quanto riguarda l'utilizzo di CPU e larghezza di banda. Per ulteriori informazioni, vedere la [Panoramica dei prezzi dei Siti Web di Azure](http://www.windowsazure.com/en-us/pricing/details/#header-1). Per informazioni su come aggiornare e scalare il sito Web, vedere [Come applicare la scalabilità ai siti Web](/en-us/manage/services/web-sites/how-to-scale-websites/).

Sommario
--------

-   [Creazione di un sito Web mediante il portale di gestione di Azure](#portal)
-   [Creazione di un sito Web con gli strumenti da riga di comando per Mac e Linux](#XplatTools)
-   [Creazione di un sito Web con i cmdlet di Azure PowerShell](#PowerShell)

Creazione di un sito Web PHP mediante il portale di gestione di Azure
---------------------------------------------------------------------

Per la creazione di un sito Web nel portale di gestione di Azure sono disponibili tre opzioni: **Quick Create**, **Create with Database** e **From Gallery**. Nelle istruzioni seguenti viene utilizzata l'opzione **Quick Create**. Per informazioni sulle altre due opzioni, vedere [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git](/en-us/develop/php/tutorials/website-w-mysql-and-git/) e [Creazione di un sito Web WordPress dalla raccolta in Azure](/en-us/develop/php/tutorials/website-from-gallery/).

Per creare un sito Web PHP mediante il portale di gestione di Azure, eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).
2.  Fare clic su **New** nella parte inferiore della pagina, quindi selezionare **Compute**, **Web Site** e infine **Quick Create**. Specificare un **URL** per il sito Web e selezionare un valore per **Region** per il sito Web. Infine, fare clic su **Create Web Site**.

    ![Selezione di Quick Create](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

Creazione di un sito Web PHP con gli strumenti da riga di comando per Mac e Linux
---------------------------------------------------------------------------------

Per creare un sito Web PHP con gli strumenti da riga di comando per Mac e Linux, eseguire le operazioni seguenti:

1.  Installare gli strumenti da riga di comando di Azure seguendo le istruzioni riportate in [Come installare gli strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/php/how-to-guides/command-line-tools/#Download).

2.  Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Come scaricare e importare impostazioni di pubblicazione](/en-us/develop/php/how-to-guides/command-line-tools/#Account).

3.  Eseguire il comando seguente da un prompt dei comandi:

         azure site create MySiteName

L'URL del nuovo sito Web sarà `http://MySiteName.azurewebsites.net`.

È possibile eseguire il comando `azure site create` con una qualsiasi delle opzioni seguenti:

-   `--location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creato il sito Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
-   `--hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per il sito Web.
-   `--git`. Questa opzione consente di utilizzare Git per pubblicare nel sito Web creando archivi Git sia nella directory delle applicazioni locale che nel data center del sito Web. Si noti che qualora la cartella locale sia già un archivio Git, il comando aggiungerà a quello esistente un nuovo archivio remoto che punterà all'archivio del data center del sito Web.

Per informazioni sulle altre opzioni, vedere [Come creare e gestire un sito Web di Azure](/en-us/develop/php/how-to-guides/command-line-tools/#WebSites).

Creazione di un sito Web PHP con i cmdlet di Azure PowerShell
-------------------------------------------------------------

Per creare un sito Web PHP con i cmdlet di Azure PowerShell, eseguire le operazioni seguenti:

1.  Installare i cmdlet di Azure PowerShell seguendo le istruzioni riportate in [Guida introduttiva ad Azure PowerShell](/en-us/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

2.  Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Procedura: Importare le impostazioni di pubblicazione](/en-us/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

3.  Aprire un prompt dei comandi PowerShell ed eseguire il comando seguente:

         New-AzureWebSite MySiteName

L'URL del nuovo sito Web sarà `http://MySiteName.azurewebsites.net`.

È possibile eseguire il comando `New-AzureWebSite` con una qualsiasi delle opzioni seguenti:

-   `-Location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creato il sito Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
-   `-Hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per il sito Web.
-   `-Git`. Questa opzione consente di utilizzare Git per pubblicare nel sito Web creando archivi Git sia nella directory delle applicazioni locale che nel data center del sito Web. Si noti che qualora la cartella locale sia già un archivio Git, il comando aggiungerà a quello esistente un nuovo archivio remoto che punterà all'archivio del data center del sito Web.

Per informazioni sulle altre opzioni, vedere [Procedura: Creare e gestire un sito Web di Azure](/en-us/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

Passaggi successivi
-------------------

Dopo aver creato un sito Web PHP in Siti Web di Azure, è possibile eseguirvi operazioni di gestione, configurazione, monitoraggio distribuzione ed è inoltre possibile ridimensionarlo. Per ulteriori informazioni, vedere i collegamenti seguenti:

-   [Come configurare i siti Web](/en-us/manage/services/web-sites/how-to-configure-websites/)
-   [Come configurare PHP in Siti Web di Azure](/en-us/develop/php/common-tasks/configure-php-web-site/)
-   [Come gestire i siti Web](/en-us/manage/services/web-sites/how-to-manage-websites/)
-   [Come monitorare i siti Web](/en-us/manage/services/web-sites/how-to-monitor-websites/)
-   [Come applicare la scalabilità ai siti Web](/en-us/manage/services/web-sites/how-to-scale-websites/)
-   [Pubblicazione con Git](/en-us/develop/php/common-tasks/publishing-with-git/)

Per le esercitazioni complete, visitare la pagina delle [esercitazioni nel Centro per sviluppatori di PHP](/en-us/develop/php/tutorials/).

