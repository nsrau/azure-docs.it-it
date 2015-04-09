<properties
	pageTitle="Come creare un'app Web - Gestione del servizio di Azure"
	description="Informazioni su come creare un'app Web nel portale di Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

#Come creare un'app Web

In questo argomento viene illustrato come creare un'app Web dalla raccolta oppure mediante il portale di Azure.

Per informazioni su come distribuire i contenuti in un'app Web creata, vedere la sezione **Distribuzione** in [App Web di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non é necessario fornire una carta di credito né impegnarsi in alcun modo.

##<a name="createawebsiteportal"></a>Procedura: Creare un'app Web tramite il portale di Azure

Seguire le istruzioni per creare un'app Web in Azure.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

2. Fare clic sull'icona **Nuovo** nella parte inferiore sinistra del portale.

3. Fare clic sull'icona **Web + Mobile**, quindi sull'icona **App Web**, inserire un valore per l'URL, quindi selezionare **Crea** nell'angolo inferiore destro nel pannello di creazione.

4. Una volta creata l'app Web, viene visualizzato il testo **Distribuzione al gruppo di risorse <nome gruppo di risorse> riuscita**.

5. Nel portale, fare clic sul nome dell'app Web visualizzata nel relativo elenco per aprire il pannello dell'app Web.

6. Nel pannello, sono visualizzate le opzioni per ottenere per ottenere gli strumenti di sviluppo per l'app Web, configurare la pubblicazione dell'app Web oppure la distribuzione da un provider di controllo del codice sorgente come TFS o Git. La pubblicazione con FTP viene configurata per impostazione predefinita per le app Web e il nome host FTP viene visualizzato nella sezione **Elementi essenziali** presente nel pannello dell'app Web. Prima della pubblicazione con FTP o Git, scegliere l'opzione per **reimpostare la pubblicazione del profilo** nel pannello dell'app Web. In questo modo, è possibile eseguire l'autenticazione con l'host FTP o il repository Git durante la distribuzione dei contenuti nell'app Web.

7. Nel pannello **Impostazioni** vengono riportate le impostazioni dell'app Web, ad esempio:

	- Versione di .NET, PHP, Java o Python per l'app Web
	- Modifiche in Visual Studio Online
	- Associazioni SSL
	- Nomi di dominio personalizzati
	- Autenticazione/autorizzazione
	- Diagnostica dell'applicazione e del sito
	- Endpoint di monitoraggio
	- Opzioni di registrazione
	- Impostazioni app per l'ambiente Azure (ad esempio eseguendo l'override di <appSettings> nel file Web.config dell'ambiente di sviluppo)
	- Stringhe di connessione (ad esempio eseguendo l'override di <connectionStrings> nel file Web.config dell'ambiente di sviluppo)
	- Processori script per estensioni file specifiche come *.php

##<a name="howtocreatefromgallery"></a> Procedura: Creare un'app Web dalla raccolta

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a> Procedura: Eliminare un'app Web
Le app Web vengono eliminate utilizzando l'icona **Elimina** disponibile nel portale di Azure. L'icona **Elimina** è presente nella parte superiore del pannello per l'app Web.

##<a name="nextsteps"></a> Passaggi successivi

Per ulteriori informazioni, vedere [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->