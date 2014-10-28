<properties title="How to create a PHP website in Azure Websites" pageTitle="How to create a PHP website in Azure Websites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP website in Azure Websites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="cephalin" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Come creare un sito Web PHP in Siti Web di Azure

In questo articolo viene illustrato come creare un sito Web PHP in [Siti Web di Azure][Siti Web di Azure] usando il [portale di gestione di Azure][portale di gestione di Azure], gli [strumenti da riga di comando di Azure per Mac e Linux][strumenti da riga di comando di Azure per Mac e Linux] oppure i [cmdlet di Azure PowerShell][cmdlet di Azure PowerShell].

In generale, la creazione di un sito Web PHP non è diversa da quella di *qualsiasi* sito Web in Siti Web di Azure. Per impostazione predefinita, PHP è abilitato per tutti i siti Web. Per informazioni sulla configurazione di PHP o su come specificare il proprio runtime PHP personalizzato, vedere [Come configurare PHP in Siti Web di Azure][Come configurare PHP in Siti Web di Azure].

Per ogni opzione descritta di seguito viene illustrato come creare un sito Web in un ambiente host condiviso senza alcun costo, ma con alcune limitazioni per quanto riguarda l'uso della CPU e della larghezza di banda. Per altre informazioni, vedere la [Panoramica dei prezzi dei Siti Web di Azure][Panoramica dei prezzi dei Siti Web di Azure]. Per informazioni su come aggiornare e scalare il sito Web, vedere [Come applicare la scalabilità ai siti Web][Come applicare la scalabilità ai siti Web].

## Sommario

-   [Creazione di un sito Web mediante il portale di gestione di Azure][Creazione di un sito Web mediante il portale di gestione di Azure]
-   [Creazione di un sito Web con gli strumenti da riga di comando per Mac e Linux][Creazione di un sito Web con gli strumenti da riga di comando per Mac e Linux]
-   [Creazione di un sito Web con i cmdlet di Azure PowerShell][Creazione di un sito Web con i cmdlet di Azure PowerShell]

## <a name="portal"></a>Creare un sito Web PHP mediante il portale di gestione di Azure

Per la creazione di un sito Web nel portale di gestione di Azure sono disponibili tre opzioni: **Quick Create**, **Create with Database** e **From Gallery**. Nelle istruzioni seguenti viene utilizzata l'opzione **Quick Create**. Per informazioni sulle altre due opzioni, vedere [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git][Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git] e [Creazione di un sito Web WordPress dalla raccolta in Azure][Creazione di un sito Web WordPress dalla raccolta in Azure].

Per creare un sito Web PHP mediante il portale di gestione di Azure, eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **Nuovo** nella parte inferiore della pagina, quindi selezionare **Calcolo**, **Sito Web** e **Creazione rapida**. Specificare un **URL** per il sito Web e selezionare la **Regione** per il sito Web. Infine, fare clic su **Crea sito Web**.

    ![Selezione di Quick Create][Selezione di Quick Create]

## <a name="XplatTools"></a>Creare un sito Web PHP con gli strumenti da riga di comando per Mac e Linux

Per creare un sito Web PHP con gli strumenti da riga di comando per Mac e Linux, eseguire le operazioni seguenti:

1.  Installare gli strumenti da riga di comando di Azure seguendo le istruzioni riportate in [Come installare gli strumenti da riga di comando di Azure per Mac e Linux][Come installare gli strumenti da riga di comando di Azure per Mac e Linux].

2.  Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Come scaricare e importare impostazioni di pubblicazione][Come scaricare e importare impostazioni di pubblicazione].

3.  Eseguire il comando seguente da un prompt dei comandi:

        azure site create MySiteName

L'URL del nuovo sito Web sarà `http://MySiteName.azurewebsites.net`.

È possibile eseguire il comando `azure site create` con una qualsiasi delle opzioni seguenti:

-   `--location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creato il sito Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
-   `--hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per il sito Web.
-   L'opzione `--git` consente di usare Git per eseguire la pubblicazione nel sito Web creando repository Git sia nella directory delle applicazioni locale che nel data center del sito Web. Si noti che qualora la cartella locale sia già un repository Git, il comando aggiungerà a quello esistente un nuovo repository remoto che punterà al repository del data center del sito Web.

Per informazioni sulle altre opzioni, vedere [Come creare e gestire un sito Web di Azure][Come creare e gestire un sito Web di Azure].

## <a name="PowerShell"></a>Creare un sito Web PHP con i cmdlet di Azure PowerShell

Per creare un sito Web PHP con i cmdlet di Azure PowerShell, eseguire le operazioni seguenti:

1.  Installare i cmdlet di Azure PowerShell seguendo le istruzioni riportate in [Guida introduttiva ad Azure PowerShell][Guida introduttiva ad Azure PowerShell].

2.  Scaricare e importare il file delle impostazioni di pubblicazione seguendo le istruzioni riportate in [Procedura: Importare le impostazioni di pubblicazione][Procedura: Importare le impostazioni di pubblicazione].

3.  Aprire un prompt dei comandi PowerShell ed eseguire il comando seguente:

        New-AzureWebSite MySiteName

L'URL del nuovo sito Web sarà `http://MySiteName.azurewebsites.net`.

È possibile eseguire il comando `New-AzureWebSite` con una qualsiasi delle opzioni seguenti:

-   `-Location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creato il sito Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
-   `-Hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per il sito Web.
-   L'opzione `-Git` consente di usare Git per eseguire la pubblicazione nel sito Web creando repository Git sia nella directory delle applicazioni locale che nel data center del sito Web. Si noti che qualora la cartella locale sia già un repository Git, il comando aggiungerà a quello esistente un nuovo repository remoto che punterà al repository del data center del sito Web.

Per informazioni sulle altre opzioni, vedere [Procedura: Creare e gestire un sito Web di Azure][Procedura: Creare e gestire un sito Web di Azure].

## <a name="NextSteps"></a>Passaggi successivi

Dopo aver creato un sito Web PHP in Siti Web di Azure, è possibile eseguirvi operazioni di gestione, configurazione, monitoraggio e distribuzione, ed è inoltre possibile ridimensionarlo. Per altre informazioni, vedere i collegamenti seguenti:

-   [Come configurare i siti Web][Come configurare i siti Web]
-   [Come configurare PHP in Siti Web di Azure][Come configurare PHP in Siti Web di Azure]
-   [Come gestire i siti Web][Come gestire i siti Web]
-   [Come monitorare i siti Web][Come monitorare i siti Web]
-   [Come applicare la scalabilità ai siti Web][Come applicare la scalabilità ai siti Web]
-   [Pubblicazione con Git][Pubblicazione con Git]

Per le esercitazioni complete, visitare la pagina delle [esercitazioni nel Centro per sviluppatori di PHP][esercitazioni nel Centro per sviluppatori di PHP].

  [Siti Web di Azure]: /it-it/manage/services/web-sites/
  [portale di gestione di Azure]: http://manage.windowsazure.com/
  [strumenti da riga di comando di Azure per Mac e Linux]: /it-it/develop/php/how-to-guides/command-line-tools/
  [cmdlet di Azure PowerShell]: /it-it/develop/php/how-to-guides/powershell-cmdlets/
  [Come configurare PHP in Siti Web di Azure]: /it-it/develop/php/common-tasks/configure-php-web-site/
  [Panoramica dei prezzi dei Siti Web di Azure]: http://www.windowsazure.com/it-it/pricing/details/#header-1
  [Come applicare la scalabilità ai siti Web]: /it-it/manage/services/web-sites/how-to-scale-websites/
  [Creazione di un sito Web mediante il portale di gestione di Azure]: #portal
  [Creazione di un sito Web con gli strumenti da riga di comando per Mac e Linux]: #XplatTools
  [Creazione di un sito Web con i cmdlet di Azure PowerShell]: #PowerShell
  [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git]: /it-it/develop/php/tutorials/website-w-mysql-and-git/
  [Creazione di un sito Web WordPress dalla raccolta in Azure]: /it-it/develop/php/tutorials/website-from-gallery/
  [Selezione di Quick Create]: ./media/web-sites-php-create-web-sites/select-quickcreate-website.png
  [Come installare gli strumenti da riga di comando di Azure per Mac e Linux]: /it-it/develop/php/how-to-guides/command-line-tools/#Download
  [Come scaricare e importare impostazioni di pubblicazione]: /it-it/develop/php/how-to-guides/command-line-tools/#Account
  [Come creare e gestire un sito Web di Azure]: /it-it/develop/php/how-to-guides/command-line-tools/#WebSites
  [Guida introduttiva ad Azure PowerShell]: /it-it/develop/php/how-to-guides/powershell-cmdlets/#GetStarted
  [Procedura: Importare le impostazioni di pubblicazione]: /it-it/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [Procedura: Creare e gestire un sito Web di Azure]: /it-it/develop/php/how-to-guides/powershell-cmdlets/#WebSite
  [Come configurare i siti Web]: /it-it/manage/services/web-sites/how-to-configure-websites/
  [Come gestire i siti Web]: /it-it/manage/services/web-sites/how-to-manage-websites/
  [Come monitorare i siti Web]: /it-it/manage/services/web-sites/how-to-monitor-websites/
  [Pubblicazione con Git]: /it-it/develop/php/common-tasks/publishing-with-git/
  [esercitazioni nel Centro per sviluppatori di PHP]: /it-it/develop/php/tutorials/
