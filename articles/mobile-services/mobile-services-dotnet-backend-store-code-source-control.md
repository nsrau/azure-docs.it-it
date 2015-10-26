<properties
	pageTitle="Archiviare il codice del progetto di back-end .NET nel controllo del codice sorgente | Servizi mobili di Azure"
	description="Informazioni su come archiviare il progetto back-end per .NET e pubblicarlo nel computer da un repository Git locale."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="glenga"/>

# Archiviare il codice del progetto nel controllo del codice sorgente dei Servizi mobili

> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-store-code-source-control.md)
- [Javascript backend](mobile-services-store-scripts-source-control.md)

Questo argomento descrive come usare il controllo del codice sorgente fornito da Servizi mobili di Azure per archiviare il progetto di servizio back-end .NET. Per pubblicare il progetto è sufficiente caricarlo nel servizio mobile di produzione dal repository Git locale.

Per completare l'esercitazione, è necessario avere già creato un servizio mobile in base alle procedure descritte nell'esercitazione [Introduzione a Servizi mobili].

##<a name="enable-source-control"></a>Abilitare il controllo del codice sorgente nel servizio mobile

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installare Git e creare il repository locale

1. Installare Git nel computer locale.

	I passaggi necessari per installare Git variano a seconda del sistema operativo. Vedere la sezione [Installazione di Git] per indicazioni specifiche del sistema operativo relative a distribuzioni e installazione.

	> [AZURE.NOTE]
	> In alcuni sistemi operativi sono disponibili versioni di Git sia da riga di comando che tramite GUI. Nelle istruzioni fornite in questo articolo si utilizza la versione da riga di comando.

2. Aprire una riga di comando, ad esempio **GitBash** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.

3. Dalla riga di comando passare alla directory in cui verranno archiviati gli script, ad esempio `cd SourceControl`.

4. Usare il seguente comando per creare una copia locale del nuovo repository Git, sostituendo `<your_git_URL>` con l'URL del repository Git per il servizio mobile:

		git clone <your_git_URL>

5. Quando richiesto, digitare il nome utente e la password impostati quando è stato abilitato il controllo del codice sorgente nel servizio mobile. Dopo avere eseguito l'autenticazione, verrà visualizzata una serie di risposte simili a questa:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Passare alla directory da cui si è eseguito il comando `git clone` e osservare che è stata creata una nuova directory con il nome del servizio mobile. Per un servizio mobile back-end .NET, il repository Git è inizialmente vuoto.

Una volta creato il repository locale, è possibile pubblicare il progetto di servizio back-end .NET da questo repository.

##<a name="deploy-scripts"></a>Pubblicare il progetto mediante Git

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

	![Pagina di avvio di servizi mobili](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

A questo punto, il progetto di servizio mobile viene gestito nel controllo del codice sorgente ed è possibile pubblicare gli aggiornamenti del servizio tramite il semplice push dal repository locale. Per informazioni sulla modifica del modello di dati in un servizio mobile back-end .NET che usa un database SQL, vedere [Come modificare un modello di dati in un servizio mobile back-end .NET].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Git website]: http://git-scm.com
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installazione di Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Come modificare un modello di dati in un servizio mobile back-end .NET]: mobile-services-dotnet-backend-how-to-use-code-first-migrations.md

<!---HONumber=Oct15_HO3-->