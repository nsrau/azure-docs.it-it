<properties 
	pageTitle="Archiviazione del codice del progetto nel controllo del codice sorgente - Servizi mobili di Azure" 
	description="Informazioni su come archiviare il progetto back-end per .NET e pubblicarlo nel computer da un repository Git locale." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/5/2014" 
	ms.author="glenga"/>

<div class="dev-center-tutorial-subselector">
	<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-store-scripts-source-control/"  title="JavaScript backend">Back-end JavaScript</a>
</div>

# Archiviazione del codice del progetto nel controllo del codice sorgente

Questo argomento descrive come usare il controllo del codice sorgente fornito da Servizi mobili di Azure per archiviare il progetto di servizio back-end .NET. Per pubblicare il progetto è sufficiente caricarlo nel servizio mobile di produzione dal repository Git locale. 

Questa esercitazione descrive come:

1. [Abilitare il controllo del codice sorgente nel servizio mobile].
2. [Installare Git e creare il repository locale].
3. [Pubblicazione del progetto mediante Git].

Per completare l'esercitazione, è necessario avere già creato un servizio mobile in base alle procedure descritte nell'esercitazione [Introduzione a Servizi mobili] o [Aggiungere Servizi mobili a un'app esistente].

##<a name="enable-source-control"></a>Abilitare il controllo del codice sorgente nel servizio mobile

[AZURE.INCLUDE [mobile-services-enable-source-control](../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installare Git e creare il repository locale

1. Installare Git nel computer locale. 

	I passaggi necessari per installare Git variano a seconda del sistema operativo. Per informazioni aggiuntive specifiche del sistema operativo sulle distribuzioni e l'installazione, vedere la pagina relativa all'[installazione di Git].

	> [AZURE.NOTE]
	> In alcuni sistemi operativi sono disponibili versioni di Git sia da riga di comando che tramite GUI. Nelle istruzioni fornite in questo articolo si usa la versione da riga di comando.

2. Aprire una riga di comando, ad esempio **GitBash** (Windows) o **Bash** (shell Unix). Nei sistemi operativi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.

3. Dalla riga di comando passare alla directory in cui verranno archiviati gli script, Ad esempio, `cd SourceControl`.

4. Usare il comando seguente per creare una copia locale del nuovo repository Git, sostituendo `<your_git_URL>` con l'URL del repository Git per il servizio mobile:

		git clone <your_git_URL>

5. Quando richiesto, digitare il nome utente e la password impostati quando è stato abilitato il controllo del codice sorgente nel servizio mobile. Dopo avere eseguito l'autenticazione, verrà visualizzata una serie di risposte simili a questa:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Passare alla directory da cui si è eseguito il comando `git clone` e osservare che è stata creata una nuova directory con il nome del servizio mobile. Per un servizio mobile back-end .NET, il repository Git è inizialmente vuoto. 

Una volta creato il repository locale, è possibile pubblicare il progetto di servizio back-end .NET da questo repository.

##<a name="deploy-scripts"></a>Pubblicazione del progetto mediante Git

1. Creare un nuovo progetto di servizio mobile back-end .NET in Visual Studio 2013 o spostare un progetto esistente nel nuovo repository locale.  

	Per un test veloce, scaricare e salvare il progetto di guida introduttiva di Servizi mobili in questa cartella.

2. Rimuovere le cartelle dei pacchetti NuGet, lasciando il file packages.config.

	Servizi mobili ripristinerà automaticamente i pacchetti NuGet in base al file packages.config. È anche possibile definire un file con estensione gitignore per impedire l'aggiunta di directory dei pacchetti. 
 
3. Al prompt dei comandi di Git, digitare il comando seguente per iniziare a monitorare il nuovo file di script:

		$ git add .
	
4. Digitare il comando seguente per eseguire il commit delle modifiche:

		$ git commit -m "adding the .NET backend service project"

5. Digitare il comando seguente per caricare le modifiche nel repository remoto e immettere le proprie credenziali:

		$ git push origin master
	
	Dovrebbe essere visualizzata una serie di comandi per indicare che il progetto è stato distribuito in Servizi mobili, che i pacchetti sono stati aggiunti e che il servizio è stato riavviato.

6. Passare all'URL del servizio mobile back-end .NET. Dovrebbe essere visualizzata la schermata seguente:

	![Mobile Services startup page](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

A questo punto, il progetto di servizio mobile viene gestito nel controllo del codice sorgente ed è possibile pubblicare gli aggiornamenti del servizio tramite il semplice push dal repository locale. Per informazioni sulla modifica del modello di dati in un servizio mobile back-end .NET che usa un database SQL, vedere [Come modificare un modello di dati in un servizio mobile back-end .NET].

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione si è appreso ad archiviare gli script nel controllo del codice sorgente. Per altre informazioni sui servizi mobili, vedere: 

+ [Come modificare un modello di dati in un servizio mobile back-end .NET]
	<br/> Mostra come usare Migrazioni Code First di Entity Framework per apportare modifiche al modello di dati in un database SQL esistente di Azure ed evitare la perdita di dati esistenti. 	

+ [Chiamare un'API personalizzata dal client] 
	<br/> Mostra come creare API personalizzate che possono essere chiamate dal client.

<!-- Anchors. -->
[Abilitare il controllo del codice sorgente nel servizio mobile]: #enable-source-control
[Installare Git e creare il repository locale]: #clone-repo
[Pubblicazione del progetto mediante Git]: #deploy-scripts

<!-- Images. -->

<!-- URLs. -->
[Sito Web di Git]: http://git-scm.com
[Controllo del codice sorgente]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installazione di Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Aggiungere Servizi mobili a un'app esistente]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Chiamare un'API personalizzata dal client]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api/
[Come modificare un modello di dati in un servizio mobile back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations


<!--HONumber=42-->
