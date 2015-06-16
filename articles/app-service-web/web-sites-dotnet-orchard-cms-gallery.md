<properties 
	pageTitle="Creare un'app Web CMS Orchard da Azure Marketplace" 
	description="Un'esercitazione che illustra come creare un nuovo sito Web in Azure. Inoltre, informazioni su come avviare e gestire applicazioni web tramite il portale di Azure." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>

# Creare un'app Web CMS Orchard da Azure Marketplace

## Panoramica

La raccolta rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. Le applicazioni Web create dalla raccolta non richiedono l'installazione di software, oltre al browser utilizzato per connettersi al [Portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Per ulteriori informazioni sulle applicazioni web nel mercato, vedere [Azure Marketplace](/marketplace/).

In questa esercitazione si apprenderà come:

- Come creare una nuova applicazione web da Marketplace

- Come avviare e gestire l'applicazione web dal portale di Azure
 
Verrà creato un sito Web CMS Orchard basato su un modello predefinito. [Orchard](http://www.orchardproject.net/) è un'applicazione CMS basata su .NET open source gratuita che consente di creare siti Web basati sul contenuto personalizzati. CMS Orchard include un framework di estensibilità tramite il quale è possibile [scaricare moduli e temi aggiuntivi](http://gallery.orchardproject.net/) per personalizzare il sito. La figura seguente mostra l'applicazione web CMS Orchard in [servizio App Azure](http://go.microsoft.com/fwlink/?LinkId=529714) che verrà creato.

![Blog Orchard][13]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un'applicazione web Orchard da Marketplace

1. Accedere al [portale di anteprima di Azure](http://portal.azure.com).

2. Selezionare **NUOVO** > **Web**> **Azure Marketplace**.
	
	![Creazione di un nuovo sito][1]

3. Fare clic su **App Web** > **Orchard CMS**. Nel blade successivo, fare clic su **Crea**.
	
	![Creare da Marketplace][2]

4. Configurare l'URL dell'applicazione web, piano servizio App, gruppo di risorse e percorso. Al termine dell'operazione, scegliere **Crea**.
	
	![Configurare l'app][3]

	Una volta creata l'applicazione web, il **notifiche** pulsante verrà visualizzato un verde "SUCCESS" e verrà visualizzato il blade dell'applicazione web.

## Avvio e gestione del sito Orchard

1. Nel blade dell'applicazione web, fare clic su **Sfoglia** per aprire la pagina iniziale dell'applicazione web.

	![pulsante Browse][12]

2. Immettere le informazioni di configurazione richieste da Orchard e quindi fare clic su **Finish Setup** per completare la configurazione e aprire la home page del sito Web.

	![accesso a Orchard][7]

	Il nuovo sito Orchard avrà un aspetto simile al seguente.

	![l'applicazione web Orchard][13]

3. Per ulteriori informazioni su Orchard e su come configurare il nuovo sito, fare riferimento alla [documentazione di Orchard](http://docs.orchardproject.net/).

## Passaggio successivo

* [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md) Ulteriori informazioni su come modificare un sito Web di Azure in WebMatrix. 
* [Creare un'applicazione MVC ASP.NET con autenticazione e database SQL e distribuire al servizio di Azure App](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)-informazioni su come creare una nuova applicazione web nel servizio di applicazione Azure da Visual Studio.

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il portale di anteprima, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png



<!--HONumber=54--> 