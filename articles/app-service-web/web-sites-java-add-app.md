<properties 
	pageTitle="Distribuire l'applicazione di esempio nelle app Web di Servizio app di Azure" 
	description="In questa esercitazione viene illustrato come aggiungere una pagina o applicazione per l'istanza di Azure applicazione servizio Web App è già configurato per l'utilizzo di Java." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="robmcm"/>

# Distribuire l'applicazione di esempio nelle app Web di Servizio app di Azure

Una volta inizializzata app web Java in [servizio App Azure][] come descritto in [creare un'applicazione web Java nel servizio di Azure App](web-sites-java-get-started.md), è possibile caricare l'applicazione inserendo il file WAR nel **webapps** cartella.

Il percorso della cartella **webapps** varia a seconda della configurazione del sito Web.

- Se si configura il sito Web usando la raccolta di applicazioni di Azure, il percorso della cartella **webapps** sarà nel formato **d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps**, dove **application\_server** è il nome del server applicazioni usato per il sito Web. 
- Se si configura il sito Web usando l'interfaccia utente di configurazione di Azure, il percorso della cartella **webapps** sarà nel formato **d:\\home\\site\\wwwroot\\webapps**. 

Si noti che è possibile usare il controllo del codice sorgente per caricare l'applicazione o le pagine Web, anche negli scenari di integrazione continuata. Istruzioni per l'utilizzo del controllo del codice sorgente con l'applicazione web sono disponibili all'indirizzo [la distribuzione continua utilizzando GIT nel servizio di Azure App](web-sites-publish-source-control.md). Per caricare l'applicazione o le pagine Web è anche possibile usare FTP.

Dopo avere caricato il file WAR nella cartella **webapps**, il server applicazioni Tomcat ne rileverà l'aggiunta e lo caricherà automaticamente. Si noti che se vengono copiati file (eccetto i file WAR) nella directory ROOT, è necessario riavviare il server applicazioni per usarli. La funzionalità di caricamento automatico per i siti Web Java Tomcat in esecuzione in Azure si basa su un nuovo file WAR aggiunto o su nuovi file o nuove directory create nella cartella **webapps**.

## Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[servizio App Azure]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=AcomDC_0309_2016-->