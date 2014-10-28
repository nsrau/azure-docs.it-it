<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java website" pageTitle="Add an application to your Java website" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java website on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java website" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Aggiunta di un'applicazione a un sito Web Java in Azure

Dopo avere inizializzato il sito Web Java, come illustrato in [Introduzione all'uso di siti Web di Azure con Java][Introduzione all'uso di siti Web di Azure con Java], è possibile caricare l'applicazione inserendo il file WAR nella cartella **webapps**.

Il percorso della cartella **webapps** varia a seconda della configurazione del sito Web.

-   Se si configura il sito Web usando la raccolta di applicazioni di Azure, il percorso della cartella **webapps** sarà nel formato **d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps**, dove **application\_server** è il nome del server applicazioni usato per il sito Web.
-   Se si configura il sito Web usando l'interfaccia utente di configurazione di Azure, il percorso della cartella **webapps** sarà nel formato **d:\\home\\site\\wwwroot\\webapps**.

Si noti che è possibile usare il controllo del codice sorgente per caricare l'applicazione o le pagine Web, anche negli scenari di integrazione continuata. Per istruzioni relative all'uso del controllo del codice sorgente nel proprio sito Web, vedere [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][Pubblicazione da controllo del codice sorgente in Siti Web di Azure]. Per caricare l'applicazione o le pagine Web è anche possibile usare FTP.

Nota per i siti Web Tomcat: Dopo avere caricato il file WAR nella cartella **webapps**, il server applicazioni Tomcat ne rileverà l'aggiunta e lo caricherà automaticamente. Si noti che se vengono copiati file (eccetto i file WAR) nella directory ROOT, è necessario riavviare il server applicazioni per usarli. La funzionalità di caricamento automatico per i siti Web Java Tomcat in esecuzione in Azure si basa su un nuovo file WAR aggiunto o su nuovi file o nuove directory create nella cartella **webapps**.

  [Introduzione all'uso di siti Web di Azure con Java]: ../web-sites-java-get-started
  [Pubblicazione da controllo del codice sorgente in Siti Web di Azure]: ../web-sites-publish-source-control
