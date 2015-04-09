<properties 
	pageTitle="Creare un sito Web Umbraco dalla raccolta in Microsoft Azure" 
	description="Creare un sistema di gestione dei contenuti Umbraco ed effettuare la distribuzione in siti Web di Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="tomfitz"/>

#Creare un sito Web Umbraco dalla raccolta in Microsoft Azure#

Umbraco CMS è un sistema di gestione dei contenuti open source completo che può essere usato per creare svariate applicazioni, dalle più piccole alle più complesse. La raccolta di applicazioni di Siti Web di Azure offre un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. La raccolta consente di creare un sito Umbraco CMS in pochi minuti applicando gli starter kit o integrando il proprio progetto. 

Questo articolo descrive il nuovo portale di anteprima di Azure, che semplifica notevolmente la gestione delle risorse. Il nuovo portale di Azure è progettato per velocizzare il processo di distribuzione del software inserendo in una sola area di lavoro servizi, tecnologie e strumenti per più piattaforme di Microsoft e dei relativi partner. Invece di usare risorse autonome come Siti Web di Azure, progetti di Visual Studio o database, è possibile creare, gestire e analizzare l'intera applicazione come un solo gruppo di risorse. 

In questa esercitazione si apprenderà come:

- Creare un nuovo sito dalla raccolta con il nuovo portale di anteprima di Azure
- Compilare un sito Web con blog usando Umbraco CMS 

##Creare un sito Web dalla raccolta nel portale di Azure

1. Accedere al [portale di gestione di Microsoft Azure](https://portal.azure.com/).

2. Scegliere l'icona **Raccolta di Azure**.
	
	![Choose Web Gallery][01Startboard]
	
3. Nella **Raccolta** selezionare la scheda **Web**, quindi **Umbraco CMS**.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. Per creare un nuovo sito Web Umbraco CMS, fare clic su **Crea**.
	
	![Click Create][03UmbracoCMS]
	
5. Il passaggio successivo consiste nel configurare tutte le risorse associate a Umbraco CMS. In questo caso, le risorse sono un sito Web e un database SQL Server. Selezionare prima **Sito Web** per configurare le impostazioni del sito Web, ad esempio **URL**, **Piano di hosting Web**, **Impostazioni app Web** e **Località** del sito. 
	
	![Configure resources][04AppSettings]
	
6. Ora configurare il database. Selezionare **Database**, quindi scegliere **Crea un nuovo database**. In questo esempio viene creata un'istanza di SQL Server per database su Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Ora che il sito Web e il database sono configurati, è possibile iniziare a implementare l'applicazione facendo clic su **Crea** nella parte inferiore del primo pannello **Umbraco CMS** illustrato nell'immagine precedente.
	
	![Click Create][06UmbracoCMSGroup]
	
Una volta completata la distribuzione, la schermata iniziale del portale mostra che è stato creato il gruppo di risorse per Umbraco CMS, in questo caso **UmbracoCMSgroup**. Nella sezione **Riepilogo** fare clic sul nome del sito Web (in questo caso **umbracocmsgroup**) per visualizzare le proprietà del sito Web. Inoltre, nella sezione **Riepilogo** è possibile selezionare la risorsa del database per visualizzare le proprietà del database associato.
	
![][07UmbracoCMSGroupBlade]

## Avviare e configurare il sito Web Umbraco CMS ##

1. Nel pannello dei dettagli del sito Web fare clic su **Esplora** per andare a il sito (in questo caso,  umbracocmsgroup.azurewebsites.net.)
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Quando si esplora il sito Web, Umbraco CMS avvia l'installazione guidata. Immettere le informazioni richieste e quindi fare clic su **Personalizza**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Immettere i dettagli sulla connessione e l'autenticazione per il database che verranno usati da Umbraco. Selezionare **Microsoft SQL Azure** come tipo di database.  È possibile ottenere la stringa di connessione per il database dalla sezione **Impostazioni sito** del sito Web.
	
	![Configure your database][10ConfigureYourDatabase] 
	
4. Se non si conosce Umbraco CMS, è possibile selezionare uno starter kit per il sito Web. Altrimenti, fare clic su **No grazie, non intendo installare un sito Web iniziale**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Il programma di installazione di Umbraco completerà la configurazione dell'applicazione. Una volta configurata l'applicazione, si verrà reindirizzati al dashboard amministrativo di Umbraco CMS.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Ora si creerà una pagina di testo di esempio da pubblicare. Selezionare **Contenuto**, **Overflow** e infine **TextPage**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Immettere un titolo e il contenuto per la pagina di testo, come mostrato di seguito. Al termine dell'operazione fare clic su **Salva e pubblica**.
	
	![Enter a title and some content][16EnterAName]
	
8. Attendere che la pagina venga pubblicata. Una volta completata la pubblicazione, si riceverà un breve avviso nell'angolo in basso a destra della schermata. Ora è possibile esplorare il nuovo contenuto nel sito Web. 
	
	![Published web site page][17MyPage]
	

La procedura è terminata. In pochi minuti, con Umbraco CMS è stato creato un sito Web con blog. 

##Risorse aggiuntive

[Documentazione su Umbraco](http://our.umbraco.org/documentation)

[Video Tutorial su Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Panoramica del portale di anteprima di Microsoft Azure](http://azure.microsoft.com/overview/preview-portal/)

[Documentazione sul portale di anteprima di Microsoft Azure](http://azure.microsoft.com/documentation/preview-portal/)

[Portale di anteprima di Azure (Channel 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Documentazione su Siti Web di Microsoft Azure](http://azure.microsoft.com/documentation/services/web-sites/)


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

<!--HONumber=49-->