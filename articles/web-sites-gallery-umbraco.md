<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Create an Umbraco website from the gallery in Microsoft Azure" description="required" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# Creare un sito Web Umbraco dalla raccolta in Microsoft Azure

Umbraco CMS è un sistema di gestione dei contenuti open source completo che può essere usato per creare svariate applicazioni, dalle più piccole alle più complesse. La raccolta di applicazioni di Siti Web di Azure offre un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. La raccolta consente di creare un sito Umbraco CMS in pochi minuti applicando gli starter kit o integrando il proprio progetto.

Questo articolo descrive il nuovo portale di anteprima di Azure, che semplifica notevolmente la gestione delle risorse. Il nuovo portale di Azure è progettato per velocizzare il processo di distribuzione del software inserendo in una sola area di lavoro servizi, tecnologie e strumenti per più piattaforme di Microsoft e dei relativi partner. Invece di usare risorse autonome come Siti Web di Azure, progetti di Visual Studio o database, è possibile creare, gestire e analizzare l'intera applicazione come un solo gruppo di risorse.

In questa esercitazione si apprenderà come:

-   Creare un nuovo sito dalla raccolta con il nuovo portale di anteprima di Azure
-   Compilare un sito Web con blog usando Umbraco CMS

## Creare un sito Web dalla raccolta nel portale di Azure

1.  Accedere al [portale di gestione di Microsoft Azure][portale di gestione di Microsoft Azure].

2.  Scegliere l'icona **Raccolta di Azure**.

    ![Choose Web Gallery][Choose Web Gallery]

3.  Nella **Raccolta** selezionare la scheda **Web** e quindi selezionare **Umbraco CMS**.

    ![Select Umbraco in the Web Gallery][Select Umbraco in the Web Gallery]

4.  Per creare un nuovo sito Web Umbraco CMS, fare clic su **Crea**.

    ![Click Create][Click Create]

5.  Il passaggio successivo consiste nel configurare tutte le risorse associate a Umbraco CMS. In questo caso, le risorse sono un sito Web e un database SQL Server. Selezionare prima **Sito Web** per configurare le impostazioni del sito Web, ad esempio **URL**, **Piano di hosting Web**, **Impostazioni app Web** e **Località** del sito.

    ![Configure resources][Configure resources]

6.  Ora configurare il database. Selezionare **Database** e quindi scegliere **Crea un nuovo database**. In questo esempio viene creata un'istanza di SQL Server per database su Azure.

    ![Create a SQL Server on Azure][Create a SQL Server on Azure]

7.  Ora che il sito Web e il database sono configurati, è possibile iniziare a implementare l'applicazione facendo clic su **Crea** nella parte inferiore del primo pannello **Umbraco CMS** illustrato nell'immagine precedente.

    ![Click Create][1]

Una volta completata la distribuzione, la schermata iniziale del portale mostra che è stato creato il gruppo di risorse per Umbraco CMS, in questo caso **UmbracoCMSgroup**. Nella sezione **Riepilogo** fare clic sul nome del sito Web (in questo caso **umbracocmsgroup**) per visualizzare le proprietà del sito Web. Anche nella sezione **Riepilogo**, è possibile selezionare la risorsa del database per visualizzare le proprietà del database associato.

![][0]

## Avviare e configurare il sito Web Umbraco CMS

1.  Nel pannello dei dettagli del sito Web fare clic su **Esplora** per esplorare il sito (in questo caso, umbracocmsgroup.azurewebsites.net.)

    ![Browse to your site][Browse to your site]

2.  Quando si esplora il sito Web, Umbraco CMS avvia l'installazione guidata. Immettere le informazioni richieste e quindi fare clic su **Customize**.

    ![Install Umbraco wizard][Install Umbraco wizard]

3.  Immettere i dettagli sulla connessione e l'autenticazione per il database che verranno usati da Umbraco. Selezionare **Microsoft SQL Azure** come tipo di database. È possibile ottenere la stringa di connessione per il database dalla sezione **Impostazioni sito** del sito Web.

    ![Configure your database][Configure your database]

4.  Se non si conosce Umbraco CMS, è possibile selezionare uno starter kit per il sito Web. Altrimenti, fare clic su **No thanks, I do not want to install a starter website**.

    ![Install a starter website][Install a starter website]

5.  Il programma di installazione di Umbraco completerà la configurazione dell'applicazione. Una volta configurata l'applicazione, si verrà reindirizzati al dashboard amministrativo di Umbraco CMS.

    ![Umbraco CMS dashboard][Umbraco CMS dashboard]

6.  Ora si creerà una pagina di testo di esempio da pubblicare. Selezionare **Content**, **Overflow** e infine **TextPage**.

    ![Create a text page][Create a text page]

7.  Immettere un titolo e il contenuto per la pagina di testo, come mostrato di seguito. Al termine dell'operazione fare clic su **Save and publish**.

    ![Enter a title and some content][Enter a title and some content]

8.  Attendere che la pagina venga pubblicata. Una volta completata la pubblicazione, si riceverà un breve avviso nell'angolo in basso a destra della schermata. Ora è possibile esplorare il nuovo contenuto nel sito Web.

    ![Published web site page][Published web site page]

La procedura è terminata. In pochi minuti, con Umbraco CMS è stato creato un sito Web con blog.

## Risorse aggiuntive

[Documentazione su Umbraco][Documentazione su Umbraco]

[Esercitazioni video di Umbraco][Esercitazioni video di Umbraco]

[Panoramica del portale di anteprima di Microsoft Azure][Panoramica del portale di anteprima di Microsoft Azure]

[Documentazione sul portale di anteprima di Microsoft Azure][Documentazione sul portale di anteprima di Microsoft Azure]

[Portale di anteprima di Azure (Channel 9)][Portale di anteprima di Azure (Channel 9)]

[Documentazione su Siti Web di Microsoft Azure][Documentazione su Siti Web di Microsoft Azure]

<!-- IMAGES -->

  [portale di gestione di Microsoft Azure]: https://portal.azure.com/
  [Choose Web Gallery]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
  [Select Umbraco in the Web Gallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
  [Click Create]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
  [Configure resources]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
  [Create a SQL Server on Azure]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
  [1]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
  [0]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
  [Browse to your site]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
  [Install Umbraco wizard]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
  [Configure your database]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
  [Install a starter website]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
  [Umbraco CMS dashboard]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
  [Create a text page]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
  [Enter a title and some content]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
  [Published web site page]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
  [Documentazione su Umbraco]: http://our.umbraco.org/documentation
  [Esercitazioni video di Umbraco]: https://umbraco.com/help-and-support/video-tutorials.aspx
  [Panoramica del portale di anteprima di Microsoft Azure]: http://azure.microsoft.com/it-it/overview/preview-portal/
  [Documentazione sul portale di anteprima di Microsoft Azure]: http://azure.microsoft.com/it-it/documentation/preview-portal/
  [Portale di anteprima di Azure (Channel 9)]: http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal
  [Documentazione su Siti Web di Microsoft Azure]: http://azure.microsoft.com/it-it/documentation/services/web-sites/
