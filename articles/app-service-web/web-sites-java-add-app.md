---
title: Distribuire l&quot;applicazione di esempio nelle app Web di Servizio app di Azure
description: "In questa esercitazione viene illustrato come aggiungere una pagina o applicazione per l&quot;istanza di Azure applicazione servizio Web App è già configurato per l&quot;utilizzo di Java."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 627930ca68a94ecc56e7ef9ac9435f4b5f3f41c7
ms.openlocfilehash: 61466be17a52f1f230207b71bb94e10f88ee075c


---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Distribuire l'applicazione di esempio nelle app Web di Servizio app di Azure
Dopo aver inizializzato l'app Web Java nel [servizio app di Azure][Azure App Service] come indicato in [Creazione di un'app Web Java nel servizio app di Azure](web-sites-java-get-started.md), è possibile caricare l'applicazione inserendo il file WAR nella cartella **webapps**.

Il percorso della cartella **webapps** varia a seconda della configurazione del sito Web.

* Se si configura il sito Web usando Azure Marketplace, il percorso della cartella **webapps** sarà nel formato **d:\home\site\wwwroot\bin\application\_server\webapps**, dove **application\_server** è il nome del server applicazioni usato per l'istanza di App Web. 
* Se si configura il sito Web usando l'interfaccia utente di configurazione di Azure, il percorso della cartella **webapps** sarà nel formato **d:\home\site\wwwroot\webapps**. 

Si noti che è possibile usare il controllo del codice sorgente per caricare l'applicazione o le pagine Web, anche negli [scenari di integrazione continua](app-service-continuous-deployment.md). È anche possibile usare l'FTP per caricare le pagine web o l'applicazione. Per altre informazioni sulla distribuzione delle applicazioni tramite FTP, vedere [Distribuire l'app nel Servizio app di Azure].

Dopo avere caricato il file WAR nella cartella **webapps** , il server applicazioni Tomcat ne rileverà l'aggiunta e lo caricherà automaticamente. Si noti che se vengono copiati file (eccetto i file WAR) nella directory ROOT, è necessario riavviare il server applicazioni per utilizzarli. La funzionalità di caricamento automatico per i siti Web Java Tomcat in esecuzione in Azure si basa su un nuovo file WAR aggiunto o su nuovi file o nuove directory create nella cartella **webapps** . 

<a name="see-also"></a>

## <a name="see-also"></a>Vedere anche
Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

[!INCLUDE [application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)]

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Distribuire l'app nel Servizio app di Azure]: ./web-sites-deploy.md



<!--HONumber=Jan17_HO2-->


