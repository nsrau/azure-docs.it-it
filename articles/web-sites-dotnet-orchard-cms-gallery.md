<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Website from Gallery" pageTitle="Create an Orchard CMS website from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Creare un sito Web CMS Orchard dalla raccolta in Azure

La raccolta rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. Le applicazioni Web create dalla raccolta non richiedono l'installazione di software, oltre al browser utilizzato per connettersi al [portale di gestione di Azure][portale di gestione di Azure]. Per altre informazioni sulle applicazioni Web nella raccolta, vedere [Raccolta di app Web Windows][Raccolta di app Web Windows].

In questa esercitazione si apprenderà come:

-   Creare un nuovo sito dalla raccolta.

-   Avviare e gestire il sito dal portale di gestione

Verrà creato un sito Web CMS Orchard basato su un modello predefinito. [Orchard][Orchard] è un'applicazione CMS basata su .NET open source gratuita che consente di creare siti Web basati sul contenuto personalizzati. CMS Orchard include un framework di estensibilità tramite il quale è possibile [scaricare moduli e temi aggiuntivi][scaricare moduli e temi aggiuntivi] per personalizzare il sito. Nell'illustrazione riportata di seguito è mostrato il sito CMS Orchard che verrà creato.

![Blog Orchard][Blog Orchard]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Creare un sito Web Orchard dalla raccolta

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic sull'icona **Nuovo** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito][Creazione di un nuovo sito]

3.  Fare clic sull'icona **Sito Web** e quindi **Da raccolta**.

    ![Creazione da raccolta][Creazione da raccolta]

4.  Individuare l'icona **Orchard CMS** nell'elenco, fare clic su di essa e quindi fare clic sulla freccia per continuare.

    ![Orchard nell'elenco][Orchard nell'elenco]

5.  Nella pagina **Configure Your App** immettere o selezionare i valori per tutti i campi:

-   Immettere un nome di URL a scelta.
-   Selezionare l'area più vicina agli utenti del sito. In questo modo sarà possibile ottenere prestazioni ottimali.

    ![configurazione dell'app][configurazione dell'app]

1.  Fare clic sul segno di spunta nell'angolo inferiore destro della casella per avviare la distribuzione del nuovo sito Web CMS Orchard.

In Azure verranno avviate le operazioni di creazione e distribuzione. Durante la creazione e la distribuzione del sito Web, nella parte inferiore del portale di gestione di Siti Web verrà visualizzato lo stato di queste operazioni. Al termine di tutte le operazioni verrà visualizzato un messaggio che conferma la creazione del sito Web.

## Avvio e gestione del sito Orchard

1.  Fare clic sul nome del nuovo sito nella pagina **Siti Web**, quindi fare clic su **Sfoglia** nella parte inferiore del portale per aprire la pagina iniziale del sito Web.

    ![avvio del dashboard][avvio del dashboard]

    ![pulsante Browse][pulsante Browse]

2.  Immettere le informazioni di configurazione richieste da Orchard e quindi fare clic su **Finish Setup** per completare la configurazione e aprire la home page del sito Web.

    ![accesso a Orchard][accesso a Orchard]

    Il nuovo sito Orchard avrà un aspetto simile al seguente.

    ![sito Orchard][Blog Orchard]

3.  Per altre informazioni su Orchard e su come configurare il nuovo sito, fare riferimento alla [documentazione di Orchard][documentazione di Orchard].

## <span class="short-header">Passaggi successivi</span>Passaggio successivo

-   [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix][Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix] Altre informazioni su come modificare un sito Web di Azure in WebMatrix.
-   [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure][Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure] Altre informazioni su come creare un nuovo sito Web da Visual Studio.

  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Raccolta di app Web Windows]: http://www.microsoft.com/web/gallery/categories.aspx
  [Orchard]: http://www.orchardproject.net/
  [scaricare moduli e temi aggiuntivi]: http://gallery.orchardproject.net/
  [Blog Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Creazione di un nuovo sito]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
  [Creazione da raccolta]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
  [Orchard nell'elenco]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
  [configurazione dell'app]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
  [avvio del dashboard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
  [pulsante Browse]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
  [accesso a Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
  [documentazione di Orchard]: http://docs.orchardproject.net/
  [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix]: /it-it/develop/net/tutorials/website-with-webmatrix/
  [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]: /it-it/develop/net/tutorials/web-site-with-sql-database/
