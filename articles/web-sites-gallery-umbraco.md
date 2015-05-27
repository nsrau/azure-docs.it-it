<properties 
	pageTitle="Creare un'app web Umbraco da Marketplace in Microsoft Azure" 
	description="Creare un sistema di gestione dei contenuti Umbraco ed effettuare la distribuzione in siti Web di Azure." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>

#Creare un'app web Umbraco da Marketplace in Microsoft Azure#

Umbraco CMS è un sistema di gestione dei contenuti open source completo che può essere usato per creare svariate applicazioni, dalle più piccole alle più complesse. Azure Marketplace rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. La raccolta consente di creare un sito Umbraco CMS in pochi minuti applicando gli starter kit o integrando il proprio progetto.

Questo articolo descrive il nuovo portale di anteprima di Azure, che semplifica notevolmente la gestione delle risorse. Il nuovo portale di Azure è progettato per velocizzare il processo di distribuzione del software inserendo in una sola area di lavoro servizi, tecnologie e strumenti per più piattaforme di Microsoft e dei relativi partner. Invece di usare risorse autonome come Siti App Web di [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), progetti di Visual Studio o database, è possibile creare, gestire e analizzare l'intera applicazione come un solo gruppo di risorse.

In questa esercitazione si apprenderà come:

- Come creare una nuova applicazione web tramite Markeplace tramite il portale di anteprima di Azure
- Compilare un sito Web con blog usando Umbraco CMS 

##Creare un'applicazione web da Marketplace nel portale di anteprima di Azure

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com/).

2. Scegliere il **Marketplace** icona.
	
	![Choose Web Gallery][01Startboard]
	
3. Nel **Marketplace** , selezionare il **applicazioni Web** scheda e quindi selezionare **Umbraco CMS**.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. Per creare un nuovo sito Web Umbraco CMS, fare clic su **Crea**.
	
	![Click Create][03UmbracoCMS]
	
5. Il passaggio successivo consiste nel configurare tutte le risorse associate a Umbraco CMS. In questo caso, le risorse sono un sito Web e un database SQL Server. Selezionare innanzitutto **Web app** per configurare le impostazioni dell'applicazione web, ad esempio il **URL**, **pianificare servizio App**, **Impostazioni applicazione Web**, e **indirizzo**.
	
	![Configure resources][04AppSettings]
	
6. Ora configurare il database. Selezionare **Database**, quindi scegliere **Server**. In questo esempio viene creata un'istanza di SQL Server per database su Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Ora che il sito Web e il database sono configurati, è possibile iniziare a implementare l'applicazione facendo clic su **Crea** nella parte inferiore del primo pannello **Umbraco CMS** illustrato nell'immagine precedente.
	
	![Click Create][06UmbracoCMSGroup]
	
Al termine della distribuzione, il portale verrà visualizzato il pannello del gruppo di risorse dell'applicazione web Umbraco CMS. Nel **riepilogo** sezione fare clic sul nome di applicazione web per visualizzare le proprietà dell'applicazione web. Anche nella sezione **Riepilogo**, è possibile selezionare la risorsa del database per visualizzare le proprietà del database associato.
	
![][07UmbracoCMSGroupBlade]

## Avviare e configurare il sito Web Umbraco CMS ##

1. Nel pannello relativo ai dettagli dell'applicazione Web, fare clic su **Sfoglia** per esplorare l'applicazione web.
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Quando si esplora il sito Web, Umbraco CMS avvia l'installazione guidata. Immettere le informazioni richieste e quindi fare clic su **Customize**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Immettere i dettagli sulla connessione e l'autenticazione per il database che verranno usati da Umbraco. Selezionare **Microsoft SQL Azure** come tipo di database. È possibile ottenere la stringa di connessione per il database dalla sezione **Impostazioni sito** del sito Web.
	
	![Configure your database][10ConfigureYourDatabase]
	
4. Se non si conosce Umbraco CMS, è possibile selezionare uno starter kit per il sito Web. Altrimenti, fare clic su **No thanks, I do not want to install a starter website**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Il programma di installazione di Umbraco completerà la configurazione dell'applicazione. Una volta configurata l'applicazione, si verrà reindirizzati al dashboard amministrativo di Umbraco CMS.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Ora si creerà una pagina di testo di esempio da pubblicare. Selezionare **Content**, **Overflow** e infine **TextPage**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Immettere un titolo e il contenuto per la pagina di testo, come mostrato di seguito. Al termine dell'operazione fare clic su **Save and publish**.
	
	![Enter a title and some content][16EnterAName]
	
8. Attendere che la pagina venga pubblicata. Una volta completata la pubblicazione, si riceverà un breve avviso nell'angolo in basso a destra della schermata. Ora è possibile esplorare il nuovo contenuto nel sito Web.
	
	![Published web site page][17MyPage]
	

La procedura è terminata. In pochi minuti, con Umbraco CMS è stato creato un sito Web con blog.

##Risorse aggiuntive

[Documentazione su Umbraco](http://our.umbraco.org/documentation)

[Esercitazioni video di Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Documentazione di portale di Azure](preview-portal.md)

[Portale di Azure (Channel 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Documentazione relativa alle app Web di Azure App Service](/documentation/services/websites/)

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente con il portale di anteprima, vedere: [riferimento per lo spostamento nel portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG

<!--HONumber=54-->