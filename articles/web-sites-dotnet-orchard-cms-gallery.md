<properties 
	pageTitle="Creare un sito Web CMS Orchard dalla raccolta in Azure" 
	description="Un'esercitazione che illustra come creare un nuovo sito Web in Azure. L'esercitazione contiene inoltre informazioni sull'avvio e sulla gestione del sito con il portale di gestione." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

# Creare un sito Web CMS Orchard dalla raccolta in Azure

La raccolta rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. Le applicazioni Web create dalla raccolta non richiedono l'installazione di software, oltre al browser usato per connettersi al [portale di gestione di Azure](http://manage.windowsazure.com). Per altre informazioni sulle applicazioni Web nella raccolta, vedere [Windows Web App Gallery](http://www.microsoft.com/web/gallery/categories.aspx).

In questa esercitazione si apprenderà come:

- Creare un nuovo sito dalla raccolta.

- Avviare e gestire il sito dal portale di gestione
 
Verrà creato un sito Web CMS Orchard basato su un modello predefinito. [Orchard](http://www.orchardproject.net/) è un'applicazione CMS basata su .NET open source gratuita che consente di creare siti Web basati sul contenuto personalizzati. CMS Orchard include un framework di estensibilità tramite il quale è possibile [scaricare moduli e temi aggiuntivi](http://gallery.orchardproject.net/) per personalizzare il sito. Nell'illustrazione riportata di seguito è mostrato il sito CMS Orchard che verrà creato.

![Orchard blog][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>Creare un sito Web Orchard dalla raccolta</h2>

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2. Fare clic sull'icona **Nuovo** nella parte inferiore sinistra del portale.
	
	![Create New][1]

3. Fare clic sull'icona **Sito Web** e quindi **Da raccolta**.
	
	![Create From Gallery][2]

4. Individuare l'icona **Orchard CMS** nell'elenco, fare clic su di essa e quindi fare clic sulla freccia per continuare.
	
	![Orchard from list][3]

5. Nella pagina **Configura applicazione** immettere o selezionare i valori per tutti i campi:
	
- Immettere un nome di URL a scelta.	
- Selezionare l'area più vicina agli utenti del sito. In questo modo sarà possibile ottenere prestazioni ottimali.

	![configure your app][4]

6. Fare clic sul segno di spunta nell'angolo inferiore destro della casella per avviare la distribuzione del nuovo sito Web CMS Orchard.

In Azure verranno avviate le operazioni di creazione e distribuzione. Durante la creazione e la distribuzione del sito Web, nella parte inferiore del portale di gestione di Siti Web verrà visualizzato lo stato di queste operazioni. Al termine di tutte le operazioni verrà visualizzato un messaggio che conferma la creazione del sito Web.

<h2>Avvio e gestione del sito Orchard</h2>

1. Fare clic sul nome del nuovo sito nella pagina **Siti Web**, quindi fare clic su **Sfoglia** nella parte inferiore del portale per aprire la pagina iniziale del sito Web.

	![launch dashboard][5]

	![browse button][12]

2. Immettere le informazioni di configurazione richieste da Orchard, quindi fare clic su **Finish Setup** per completare la configurazione e aprire la home page del sito Web.

	![login to Orchard][7]

	Il nuovo sito Orchard avrà un aspetto simile al seguente.  

	![your Orchard site][13]

3. Per altre informazioni su Orchard e su come configurare il nuovo sito, fare riferimento alla [documentazione di Orchard](http://docs.orchardproject.net/).

<h2>Passaggio successivo</h2>
* [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix](/it-it/develop/net/tutorials/website-with-webmatrix/) - Altre informazioni su come modificare un sito Web di Azure in WebMatrix. 
* [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](/it-it/develop/net/tutorials/web-site-with-sql-database/)- Altre informazioni su come creare un nuovo sito Web da Visual Studio.

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png




<!--HONumber=42-->
