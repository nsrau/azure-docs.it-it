<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Web Site from Gallery" pageTitle="Create an Orchard CMS web site from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new web site in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS web site from the gallery in Azure" authors="" solutions="" manager="" editor="" />

Creazione di un sito Web CMS Orchard dalla raccolta in Azure
============================================================

La raccolta rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. Le applicazioni Web create dalla raccolta non richiedono l'installazione di software, oltre al browser utilizzato per connettersi al [portale di gestione di Azure](http://manage.windowsazure.com). Per ulteriori informazioni sulle applicazioni Web nella raccolta, vedere [Raccolta di app Web Windows](http://www.microsoft.com/web/gallery/categories.aspx).

In questa esercitazione si apprenderà come:

-   Creare un nuovo sito dalla raccolta.

-   Avviare e gestire il sito dal portale di gestione

Verrà creato un sito Web CMS Orchard basato su un modello predefinito. [Orchard](http://www.orchardproject.net/) è un'applicazione CMS basata su .NET open source gratuita che consente di creare siti Web basati sul contenuto personalizzati. CMS Orchard include un framework di estensibilità tramite il quale è possibile [scaricare moduli e temi aggiuntivi](http://gallery.orchardproject.net/) per personalizzare il sito. Nell'illustrazione riportata di seguito è mostrato il sito CMS Orchard che verrà creato.

![Blog Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Creazione di un sito Web Orchard dalla raccolta
-----------------------------------------------

1.  Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2.  Fare clic sull'icona **New** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png)

3.  Fare clic sull'icona **Web Site** e quindi su **From Gallery**.

    ![Creazione da raccolta](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png)

4.  Individuare l'icona **Orchard CMS** nell'elenco, fare clic su di essa e quindi fare clic sulla freccia per continuare.

    ![Orchard nell'elenco](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png)

5.  Nella pagina **Configure Your App** immettere o selezionare i valori per tutti i campi:

-   Immettere un nome di URL a scelta.
-   Selezionare l'area più vicina agli utenti del sito. In questo modo sarà possibile ottenere prestazioni ottimali.

    ![configurazione dell'app](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png)

1.  Fare clic sul segno di spunta nell'angolo inferiore destro della casella per avviare la distribuzione del nuovo sito Web CMS Orchard.

In Azure verranno avviate le operazioni di creazione e distribuzione. Durante la creazione e la distribuzione del sito Web, nella parte inferiore del portale di gestione Web Sites verrà visualizzato lo stato di queste operazioni. Al termine di tutte le operazioni, verrà visualizzato un messaggio che conferma la creazione del sito Web.

Avvio e gestione del sito Orchard
---------------------------------

1.  Fare clic sul nome del nuovo sito nella pagina **Web Sites**, quindi fare clic su **Browse** nella parte inferiore del portale per aprire la pagina iniziale del sito Web.

    ![avvio del dashboard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png)

    ![pulsante Browse](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png)

2.  Immettere le informazioni di configurazione richieste da Orchard e quindi fare clic su **Finish Setup** per completare la configurazione e aprire la home page del sito Web.

    ![accesso a Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png)

    Il nuovo sito Orchard avrà un aspetto simile al seguente.

    ![sito Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png)

3.  Per ulteriori informazioni su Orchard e su come configurare il nuovo sito, fare riferimento alla [documentazione di Orchard](http://docs.orchardproject.net/).

Passaggi successiviPassaggio successivo
---------------------------------------

-   [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix](/en-us/develop/net/tutorials/website-with-webmatrix/) Ulteriori informazioni su come modificare un sito Web di Azure in WebMatrix.
-   [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/) Ulteriori informazioni su come creare un nuovo sito Web da Visual Studio.

